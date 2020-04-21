---
title: Przekazywanie plików w ASP.NET Core
author: rick-anderson
description: Jak używać powiązania modelu i przesyłania strumieniowego do przekazywania plików w ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/18/2020
uid: mvc/models/file-uploads
ms.openlocfilehash: e25da0b3867181a16a4636768f36c148a152dd23
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661735"
---
# <a name="upload-files-in-aspnet-core"></a>Przekazywanie plików w ASP.NET Core

Przez [Steve Smith](https://ardalis.com/) i [Rutger Storm](https://github.com/rutix)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core obsługuje przekazywanie jednego lub więcej plików przy użyciu powiązania modelu buforowanego dla mniejszych plików i niebuforowanego przesyłania strumieniowego dla większych plików.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer. Osoby atakujące mogą próbować:

* Wykonywanie [ataków typu "odmowa usługi".](/windows-hardware/drivers/ifs/denial-of-service)
* Przesyłaj wirusy lub złośliwe oprogramowanie.
* Narażaj sieci i serwery na inne sposoby.

Kroki zabezpieczeń, które zmniejszają prawdopodobieństwo pomyślnego ataku są:

* Przesyłaj pliki do dedykowanego obszaru przekazywania plików, najlepiej do dysku niesystemowego. Dedykowana lokalizacja ułatwia nakładanie ograniczeń zabezpieczeń na przesłane pliki. Wyłącz uprawnienia do wykonywania w lokalizacji przekazywania pliku.&dagger;
* **Nie** utrwalić przekazanych plików w tym samym drzewie katalogów co aplikacja.&dagger;
* Użyj bezpiecznej nazwy pliku określonej przez aplikację. Nie używaj nazwy pliku podanej przez użytkownika ani niezaufanej nazwy pliku przekazanego. &dagger; Html koduje niezaufaną nazwę pliku podczas jej wyświetlania. Na przykład rejestrowanie nazwy pliku lub wyświetlania w interfejsie użytkownika (Razor automatycznie koduje dane wyjściowe).
* Zezwalaj tylko na zatwierdzone rozszerzenia plików dla specyfikacji projektu aplikacji.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Sprawdź, czy kontrole po stronie klienta są wykonywane na serwerze. &dagger; Kontrole po stronie klienta są łatwe do obejścia.
* Sprawdź rozmiar przekazanego pliku. Ustaw maksymalny limit rozmiaru, aby zapobiec dużym przesyłaniu.&dagger;
* Jeśli pliki nie powinny być zastępowane przez przekazany plik o tej samej nazwie, przed przekazaniem pliku sprawdź nazwę pliku w bazie danych lub pamięci fizycznej.
* **Uruchom skaner wirusów/złośliwego oprogramowania na przesłanych treściach przed zapisaniem pliku.**

&dagger;Przykładowa aplikacja demonstruje podejście, które spełnia kryteria.

> [!WARNING]
> Przekazywanie złośliwego kodu do systemu jest często pierwszym krokiem do wykonania kodu, który może:
>
> * Całkowicie przejmij kontrolę nad systemem.
> * Przeciążenie systemu w wyniku awarii systemu.
> * Narażaj dane użytkownika lub systemu.
> * Stosowanie graffiti do publicznego interfejsu użytkownika.
>
> Aby uzyskać informacje na temat zmniejszania powierzchni ataku podczas akceptowania plików od użytkowników, zobacz następujące zasoby:
>
> * [Nieograniczone przekazywanie plików](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Zabezpieczenia platformy Azure: upewnij się, że podczas akceptowania plików od użytkowników są dostępne odpowiednie formanty](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Aby uzyskać więcej informacji na temat wdrażania środków zabezpieczeń, w tym przykłady z przykładowej aplikacji, zobacz sekcję [Sprawdzania poprawności.](#validation)

## <a name="storage-scenarios"></a>Scenariusze magazynowania

Typowe opcje przechowywania plików obejmują:

* baza danych

  * W przypadku przekazywania małych plików baza danych jest często szybsza niż opcje magazynu fizycznego (system plików lub udział sieciowy).
  * Baza danych jest często wygodniejsza niż opcje magazynu fizycznego, ponieważ pobieranie rekordu bazy danych dla danych użytkownika może jednocześnie dostarczać zawartość pliku (na przykład obraz awatara).
  * Baza danych jest potencjalnie tańsze niż przy użyciu usługi magazynu danych.

* Pamięć fizyczna (system plików lub udział sieciowy)

  * W przypadku przesyłania dużych plików:
    * Limity bazy danych mogą ograniczać rozmiar przekazywania.
    * Magazyn fizyczny jest często mniej ekonomiczny niż magazyn w bazie danych.
  * Magazyn fizyczny jest potencjalnie tańszy niż korzystanie z usługi przechowywania danych.
  * Proces aplikacji musi mieć uprawnienia do odczytu i zapisu w lokalizacji magazynu. **Nigdy nie udzielaj uprawnień do wykonywania.**

* Usługa przechowywania danych (na przykład [usługa Azure Blob Storage)](https://azure.microsoft.com/services/storage/blobs/)

  * Usługi zwykle oferują lepszą skalowalność i odporność w przypadku rozwiązań lokalnych, które zwykle podlegają pojedynczym punktom awarii.
  * Usługi są potencjalnie niższe koszty w scenariuszach infrastruktury magazynu dużych.

  Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie obiektu blob w magazynie obiektów za pomocą platformy .NET.](/azure/storage/blobs/storage-quickstart-blobs-dotnet)

## <a name="file-upload-scenarios"></a>Scenariusze przekazywania plików

Dwa ogólne podejścia do przekazywania plików są buforowanie i przesyłania strumieniowego.

**Buforowanie**

Cały plik jest odczytywany <xref:Microsoft.AspNetCore.Http.IFormFile>do , który jest reprezentacją języka C# pliku używanego do przetwarzania lub zapisywania pliku.

Zasoby (dysk, pamięć) używane przez przesyłane pliki zależą od liczby i rozmiaru równoczesnych przekazywania plików. Jeśli aplikacja próbuje buforować zbyt wiele przekazywania, witryna ulega awarii, gdy zabraknie miejsca na dysku lub. Jeśli rozmiar lub częstotliwość przekazywania plików jest wyczerpujące zasoby aplikacji, użyj przesyłania strumieniowego.

> [!NOTE]
> Każdy pojedynczy buforowany plik przekraczający 64 KB jest przenoszony z pamięci do pliku tymczasowego na dysku.

Buforowanie małych plików jest omówione w następujących sekcjach tego tematu:

* [Pamięć fizyczna](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [baza danych](#upload-small-files-with-buffered-model-binding-to-a-database)

**Przesyłanie strumieniowe**

Plik jest odbierany z żądania wieloczęściowego i bezpośrednio przetwarzany lub zapisywany przez aplikację. Przesyłanie strumieniowe nie poprawia znacząco wydajności. Przesyłanie strumieniowe zmniejsza zapotrzebowanie na pamięć lub miejsce na dysku podczas przekazywania plików.

Przesyłanie strumieniowe dużych plików jest omówione w sekcji [Przekaż duże pliki z przesyłanie strumieniowe.](#upload-large-files-with-streaming)

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Przekazywanie małych plików z powiązaniem modelu buforowanego do magazynu fizycznego

Aby przesłać małe pliki, użyj formularza wieloczęściowego lub skonstruować żądanie POST za pomocą języka JavaScript.

W poniższym przykładzie pokazano użycie formularza Razor Pages do przesłania pojedynczego pliku *(Pages/BufferedSingleFileUploadPhysical.cshtml* w przykładowej aplikacji):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

Poniższy przykład jest analogiczny do poprzedniego przykładu, z tą różnicą, że:

* JavaScript[(Fetch API)](https://developer.mozilla.org/docs/Web/API/Fetch_API)służy do przesyłania danych formularza.
* Nie ma sprawdzania poprawności.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Aby wykonać formularz POST w języku JavaScript dla klientów, którzy [nie obsługują interfejsu API pobierania,](https://caniuse.com/#feat=fetch)użyj jednej z następujących metod:

* Użyj polyfill pobierania (na przykład [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Użyj witryny `XMLHttpRequest`. Przykład:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Aby obsługiwać przesyłanie plików, formularze HTML muszą`enctype`określać typ kodowania ( ) programu `multipart/form-data`.

Dla `files` elementu wejściowego do obsługi przekazywania wielu plików podaj `multiple` atrybut na `<input>` element:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Dostęp do poszczególnych plików przesłanych do serwera <xref:Microsoft.AspNetCore.Http.IFormFile>można uzyskać za pośrednictwem [funkcji Powiązanie modelu](xref:mvc/models/model-binding) za pomocą programu . Przykładowa aplikacja pokazuje wiele buforowanych przekazywania plików dla scenariuszy bazy danych i magazynu fizycznego.

<a name="filename"></a>

> [!WARNING]
> **Nie** należy `FileName` używać <xref:Microsoft.AspNetCore.Http.IFormFile> właściwości innych niż do wyświetlania i rejestrowania. Podczas wyświetlania lub rejestrowania kod html koduje nazwę pliku. Osoba atakująca może podać złośliwą nazwę pliku, w tym pełne ścieżki lub ścieżki względne. Wnioski powinny:
>
> * Usuń ścieżkę z nazwy pliku dostarczonej przez użytkownika.
> * Zapisz zakodowaną w formacie HTML nazwę pliku usuniętą ze ścieżki dla interfejsu użytkownika lub rejestrowania.
> * Wygeneruj nową losową nazwę pliku do przechowywania.
>
> Poniższy kod usuwa ścieżkę z nazwy pliku:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Przedstawione do tej pory przykłady nie uwzględniają względów bezpieczeństwa. Dodatkowe informacje są dostarczane przez następujące sekcje i [przykładową aplikację:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)
>
> * [Zagadnienia dotyczące bezpieczeństwa](#security-considerations)
> * [Walidacja](#validation)

Podczas przesyłania plików przy <xref:Microsoft.AspNetCore.Http.IFormFile>użyciu powiązania modelu i , metoda akcji może zaakceptować:

* Pojedynczy <xref:Microsoft.AspNetCore.Http.IFormFile>.
* Dowolna z następujących kolekcji, które reprezentują kilka plików:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Listy](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Powiązanie dopasowuje pliki formularza według nazwy. Na przykład wartość `name` HTML `<input type="file" name="formFile">` w musi odpowiadać c#`FormFile`parametr/właściwość związana ( ). Aby uzyskać więcej informacji, zobacz [Match name valuee do nazwy parametru](#match-name-attribute-value-to-parameter-name-of-post-method) sekcji metody POST.

Poniższy przykład:

* Pętle za pośrednictwem jednego lub więcej przesłanych plików.
* Używa [path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) do zwrócenia pełnej ścieżki dla pliku, w tym nazwy pliku. 
* Zapisuje pliki w lokalnym systemie plików przy użyciu nazwy pliku wygenerowanej przez aplikację.
* Zwraca całkowitą liczbę i rozmiar przekazanych plików.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Służy `Path.GetRandomFileName` do generowania nazwy pliku bez ścieżki. W poniższym przykładzie ścieżka jest uzyskiwana z konfiguracji:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Ścieżka przekazana do <xref:System.IO.FileStream> *musi* zawierać nazwę pliku. Jeśli nazwa pliku nie jest <xref:System.UnauthorizedAccessException> podana, jest generowany w czasie wykonywania.

Pliki przekazywane przy <xref:Microsoft.AspNetCore.Http.IFormFile> użyciu tej techniki są buforowane w pamięci lub na dysku na serwerze przed przetworzeniem. Wewnątrz metody akcji <xref:Microsoft.AspNetCore.Http.IFormFile> zawartość jest dostępna <xref:System.IO.Stream>jako plik . Oprócz lokalnego systemu plików pliki można zapisywać w udziale sieciowym lub w usłudze przechowywania plików, takiej jak [magazyn obiektów Blob platformy Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Inny przykład, który pętli na wiele plików do przekazywania i używa bezpiecznych nazw plików, zobacz *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* w przykładowej aplikacji.

> [!WARNING]
> [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) <xref:System.IO.IOException> zgłasza, jeśli więcej niż 65,535 pliki są tworzone bez usuwania poprzednich plików tymczasowych. Limit 65 535 plików jest limitem dla serwera. Aby uzyskać więcej informacji na temat tego limitu w systemie operacyjnym Windows, zobacz uwagi w następujących tematach:
>
> * [GetTempFileNameA, funkcja](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Przekazywanie małych plików z powiązaniem modelu buforowanego do bazy danych

Aby przechowywać dane pliku binarnego w <xref:System.Byte> bazie danych przy użyciu programu Entity [Framework,](/ef/core/index)należy zdefiniować właściwość tablicy w jednostce:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Określ właściwość modelu strony dla <xref:Microsoft.AspNetCore.Http.IFormFile>klasy, która zawiera:

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile>może służyć bezpośrednio jako parametr metody akcji lub jako właściwość modelu powiązanego. W poprzednim przykładzie używa właściwości modelu powiązanego.

Jest `FileUpload` używany w formularzu Strony Razor:

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Gdy formularz jest posted do serwera, <xref:Microsoft.AspNetCore.Http.IFormFile> skopiować do strumienia i zapisać go jako tablicy bajtów w bazie danych. W poniższym `_dbContext` przykładzie przechowuje kontekst bazy danych aplikacji:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

W poprzednim przykładzie jest podobny do scenariusza zademonstrowanego w przykładowej aplikacji:

* *Strony/BufferedSingleFileUploadDb.cshtml*
* *Strony/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> Należy zachować ostrożność podczas przechowywania danych binarnych w relacyjnych baz danych, ponieważ może to niekorzystnie wpłynąć na wydajność.
>
> Nie polegaj na właściwości `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> bez sprawdzania poprawności ani nie ufaj jej. Właściwość `FileName` powinna być używana tylko do celów wyświetlania i tylko po kodowaniu HTML.
>
> Podane przykłady nie uwzględniają kwestii bezpieczeństwa. Dodatkowe informacje są dostarczane przez następujące sekcje i [przykładową aplikację:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)
>
> * [Zagadnienia dotyczące bezpieczeństwa](#security-considerations)
> * [Walidacja](#validation)

### <a name="upload-large-files-with-streaming"></a>Przesyłanie dużych plików za pomocą przesyłania strumieniowego

W poniższym przykładzie pokazano, jak używać języka JavaScript do przesyłania strumieniowego pliku do akcji kontrolera. Token antyforgery pliku jest generowany przy użyciu atrybutu filtru niestandardowego i przekazywany do nagłówków HTTP klienta zamiast w treści żądania. Ponieważ metoda akcji przetwarza przekazane dane bezpośrednio, powiązanie modelu formularza jest wyłączone przez inny filtr niestandardowy. W ramach akcji zawartość formularza jest odczytywana za pomocą `MultipartReader`, który odczytuje każdą osobę, `MultipartSection`przetwarzania pliku lub przechowywania zawartości w stosownych przypadkach. Po odczytaniu sekcji wieloczęściowych akcja wykonuje własne powiązanie modelu.

Początkowa odpowiedź strony wczytuje formularz i zapisuje token antyforgery w pliku cookie (za pomocą atrybutu). `GenerateAntiforgeryTokenCookieAttribute` Atrybut używa wbudowanej [obsługi antyforgery](xref:security/anti-request-forgery) ASP.NET Core do ustawiania pliku cookie z tokenem żądania:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Jest `DisableFormValueModelBindingAttribute` używany do wyłączania powiązania modelu:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

W przykładowej `GenerateAntiforgeryTokenCookieAttribute` aplikacji `DisableFormValueModelBindingAttribute` i są stosowane jako filtry `/StreamedSingleFileUploadDb` do `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` modeli aplikacji strony i przy użyciu [konwencji Razor Pages:](xref:razor-pages/razor-pages-conventions)

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

Ponieważ powiązanie modelu nie odczytuje formularza, parametry, które są powiązane z formularza, nie wiążą się (kwerenda, trasa i nagłówek nadal działają). Metoda akcji działa bezpośrednio `Request` z właściwością. A `MultipartReader` służy do odczytywania każdej sekcji. Dane klucza/wartości są `KeyValueAccumulator`przechowywane w pliku . Po odczytywaniu sekcji wieloczęściowych `KeyValueAccumulator` zawartość są używane do powiązania danych formularza z typem modelu.

Pełna `StreamingController.UploadDatabase` metoda przesyłania strumieniowego do bazy danych za pomocą ef core:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Narzędzia/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Pełna `StreamingController.UploadPhysical` metoda przesyłania strumieniowego do fizycznej lokalizacji:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

W przykładowej aplikacji sprawdzanie poprawności `FileHelpers.ProcessStreamedFile`jest obsługiwane przez program .

## <a name="validation"></a>Walidacja

`FileHelpers` Klasa przykładowej aplikacji pokazuje kilka kontroli dla <xref:Microsoft.AspNetCore.Http.IFormFile> plików buforowanych i przesyłanych strumieniowo. Aby <xref:Microsoft.AspNetCore.Http.IFormFile> uzyskać przetwarzanie buforowanych przekazywania plików `ProcessFormFile` w przykładowej aplikacji, zobacz metodę w pliku *Utilities/FileHelpers.cs.* Aby przetworzyć pliki `ProcessStreamedFile` przesyłane strumieniowo, zobacz metodę w tym samym pliku.

> [!WARNING]
> Metody przetwarzania sprawdzania poprawności zademonstrowane w przykładowej aplikacji nie skanują zawartości przekazanych plików. W większości scenariuszy produkcyjnych interfejs API skanera wirusów/złośliwego oprogramowania jest używany w pliku przed udostępnieniem pliku użytkownikom lub innym systemom.
>
> Chociaż przykład tematu zawiera roboczy przykład technik sprawdzania `FileHelpers` poprawności, nie implementuj klasy w aplikacji produkcyjnej, chyba że:
>
> * W pełni zrozumieć implementację.
> * Zmodyfikuj implementację odpowiednio dla środowiska i specyfikacji aplikacji.
>
> **Nigdy nie należy bez krytycznie implementować kodu zabezpieczeń w aplikacji bez spełnienia tych wymagań.**

### <a name="content-validation"></a>Sprawdzanie poprawności zawartości

**Użyj interfejsu API skanowania wirusów/złośliwego oprogramowania innej firmy w przesłanych treściach.**

Skanowanie plików jest wymagające w zasobach serwera w scenariuszach o dużej objętości. Jeśli wydajność przetwarzania żądań jest zmniejszona z powodu skanowania plików, należy rozważyć odciążenie pracy skanowania do [usługi w tle,](xref:fundamentals/host/hosted-services)ewentualnie usługi uruchomionej na serwerze innym niż serwer aplikacji. Zazwyczaj przekazywane pliki są przechowywane w obszarze poddanym kwarantannie, dopóki skaner antywirusowy w tle nie sprawdzi ich. Po przejściu pliku plik jest przenoszony do normalnej lokalizacji przechowywania plików. Te kroki są zwykle wykonywane w połączeniu z rekordem bazy danych, który wskazuje stan skanowania pliku. Przy użyciu takiego podejścia, aplikacji i serwera aplikacji pozostają skoncentrowane na odpowiadaniu na żądania.

### <a name="file-extension-validation"></a>Sprawdzanie poprawności rozszerzenia pliku

Rozszerzenie przekazanego pliku należy sprawdzić na liście dozwolonych rozszerzeń. Przykład:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Sprawdzanie poprawności podpisu pliku

Podpis pliku jest określany przez kilka pierwszych bajtów na początku pliku. Bajty te mogą służyć do wskazania, czy rozszerzenie pasuje do zawartości pliku. Przykładowa aplikacja sprawdza podpisy plików dla kilku typowych typów plików. W poniższym przykładzie podpis pliku dla obrazu JPEG jest sprawdzany względem pliku:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Aby uzyskać dodatkowe podpisy plików, zobacz [baza danych podpisów plików](https://www.filesignatures.net/) i oficjalne specyfikacje plików.

### <a name="file-name-security"></a>Zabezpieczenia nazwy pliku

Nigdy nie używaj nazwy pliku dostarczonego przez klienta do zapisywania pliku w magazynie fizycznym. Utwórz bezpieczną nazwę pliku przy użyciu [path.getrandomFileName](xref:System.IO.Path.GetRandomFileName*) lub [Path.GetTempFileName,](xref:System.IO.Path.GetTempFileName*) aby utworzyć pełną ścieżkę (w tym nazwę pliku) do tymczasowego przechowywania.

Razor automatycznie koduje wartości właściwości do wyświetlenia. Poniższy kod jest bezpieczny w użyciu:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Poza Razor, <xref:System.Net.WebUtility.HtmlEncode*> zawsze plik zawartości nazwy z żądania użytkownika.

Wiele implementacji musi zawierać sprawdzenie, czy plik istnieje; w przeciwnym razie plik jest zastępowany plikiem o tej samej nazwie. Podaj dodatkową logikę, aby spełnić wymagania aplikacji.

### <a name="size-validation"></a>Sprawdzanie poprawności rozmiaru

Ogranicz rozmiar przesłanych plików.

W przykładowej aplikacji rozmiar pliku jest ograniczony do 2 MB (wskazany w bajtach). Limit jest dostarczany za pośrednictwem [konfiguracji](xref:fundamentals/configuration/index) z pliku *appsettings.json:*

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit` Wstrzykuje `PageModel` się do klas:

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Gdy rozmiar pliku przekracza limit, plik jest odrzucany:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Dopasuj wartość atrybutu nazwa do nazwy parametru metody POST

W formularzach innych niż Razor, które publikują dane formularza lub używają javascript bezpośrednio, nazwa określona `FormData` w elemencie formularza lub `FormData` musi być zgodna z nazwą parametru w akcji kontrolera.

W poniższym przykładzie:

* Podczas korzystania `<input>` z `name` elementu, atrybut jest `battlePlans`ustawiony na wartość:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Podczas `FormData` korzystania w javascript, nazwa jest `battlePlans`ustawiona na wartość:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Użyj pasującej nazwy parametru metody`battlePlans`C# ( ):

* Dla metody obsługi stron Razor o nazwie: `Upload`

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Dla metody akcji kontrolera MVC POST:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Konfiguracja serwera i aplikacji

### <a name="multipart-body-length-limit"></a>Granica długości obiektu wieloczęściowego

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>ustawia limit długości każdego obiektu wieloczęściowego. Sekcje formularza, które przekraczają <xref:System.IO.InvalidDataException> ten limit, są rzutowane podczas analizowania. Wartość domyślna to 134 217 728 (128 MB). Dostosuj limit za <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pomocą `Startup.ConfigureServices`ustawienia w :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>służy do ustawiania <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> dla pojedynczej strony lub akcji.

W aplikacji Razor Pages zastosuj filtr z `Startup.ConfigureServices` [konwencją](xref:razor-pages/razor-pages-conventions) w:

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    });
```

W aplikacji Razor Pages lub aplikacji MVC zastosuj filtr do modelu strony lub metody akcji:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Rozmiar ciała pustułka maksymalnego żądania

W przypadku aplikacji obsługiwanych przez Kestrel domyślny maksymalny rozmiar treści żądania wynosi 30 000 000 bajtów, czyli około 28,6 MB. Dostosuj limit za pomocą opcji serwera [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>służy do ustawiania [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) dla pojedynczej strony lub akcji.

W aplikacji Razor Pages zastosuj filtr z `Startup.ConfigureServices` [konwencją](xref:razor-pages/razor-pages-conventions) w:

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    });
```

W aplikacji stron Razor lub aplikacji MVC zastosuj filtr do klasy obsługi strony lub metody akcji:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

Może `RequestSizeLimitAttribute` być również stosowany [`@attribute`](xref:mvc/views/razor#attribute) przy użyciu dyrektywy Razor:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Inne limity pustułki

Inne limity Kestrel mogą mieć zastosowanie do aplikacji obsługiwanych przez Kestrel:

* [Maksymalna liczba połączeń klientów](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Wskaźniki danych żądań i odpowiedzi](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>Limit długości zawartości w uiścić w uiścić zawartość

Domyślny limit`maxAllowedContentLength`żądań ( ) wynosi 30 000 000 bajtów, czyli około 28,6 MB. Dostosuj limit w pliku *web.config:*

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

To ustawienie dotyczy tylko iIS. Zachowanie nie występuje domyślnie podczas hostingu na Kestrel. Aby uzyskać więcej informacji, zobacz [Żądanie limitów \<RequestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Ograniczenia w ASP.NET modułu rdzenia lub obecność modułu filtrowania żądań IIS mogą ograniczać przesyłanie do 2 lub 4 GB. Aby uzyskać więcej informacji, zobacz [Nie można przekazać pliku o rozmiarze większym niż 2 GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Poniżej znajduje się kilka typowych problemów napotkanych podczas pracy z przesyłaniem plików i ich możliwych rozwiązań.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Nie znaleziono błędu podczas wdrażania na serwerze usług IIS

Następujący błąd wskazuje, że przekazany plik przekracza skonfigurowany numer zawartości serwera:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Aby uzyskać więcej informacji na temat zwiększania limitu, zobacz sekcję [limit długości zawartości usługi IIS.](#iis-content-length-limit)

### <a name="connection-failure"></a>Błąd połączenia

Błąd połączenia i połączenie z serwerem resetowania prawdopodobnie wskazują, że przekazany plik przekracza maksymalny rozmiar treści żądania Kestrel. Aby uzyskać więcej informacji, zobacz [Kestrel maksymalny rozmiar treści żądania](#kestrel-maximum-request-body-size) sekcji. Limity połączeń klienta pustułki mogą również wymagać regulacji.

### <a name="null-reference-exception-with-iformfile"></a>Wyjątek odwołania zerowego z plikiem IFormFile

Jeśli kontroler akceptuje przesłane pliki <xref:Microsoft.AspNetCore.Http.IFormFile> przy użyciu, ale wartość jest `null`, `enctype` upewnij `multipart/form-data`się, że formularz HTML określa wartość . Jeśli ten atrybut nie jest `<form>` ustawiony na elemencie, przekazywanie <xref:Microsoft.AspNetCore.Http.IFormFile> pliku `null`nie występuje i wszystkie powiązane argumenty są . Upewnij się również, że [nazewnictwo przesyłania w danych formularza jest zgodne z nazewnictwem aplikacji](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Strumień był za długi

Przykłady w tym temacie <xref:System.IO.MemoryStream> polegać na przechowywać zawartość przekazanego pliku. Limit rozmiaru a `MemoryStream` `int.MaxValue`to . Jeśli scenariusz przekazywania plików aplikacji wymaga przechowywania zawartości pliku większej niż 50 MB, użyj `MemoryStream` alternatywnego podejścia, które nie polega na jednym do przechowywania zawartości przekazanego pliku.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core obsługuje przekazywanie jednego lub więcej plików przy użyciu powiązania modelu buforowanego dla mniejszych plików i niebuforowanego przesyłania strumieniowego dla większych plików.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer. Osoby atakujące mogą próbować:

* Wykonywanie [ataków typu "odmowa usługi".](/windows-hardware/drivers/ifs/denial-of-service)
* Przesyłaj wirusy lub złośliwe oprogramowanie.
* Narażaj sieci i serwery na inne sposoby.

Kroki zabezpieczeń, które zmniejszają prawdopodobieństwo pomyślnego ataku są:

* Przesyłaj pliki do dedykowanego obszaru przekazywania plików, najlepiej do dysku niesystemowego. Dedykowana lokalizacja ułatwia nakładanie ograniczeń zabezpieczeń na przesłane pliki. Wyłącz uprawnienia do wykonywania w lokalizacji przekazywania pliku.&dagger;
* **Nie** utrwalić przekazanych plików w tym samym drzewie katalogów co aplikacja.&dagger;
* Użyj bezpiecznej nazwy pliku określonej przez aplikację. Nie używaj nazwy pliku podanej przez użytkownika ani niezaufanej nazwy pliku przekazanego. &dagger; Html koduje niezaufaną nazwę pliku podczas jej wyświetlania. Na przykład rejestrowanie nazwy pliku lub wyświetlania w interfejsie użytkownika (Razor automatycznie koduje dane wyjściowe).
* Zezwalaj tylko na zatwierdzone rozszerzenia plików dla specyfikacji projektu aplikacji.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Sprawdź, czy kontrole po stronie klienta są wykonywane na serwerze. &dagger; Kontrole po stronie klienta są łatwe do obejścia.
* Sprawdź rozmiar przekazanego pliku. Ustaw maksymalny limit rozmiaru, aby zapobiec dużym przesyłaniu.&dagger;
* Jeśli pliki nie powinny być zastępowane przez przekazany plik o tej samej nazwie, przed przekazaniem pliku sprawdź nazwę pliku w bazie danych lub pamięci fizycznej.
* **Uruchom skaner wirusów/złośliwego oprogramowania na przesłanych treściach przed zapisaniem pliku.**

&dagger;Przykładowa aplikacja demonstruje podejście, które spełnia kryteria.

> [!WARNING]
> Przekazywanie złośliwego kodu do systemu jest często pierwszym krokiem do wykonania kodu, który może:
>
> * Całkowicie przejmij kontrolę nad systemem.
> * Przeciążenie systemu w wyniku awarii systemu.
> * Narażaj dane użytkownika lub systemu.
> * Stosowanie graffiti do publicznego interfejsu użytkownika.
>
> Aby uzyskać informacje na temat zmniejszania powierzchni ataku podczas akceptowania plików od użytkowników, zobacz następujące zasoby:
>
> * [Nieograniczone przekazywanie plików](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Zabezpieczenia platformy Azure: upewnij się, że podczas akceptowania plików od użytkowników są dostępne odpowiednie formanty](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Aby uzyskać więcej informacji na temat wdrażania środków zabezpieczeń, w tym przykłady z przykładowej aplikacji, zobacz sekcję [Sprawdzania poprawności.](#validation)

## <a name="storage-scenarios"></a>Scenariusze magazynowania

Typowe opcje przechowywania plików obejmują:

* baza danych

  * W przypadku przekazywania małych plików baza danych jest często szybsza niż opcje magazynu fizycznego (system plików lub udział sieciowy).
  * Baza danych jest często wygodniejsza niż opcje magazynu fizycznego, ponieważ pobieranie rekordu bazy danych dla danych użytkownika może jednocześnie dostarczać zawartość pliku (na przykład obraz awatara).
  * Baza danych jest potencjalnie tańsze niż przy użyciu usługi magazynu danych.

* Pamięć fizyczna (system plików lub udział sieciowy)

  * W przypadku przesyłania dużych plików:
    * Limity bazy danych mogą ograniczać rozmiar przekazywania.
    * Magazyn fizyczny jest często mniej ekonomiczny niż magazyn w bazie danych.
  * Magazyn fizyczny jest potencjalnie tańszy niż korzystanie z usługi przechowywania danych.
  * Proces aplikacji musi mieć uprawnienia do odczytu i zapisu w lokalizacji magazynu. **Nigdy nie udzielaj uprawnień do wykonywania.**

* Usługa przechowywania danych (na przykład [usługa Azure Blob Storage)](https://azure.microsoft.com/services/storage/blobs/)

  * Usługi zwykle oferują lepszą skalowalność i odporność w przypadku rozwiązań lokalnych, które zwykle podlegają pojedynczym punktom awarii.
  * Usługi są potencjalnie niższe koszty w scenariuszach infrastruktury magazynu dużych.

  Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie obiektu blob w magazynie obiektów za pomocą platformy .NET.](/azure/storage/blobs/storage-quickstart-blobs-dotnet) W temacie <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>przedstawiono <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> , ale może <xref:System.IO.FileStream> służyć do zapisywania <xref:System.IO.Stream>do magazynu obiektów blob podczas pracy z .

## <a name="file-upload-scenarios"></a>Scenariusze przekazywania plików

Dwa ogólne podejścia do przekazywania plików są buforowanie i przesyłania strumieniowego.

**Buforowanie**

Cały plik jest odczytywany <xref:Microsoft.AspNetCore.Http.IFormFile>do , który jest reprezentacją języka C# pliku używanego do przetwarzania lub zapisywania pliku.

Zasoby (dysk, pamięć) używane przez przesyłane pliki zależą od liczby i rozmiaru równoczesnych przekazywania plików. Jeśli aplikacja próbuje buforować zbyt wiele przekazywania, witryna ulega awarii, gdy zabraknie miejsca na dysku lub. Jeśli rozmiar lub częstotliwość przekazywania plików jest wyczerpujące zasoby aplikacji, użyj przesyłania strumieniowego.

> [!NOTE]
> Każdy pojedynczy buforowany plik przekraczający 64 KB jest przenoszony z pamięci do pliku tymczasowego na dysku.

Buforowanie małych plików jest omówione w następujących sekcjach tego tematu:

* [Pamięć fizyczna](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [baza danych](#upload-small-files-with-buffered-model-binding-to-a-database)

**Przesyłanie strumieniowe**

Plik jest odbierany z żądania wieloczęściowego i bezpośrednio przetwarzany lub zapisywany przez aplikację. Przesyłanie strumieniowe nie poprawia znacząco wydajności. Przesyłanie strumieniowe zmniejsza zapotrzebowanie na pamięć lub miejsce na dysku podczas przekazywania plików.

Przesyłanie strumieniowe dużych plików jest omówione w sekcji [Przekaż duże pliki z przesyłanie strumieniowe.](#upload-large-files-with-streaming)

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Przekazywanie małych plików z powiązaniem modelu buforowanego do magazynu fizycznego

Aby przesłać małe pliki, użyj formularza wieloczęściowego lub skonstruować żądanie POST za pomocą języka JavaScript.

W poniższym przykładzie pokazano użycie formularza Razor Pages do przesłania pojedynczego pliku *(Pages/BufferedSingleFileUploadPhysical.cshtml* w przykładowej aplikacji):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

Poniższy przykład jest analogiczny do poprzedniego przykładu, z tą różnicą, że:

* JavaScript[(Fetch API)](https://developer.mozilla.org/docs/Web/API/Fetch_API)służy do przesyłania danych formularza.
* Nie ma sprawdzania poprawności.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Aby wykonać formularz POST w języku JavaScript dla klientów, którzy [nie obsługują interfejsu API pobierania,](https://caniuse.com/#feat=fetch)użyj jednej z następujących metod:

* Użyj polyfill pobierania (na przykład [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Użyj witryny `XMLHttpRequest`. Przykład:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

Aby obsługiwać przesyłanie plików, formularze HTML muszą`enctype`określać typ kodowania ( ) programu `multipart/form-data`.

Dla `files` elementu wejściowego do obsługi przekazywania wielu plików podaj `multiple` atrybut na `<input>` element:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Dostęp do poszczególnych plików przesłanych do serwera <xref:Microsoft.AspNetCore.Http.IFormFile>można uzyskać za pośrednictwem [funkcji Powiązanie modelu](xref:mvc/models/model-binding) za pomocą programu . Przykładowa aplikacja pokazuje wiele buforowanych przekazywania plików dla scenariuszy bazy danych i magazynu fizycznego.

<a name="filename2"></a>

> [!WARNING]
> **Nie** należy `FileName` używać <xref:Microsoft.AspNetCore.Http.IFormFile> właściwości innych niż do wyświetlania i rejestrowania. Podczas wyświetlania lub rejestrowania kod html koduje nazwę pliku. Osoba atakująca może podać złośliwą nazwę pliku, w tym pełne ścieżki lub ścieżki względne. Wnioski powinny:
>
> * Usuń ścieżkę z nazwy pliku dostarczonej przez użytkownika.
> * Zapisz zakodowaną w formacie HTML nazwę pliku usuniętą ze ścieżki dla interfejsu użytkownika lub rejestrowania.
> * Wygeneruj nową losową nazwę pliku do przechowywania.
>
> Poniższy kod usuwa ścieżkę z nazwy pliku:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Przedstawione do tej pory przykłady nie uwzględniają względów bezpieczeństwa. Dodatkowe informacje są dostarczane przez następujące sekcje i [przykładową aplikację:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)
>
> * [Zagadnienia dotyczące bezpieczeństwa](#security-considerations)
> * [Walidacja](#validation)

Podczas przesyłania plików przy <xref:Microsoft.AspNetCore.Http.IFormFile>użyciu powiązania modelu i , metoda akcji może zaakceptować:

* Pojedynczy <xref:Microsoft.AspNetCore.Http.IFormFile>.
* Dowolna z następujących kolekcji, które reprezentują kilka plików:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Listy](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Powiązanie dopasowuje pliki formularza według nazwy. Na przykład wartość `name` HTML `<input type="file" name="formFile">` w musi odpowiadać c#`FormFile`parametr/właściwość związana ( ). Aby uzyskać więcej informacji, zobacz [Match name valuee do nazwy parametru](#match-name-attribute-value-to-parameter-name-of-post-method) sekcji metody POST.

Poniższy przykład:

* Pętle za pośrednictwem jednego lub więcej przesłanych plików.
* Używa [path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) do zwrócenia pełnej ścieżki dla pliku, w tym nazwy pliku. 
* Zapisuje pliki w lokalnym systemie plików przy użyciu nazwy pliku wygenerowanej przez aplikację.
* Zwraca całkowitą liczbę i rozmiar przekazanych plików.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Służy `Path.GetRandomFileName` do generowania nazwy pliku bez ścieżki. W poniższym przykładzie ścieżka jest uzyskiwana z konfiguracji:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Ścieżka przekazana do <xref:System.IO.FileStream> *musi* zawierać nazwę pliku. Jeśli nazwa pliku nie jest <xref:System.UnauthorizedAccessException> podana, jest generowany w czasie wykonywania.

Pliki przekazywane przy <xref:Microsoft.AspNetCore.Http.IFormFile> użyciu tej techniki są buforowane w pamięci lub na dysku na serwerze przed przetworzeniem. Wewnątrz metody akcji <xref:Microsoft.AspNetCore.Http.IFormFile> zawartość jest dostępna <xref:System.IO.Stream>jako plik . Oprócz lokalnego systemu plików pliki można zapisywać w udziale sieciowym lub w usłudze przechowywania plików, takiej jak [magazyn obiektów Blob platformy Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Inny przykład, który pętli na wiele plików do przekazywania i używa bezpiecznych nazw plików, zobacz *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* w przykładowej aplikacji.

> [!WARNING]
> [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) <xref:System.IO.IOException> zgłasza, jeśli więcej niż 65,535 pliki są tworzone bez usuwania poprzednich plików tymczasowych. Limit 65 535 plików jest limitem dla serwera. Aby uzyskać więcej informacji na temat tego limitu w systemie operacyjnym Windows, zobacz uwagi w następujących tematach:
>
> * [GetTempFileNameA, funkcja](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Przekazywanie małych plików z powiązaniem modelu buforowanego do bazy danych

Aby przechowywać dane pliku binarnego w <xref:System.Byte> bazie danych przy użyciu programu Entity [Framework,](/ef/core/index)należy zdefiniować właściwość tablicy w jednostce:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Określ właściwość modelu strony dla <xref:Microsoft.AspNetCore.Http.IFormFile>klasy, która zawiera:

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile>może służyć bezpośrednio jako parametr metody akcji lub jako właściwość modelu powiązanego. W poprzednim przykładzie używa właściwości modelu powiązanego.

Jest `FileUpload` używany w formularzu Strony Razor:

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Gdy formularz jest posted do serwera, <xref:Microsoft.AspNetCore.Http.IFormFile> skopiować do strumienia i zapisać go jako tablicy bajtów w bazie danych. W poniższym `_dbContext` przykładzie przechowuje kontekst bazy danych aplikacji:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

W poprzednim przykładzie jest podobny do scenariusza zademonstrowanego w przykładowej aplikacji:

* *Strony/BufferedSingleFileUploadDb.cshtml*
* *Strony/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> Należy zachować ostrożność podczas przechowywania danych binarnych w relacyjnych baz danych, ponieważ może to niekorzystnie wpłynąć na wydajność.
>
> Nie polegaj na właściwości `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> bez sprawdzania poprawności ani nie ufaj jej. Właściwość `FileName` powinna być używana tylko do celów wyświetlania i tylko po kodowaniu HTML.
>
> Podane przykłady nie uwzględniają kwestii bezpieczeństwa. Dodatkowe informacje są dostarczane przez następujące sekcje i [przykładową aplikację:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)
>
> * [Zagadnienia dotyczące bezpieczeństwa](#security-considerations)
> * [Walidacja](#validation)

### <a name="upload-large-files-with-streaming"></a>Przesyłanie dużych plików za pomocą przesyłania strumieniowego

W poniższym przykładzie pokazano, jak używać języka JavaScript do przesyłania strumieniowego pliku do akcji kontrolera. Token antyforgery pliku jest generowany przy użyciu atrybutu filtru niestandardowego i przekazywany do nagłówków HTTP klienta zamiast w treści żądania. Ponieważ metoda akcji przetwarza przekazane dane bezpośrednio, powiązanie modelu formularza jest wyłączone przez inny filtr niestandardowy. W ramach akcji zawartość formularza jest odczytywana za pomocą `MultipartReader`, który odczytuje każdą osobę, `MultipartSection`przetwarzania pliku lub przechowywania zawartości w stosownych przypadkach. Po odczytaniu sekcji wieloczęściowych akcja wykonuje własne powiązanie modelu.

Początkowa odpowiedź strony wczytuje formularz i zapisuje token antyforgery w pliku cookie (za pomocą atrybutu). `GenerateAntiforgeryTokenCookieAttribute` Atrybut używa wbudowanej [obsługi antyforgery](xref:security/anti-request-forgery) ASP.NET Core do ustawiania pliku cookie z tokenem żądania:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Jest `DisableFormValueModelBindingAttribute` używany do wyłączania powiązania modelu:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

W przykładowej `GenerateAntiforgeryTokenCookieAttribute` aplikacji `DisableFormValueModelBindingAttribute` i są stosowane jako filtry `/StreamedSingleFileUploadDb` do `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` modeli aplikacji strony i przy użyciu [konwencji Razor Pages:](xref:razor-pages/razor-pages-conventions)

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

Ponieważ powiązanie modelu nie odczytuje formularza, parametry, które są powiązane z formularza, nie wiążą się (kwerenda, trasa i nagłówek nadal działają). Metoda akcji działa bezpośrednio `Request` z właściwością. A `MultipartReader` służy do odczytywania każdej sekcji. Dane klucza/wartości są `KeyValueAccumulator`przechowywane w pliku . Po odczytywaniu sekcji wieloczęściowych `KeyValueAccumulator` zawartość są używane do powiązania danych formularza z typem modelu.

Pełna `StreamingController.UploadDatabase` metoda przesyłania strumieniowego do bazy danych za pomocą ef core:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper`(*Narzędzia/MultipartRequestHelper.cs*):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Pełna `StreamingController.UploadPhysical` metoda przesyłania strumieniowego do fizycznej lokalizacji:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

W przykładowej aplikacji sprawdzanie poprawności `FileHelpers.ProcessStreamedFile`jest obsługiwane przez program .

## <a name="validation"></a>Walidacja

`FileHelpers` Klasa przykładowej aplikacji pokazuje kilka kontroli dla <xref:Microsoft.AspNetCore.Http.IFormFile> plików buforowanych i przesyłanych strumieniowo. Aby <xref:Microsoft.AspNetCore.Http.IFormFile> uzyskać przetwarzanie buforowanych przekazywania plików `ProcessFormFile` w przykładowej aplikacji, zobacz metodę w pliku *Utilities/FileHelpers.cs.* Aby przetworzyć pliki `ProcessStreamedFile` przesyłane strumieniowo, zobacz metodę w tym samym pliku.

> [!WARNING]
> Metody przetwarzania sprawdzania poprawności zademonstrowane w przykładowej aplikacji nie skanują zawartości przekazanych plików. W większości scenariuszy produkcyjnych interfejs API skanera wirusów/złośliwego oprogramowania jest używany w pliku przed udostępnieniem pliku użytkownikom lub innym systemom.
>
> Chociaż przykład tematu zawiera roboczy przykład technik sprawdzania `FileHelpers` poprawności, nie implementuj klasy w aplikacji produkcyjnej, chyba że:
>
> * W pełni zrozumieć implementację.
> * Zmodyfikuj implementację odpowiednio dla środowiska i specyfikacji aplikacji.
>
> **Nigdy nie należy bez krytycznie implementować kodu zabezpieczeń w aplikacji bez spełnienia tych wymagań.**

### <a name="content-validation"></a>Sprawdzanie poprawności zawartości

**Użyj interfejsu API skanowania wirusów/złośliwego oprogramowania innej firmy w przesłanych treściach.**

Skanowanie plików jest wymagające w zasobach serwera w scenariuszach o dużej objętości. Jeśli wydajność przetwarzania żądań jest zmniejszona z powodu skanowania plików, należy rozważyć odciążenie pracy skanowania do [usługi w tle,](xref:fundamentals/host/hosted-services)ewentualnie usługi uruchomionej na serwerze innym niż serwer aplikacji. Zazwyczaj przekazywane pliki są przechowywane w obszarze poddanym kwarantannie, dopóki skaner antywirusowy w tle nie sprawdzi ich. Po przejściu pliku plik jest przenoszony do normalnej lokalizacji przechowywania plików. Te kroki są zwykle wykonywane w połączeniu z rekordem bazy danych, który wskazuje stan skanowania pliku. Przy użyciu takiego podejścia, aplikacji i serwera aplikacji pozostają skoncentrowane na odpowiadaniu na żądania.

### <a name="file-extension-validation"></a>Sprawdzanie poprawności rozszerzenia pliku

Rozszerzenie przekazanego pliku należy sprawdzić na liście dozwolonych rozszerzeń. Przykład:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Sprawdzanie poprawności podpisu pliku

Podpis pliku jest określany przez kilka pierwszych bajtów na początku pliku. Bajty te mogą służyć do wskazania, czy rozszerzenie pasuje do zawartości pliku. Przykładowa aplikacja sprawdza podpisy plików dla kilku typowych typów plików. W poniższym przykładzie podpis pliku dla obrazu JPEG jest sprawdzany względem pliku:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Aby uzyskać dodatkowe podpisy plików, zobacz [baza danych podpisów plików](https://www.filesignatures.net/) i oficjalne specyfikacje plików.

### <a name="file-name-security"></a>Zabezpieczenia nazwy pliku

Nigdy nie używaj nazwy pliku dostarczonego przez klienta do zapisywania pliku w magazynie fizycznym. Utwórz bezpieczną nazwę pliku przy użyciu [path.getrandomFileName](xref:System.IO.Path.GetRandomFileName*) lub [Path.GetTempFileName,](xref:System.IO.Path.GetTempFileName*) aby utworzyć pełną ścieżkę (w tym nazwę pliku) do tymczasowego przechowywania.

Razor automatycznie koduje wartości właściwości do wyświetlenia. Poniższy kod jest bezpieczny w użyciu:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Poza Razor, <xref:System.Net.WebUtility.HtmlEncode*> zawsze plik zawartości nazwy z żądania użytkownika.

Wiele implementacji musi zawierać sprawdzenie, czy plik istnieje; w przeciwnym razie plik jest zastępowany plikiem o tej samej nazwie. Podaj dodatkową logikę, aby spełnić wymagania aplikacji.

### <a name="size-validation"></a>Sprawdzanie poprawności rozmiaru

Ogranicz rozmiar przesłanych plików.

W przykładowej aplikacji rozmiar pliku jest ograniczony do 2 MB (wskazany w bajtach). Limit jest dostarczany za pośrednictwem [konfiguracji](xref:fundamentals/configuration/index) z pliku *appsettings.json:*

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit` Wstrzykuje `PageModel` się do klas:

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Gdy rozmiar pliku przekracza limit, plik jest odrzucany:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Dopasuj wartość atrybutu nazwa do nazwy parametru metody POST

W formularzach innych niż Razor, które publikują dane formularza lub używają javascript bezpośrednio, nazwa określona `FormData` w elemencie formularza lub `FormData` musi być zgodna z nazwą parametru w akcji kontrolera.

W poniższym przykładzie:

* Podczas korzystania `<input>` z `name` elementu, atrybut jest `battlePlans`ustawiony na wartość:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Podczas `FormData` korzystania w javascript, nazwa jest `battlePlans`ustawiona na wartość:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Użyj pasującej nazwy parametru metody`battlePlans`C# ( ):

* Dla metody obsługi stron Razor o nazwie: `Upload`

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Dla metody akcji kontrolera MVC POST:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Konfiguracja serwera i aplikacji

### <a name="multipart-body-length-limit"></a>Granica długości obiektu wieloczęściowego

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>ustawia limit długości każdego obiektu wieloczęściowego. Sekcje formularza, które przekraczają <xref:System.IO.InvalidDataException> ten limit, są rzutowane podczas analizowania. Wartość domyślna to 134 217 728 (128 MB). Dostosuj limit za <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> pomocą `Startup.ConfigureServices`ustawienia w :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>służy do ustawiania <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> dla pojedynczej strony lub akcji.

W aplikacji Razor Pages zastosuj filtr z `Startup.ConfigureServices` [konwencją](xref:razor-pages/razor-pages-conventions) w:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

W aplikacji Razor Pages lub aplikacji MVC zastosuj filtr do modelu strony lub metody akcji:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Rozmiar ciała pustułka maksymalnego żądania

W przypadku aplikacji obsługiwanych przez Kestrel domyślny maksymalny rozmiar treści żądania wynosi 30 000 000 bajtów, czyli około 28,6 MB. Dostosuj limit za pomocą opcji serwera [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>służy do ustawiania [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) dla pojedynczej strony lub akcji.

W aplikacji Razor Pages zastosuj filtr z `Startup.ConfigureServices` [konwencją](xref:razor-pages/razor-pages-conventions) w:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

W aplikacji stron Razor lub aplikacji MVC zastosuj filtr do klasy obsługi strony lub metody akcji:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>Inne limity pustułki

Inne limity Kestrel mogą mieć zastosowanie do aplikacji obsługiwanych przez Kestrel:

* [Maksymalna liczba połączeń klientów](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Wskaźniki danych żądań i odpowiedzi](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a>Limit długości zawartości w uiścić w uiścić zawartość

Domyślny limit`maxAllowedContentLength`żądań ( ) wynosi 30 000 000 bajtów, czyli około 28,6 MB. Dostosuj limit w pliku *web.config:*

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

To ustawienie dotyczy tylko iIS. Zachowanie nie występuje domyślnie podczas hostingu na Kestrel. Aby uzyskać więcej informacji, zobacz [Żądanie limitów \<RequestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Ograniczenia w ASP.NET modułu rdzenia lub obecność modułu filtrowania żądań IIS mogą ograniczać przesyłanie do 2 lub 4 GB. Aby uzyskać więcej informacji, zobacz [Nie można przekazać pliku o rozmiarze większym niż 2 GB (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Poniżej znajduje się kilka typowych problemów napotkanych podczas pracy z przesyłaniem plików i ich możliwych rozwiązań.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Nie znaleziono błędu podczas wdrażania na serwerze usług IIS

Następujący błąd wskazuje, że przekazany plik przekracza skonfigurowany numer zawartości serwera:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Aby uzyskać więcej informacji na temat zwiększania limitu, zobacz sekcję [limit długości zawartości usługi IIS.](#iis-content-length-limit)

### <a name="connection-failure"></a>Błąd połączenia

Błąd połączenia i połączenie z serwerem resetowania prawdopodobnie wskazują, że przekazany plik przekracza maksymalny rozmiar treści żądania Kestrel. Aby uzyskać więcej informacji, zobacz [Kestrel maksymalny rozmiar treści żądania](#kestrel-maximum-request-body-size) sekcji. Limity połączeń klienta pustułki mogą również wymagać regulacji.

### <a name="null-reference-exception-with-iformfile"></a>Wyjątek odwołania zerowego z plikiem IFormFile

Jeśli kontroler akceptuje przesłane pliki <xref:Microsoft.AspNetCore.Http.IFormFile> przy użyciu, ale wartość jest `null`, `enctype` upewnij `multipart/form-data`się, że formularz HTML określa wartość . Jeśli ten atrybut nie jest `<form>` ustawiony na elemencie, przekazywanie <xref:Microsoft.AspNetCore.Http.IFormFile> pliku `null`nie występuje i wszystkie powiązane argumenty są . Upewnij się również, że [nazewnictwo przesyłania w danych formularza jest zgodne z nazewnictwem aplikacji](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Strumień był za długi

Przykłady w tym temacie <xref:System.IO.MemoryStream> polegać na przechowywać zawartość przekazanego pliku. Limit rozmiaru a `MemoryStream` `int.MaxValue`to . Jeśli scenariusz przekazywania plików aplikacji wymaga przechowywania zawartości pliku większej niż 50 MB, użyj `MemoryStream` alternatywnego podejścia, które nie polega na jednym do przechowywania zawartości przekazanego pliku.

::: moniker-end


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Nieograniczone przekazywanie plików](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [Zabezpieczenia platformy Azure: ramka zabezpieczeń: sprawdzanie poprawności danych wejściowych | Czynniki](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Wzorce projektowania chmury azure: wzorzec klucza usługi valet](/azure/architecture/patterns/valet-key)
