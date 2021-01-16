---
title: Przekaż pliki w ASP.NET Core
author: rick-anderson
description: Jak używać powiązania modelu i przesyłania strumieniowego do przekazywania plików w ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/file-uploads
ms.openlocfilehash: c32d20d4616650db004c78fb4d8ea9a4d5a3beab
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252802"
---
# <a name="upload-files-in-aspnet-core"></a>Przekaż pliki w ASP.NET Core

Przez [Steve Kowalski](https://ardalis.com/) i [Rutger burzy](https://github.com/rutix)

::: moniker range=">= aspnetcore-5.0"

ASP.NET Core obsługuje przekazywanie co najmniej jednego pliku przy użyciu powiązania z buforowanym modelem dla mniejszych plików i przesyłania strumieniowego z buforem dla większych plików.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer. Osoby atakujące mogą próbować:

* Wykonywanie ataków [typu "odmowa usługi"](/windows-hardware/drivers/ifs/denial-of-service) .
* Przekazuj wirusy lub złośliwe oprogramowanie.
* Naruszyć bezpieczeństwo sieci i serwerów w inny sposób.

Kroki zabezpieczeń, które zmniejszają prawdopodobieństwo pomyślnego ataku:

* Przekaż pliki do dedykowanego obszaru przekazywania plików, najlepiej do dysku niesystemowego. Dedykowana lokalizacja ułatwia nakładanie ograniczeń zabezpieczeń na przekazane pliki. Wyłącz uprawnienia do wykonywania w lokalizacji przekazywania pliku.&dagger;
* **Nie** Utrwalaj przekazanych plików w tym samym drzewie katalogów co aplikacja.&dagger;
* Użyj bezpiecznej nazwy pliku, która jest określana przez aplikację. Nie należy używać nazwy pliku dostarczonej przez użytkownika lub niezaufanej nazwy pliku przekazanego pliku. &dagger; KOD HTML zakodowania niezaufanej nazwy pliku podczas jego wyświetlania. Na przykład rejestrowanie nazwy pliku lub wyświetlanie w interfejsie użytkownika ( Razor automatyczne kodowanie HTML kodu wyjściowego).
* Zezwalaj tylko na zatwierdzone rozszerzenia plików dla specyfikacji projektu aplikacji.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Sprawdź, czy testy po stronie klienta są wykonywane na serwerze. &dagger; Sprawdzanie po stronie klienta można łatwo obejść.
* Sprawdź rozmiar przekazanego pliku. Ustaw maksymalny limit rozmiaru, aby zapobiec dużej ilości operacji przekazywania.&dagger;
* Jeśli pliki nie powinny być zastąpione przez przekazany plik o tej samej nazwie, przed przekazaniem pliku Sprawdź nazwę pliku względem bazy danych lub magazynu fizycznego.
* **Przed zapisaniem pliku Uruchom skaner wirusów/złośliwego oprogramowania dla przekazanej zawartości.**

&dagger;Przykładowa aplikacja pokazuje podejście, które spełnia kryteria.

> [!WARNING]
> Przekazywanie złośliwego kodu do systemu jest często pierwszym krokiem do wykonania kodu, który może:
>
> * Całkowicie przejąć kontrolę nad systemem.
> * Przeciąż system z wynikiem awarii systemu.
> * Naruszanie danych użytkownika lub systemu.
> * Zastosuj graffiti do publicznego interfejsu użytkownika.
>
> Aby uzyskać informacje na temat zmniejszania obszaru ataków podczas akceptowania plików od użytkowników, zobacz następujące zasoby:
>
> * [Przekazywanie plików bez ograniczeń](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Zabezpieczenia platformy Azure: Upewnij się, że podczas akceptowania plików od użytkowników są stosowane odpowiednie kontrolki](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Aby uzyskać więcej informacji na temat implementowania środków zabezpieczeń, w tym przykładów z przykładowej aplikacji, zobacz sekcję [Walidacja](#validation) .

## <a name="storage-scenarios"></a>Scenariusze magazynu

Typowe opcje magazynu dla plików to:

* baza danych

  * W przypadku małych operacji przekazywania plików baza danych jest często szybsza niż opcje magazynu fizycznego (systemu plików lub udziału sieciowego).
  * Baza danych jest często bardziej wygodna niż opcje magazynu fizycznego, ponieważ Pobieranie rekordu bazy danych dla danych użytkownika może jednocześnie dostarczyć zawartość pliku (na przykład obraz awatara).
  * Baza danych może być tańsza niż użycie usługi magazynu danych.

* Magazyn fizyczny (system plików lub udział sieciowy)

  * W przypadku dużych operacji przekazywania plików:
    * Limity bazy danych mogą ograniczać rozmiar przekazywania.
    * Magazyn fizyczny jest często mniej ekonomiczny niż magazyn w bazie danych.
  * Magazyn fizyczny jest prawdopodobnie tańszy niż użycie usługi magazynu danych.
  * Proces aplikacji musi mieć uprawnienia do odczytu i zapisu w lokalizacji magazynu. **Nigdy nie udzielaj uprawnienia EXECUTE.**

* Usługa magazynu danych (na przykład [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * Usługa zazwyczaj oferuje ulepszoną skalowalność i odporność w rozwiązaniach lokalnych, które zwykle podlegają pojedynczym punktom awarii.
  * Usługi są potencjalnie tańsze w dużych scenariuszach infrastruktury magazynu.

  Aby uzyskać więcej informacji, zobacz [Szybki Start: korzystanie z platformy .NET do tworzenia obiektów BLOB w magazynie obiektów](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Scenariusze przekazywania plików

Dwie ogólne podejścia do przekazywania plików to buforowanie i przesyłanie strumieniowe.

**Buforowania**

Cały plik jest odczytywany do <xref:Microsoft.AspNetCore.Http.IFormFile> , który jest reprezentacją języka C# pliku używanego do przetwarzania lub zapisywania pliku.

Zasoby (dysk, pamięć) używane przez operacje przekazywania plików zależą od liczby i rozmiaru współbieżnych przekazywania plików. Jeśli aplikacja próbuje buforować zbyt wiele przeciążeń, lokacja uległa awarii, gdy zabraknie pamięci lub miejsca na dysku. Jeśli rozmiar lub częstotliwość przekazywania plików powoduje wyczerpanie zasobów aplikacji, należy użyć przesyłania strumieniowego.

> [!NOTE]
> Każdy pojedynczy buforowany plik przekraczający 64 KB jest przenoszony z pamięci do pliku tymczasowego na dysku.

Buforowanie małych plików zostało omówione w następujących sekcjach tego tematu:

* [Magazyn fizyczny](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Database](#upload-small-files-with-buffered-model-binding-to-a-database) (Baza danych)

**Przesyłanie strumieniowe**

Plik jest odbierany od żądania wieloczęściowego i bezpośrednio przetwarzany lub zapisywany przez aplikację. Przesyłanie strumieniowe nie poprawia znacząco wydajności. Przesyłanie strumieniowe zmniejsza wymagania dotyczące pamięci lub miejsca na dysku podczas przekazywania plików.

Przesyłanie strumieniowe dużych plików jest omówione w sekcji [przekazywanie dużych plików z przesyłaniem strumieniowym](#upload-large-files-with-streaming) .

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Przekazywanie małych plików z buforowanym powiązaniem modelu z magazynem fizycznym

Aby przekazać małe pliki, użyj formularza wieloczęściowego lub Skonstruuj żądanie POST przy użyciu języka JavaScript.

Poniższy przykład ilustruje użycie Razor formularza stron do przekazywania pojedynczego pliku (*Pages/BufferedSingleFileUploadPhysical. cshtml* w przykładowej aplikacji):

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

Poniższy przykład jest analogiczny do poprzedniego przykładu, z wyjątkiem tego, że:

* Kod JavaScript ([interfejs API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API)) służy do przesyłania danych formularza.
* Nie istnieje weryfikacja.

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

Aby wykonać formularz POST w języku JavaScript dla klientów, którzy [nie obsługują interfejsu API pobierania](https://caniuse.com/#feat=fetch), należy użyć jednej z następujących metod:

* Użyj wypełniania pobierania (na przykład [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).
* Użyj polecenia `XMLHttpRequest`. Na przykład:

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

W celu obsługi przekazywania plików formularze HTML muszą określać typ kodowania ( `enctype` ) `multipart/form-data` .

Aby `files` element wejściowy obsługiwał przekazywanie wielu plików, podaj `multiple` atrybut w `<input>` elemencie:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Do poszczególnych plików przekazanych do serwera można uzyskać dostęp za pośrednictwem [powiązania modelu](xref:mvc/models/model-binding) przy użyciu polecenia <xref:Microsoft.AspNetCore.Http.IFormFile> . Przykładowa aplikacja pokazuje wiele buforowanych operacji przekazywania plików dla scenariuszy bazy danych i magazynu fizycznego.

<a name="filename"></a>

> [!WARNING]
> **Nie** należy używać `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> innej niż do wyświetlania i rejestrowania. Podczas wyświetlania lub rejestrowania, kod HTML koduje nazwę pliku. Osoba atakująca może dostarczyć złośliwą nazwę pliku, w tym pełne ścieżki lub ścieżki względne. Aplikacje powinny:
>
> * Usuń ścieżkę z nazwy pliku dostarczonej przez użytkownika.
> * Zapisz plik w formacie HTML, który został usunięty z ścieżką dla interfejsu użytkownika lub rejestrowania.
> * Wygeneruj nową losową nazwę pliku dla magazynu.
>
> Poniższy kod usuwa ścieżkę z nazwy pliku:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Przykłady udostępnione w ten sposób nie uwzględniają zagadnień związanych z bezpieczeństwem. Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Zagadnienia dotyczące bezpieczeństwa](#security-considerations)
> * [Walidacja](#validation)

Podczas przekazywania plików przy użyciu powiązania modelu i <xref:Microsoft.AspNetCore.Http.IFormFile> , Metoda akcji może przyjmować:

* Jeden <xref:Microsoft.AspNetCore.Http.IFormFile> .
* Dowolne z następujących kolekcji, które reprezentują kilka plików:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Staw](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Powiązanie dopasowuje pliki formularza według nazwy. Na przykład `name` wartość HTML w `<input type="file" name="formFile">` musi być zgodna z parametrem lub właściwością języka C# ( `FormFile` ). Aby uzyskać więcej informacji, zobacz sekcję [dopasowanie wartości atrybutu do nazwy parametru w sekcji Metoda post](#match-name-attribute-value-to-parameter-name-of-post-method) .

Poniższy przykład:

* Pętle przez co najmniej jeden przekazany plik.
* Używa funkcji [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby zwrócić pełną ścieżkę do pliku, łącznie z nazwą pliku. 
* Zapisuje pliki w lokalnym systemie plików przy użyciu nazwy pliku wygenerowanej przez aplikację.
* Zwraca łączną liczbę i rozmiar przekazanych plików.

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

Użyj `Path.GetRandomFileName` do wygenerowania nazwy pliku bez ścieżki. W poniższym przykładzie ścieżka jest uzyskiwana z konfiguracji:

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

Ścieżka przenoszona do <xref:System.IO.FileStream> *musi* zawierać nazwę pliku. Jeśli nie podano nazwy pliku, <xref:System.UnauthorizedAccessException> jest generowany w czasie wykonywania.

Pliki przekazane przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> techniki są buforowane w pamięci lub na dysku na serwerze przed przetworzeniem. Wewnątrz metody akcji <xref:Microsoft.AspNetCore.Http.IFormFile> zawartość jest dostępna jako <xref:System.IO.Stream> . Oprócz lokalnego systemu plików można zapisywać pliki w udziale sieciowym lub w usłudze magazynu plików, na przykład w [usłudze Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Aby uzyskać inny przykład pętli dla wielu plików na potrzeby przekazywania i używania bezpiecznych nazw plików, zobacz *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* w przykładowej aplikacji.

> [!WARNING]
> [Ścieżka. GetTempFileName](xref:System.IO.Path.GetTempFileName*) zgłasza, <xref:System.IO.IOException> że tworzone są więcej niż 65 535 plików bez usuwania poprzednich plików tymczasowych. Limit 65 535 plików jest limitem dla serwera. Aby uzyskać więcej informacji na temat tego limitu dla systemu operacyjnego Windows, zobacz uwagi w następujących tematach:
>
> * [GetTempFileNameA, funkcja](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Przekazywanie małych plików z buforowanym powiązaniem modelu z bazą danych

Aby przechowywać dane binarne pliku w bazie danych przy użyciu [Entity Framework](/ef/core/index), zdefiniuj <xref:System.Byte> Właściwość Array dla jednostki:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Określ właściwość modelu strony dla klasy, która zawiera <xref:Microsoft.AspNetCore.Http.IFormFile> :

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
> <xref:Microsoft.AspNetCore.Http.IFormFile> można używać bezpośrednio jako parametru metody akcji lub jako powiązanej właściwości modelu. W poprzednim przykładzie użyto powiązanej właściwości modelu.

`FileUpload`Jest używana w Razor formularzu stron:

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

Gdy formularz zostanie opublikowany na serwerze, skopiuj <xref:Microsoft.AspNetCore.Http.IFormFile> do strumienia i Zapisz go jako tablicę bajtową w bazie danych. W poniższym przykładzie `_dbContext` jest przechowywany kontekst bazy danych aplikacji:

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

Poprzedni przykład przypomina scenariusz przedstawiony w przykładowej aplikacji:

* *Pages/BufferedSingleFileUploadDb. cshtml*
* *Strony/BufferedSingleFileUploadDb. cshtml. cs*

> [!WARNING]
> Należy zachować ostrożność podczas przechowywania danych binarnych w relacyjnych bazach danych, ponieważ może to mieć negatywny wpływ na wydajność.
>
> Nie używaj ani nie ufaj `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> bez sprawdzania poprawności. `FileName`Właściwość powinna być używana tylko do celów wyświetlania i tylko po kodowaniu html.
>
> Podane przykłady nie uwzględniają zagadnień związanych z zabezpieczeniami. Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Zagadnienia dotyczące bezpieczeństwa](#security-considerations)
> * [Walidacja](#validation)

### <a name="upload-large-files-with-streaming"></a>Przekazywanie dużych plików strumieniowo

Poniższy przykład ilustruje sposób użycia języka JavaScript do przesyłania strumieniowego pliku do akcji kontrolera. Token antysfałszowany pliku jest generowany przy użyciu niestandardowego atrybutu filtru i przekazywać do nagłówków HTTP klienta zamiast w treści żądania. Ponieważ metoda akcji przetwarza przekazane dane bezpośrednio, powiązanie modelu formularza jest wyłączone przez inny filtr niestandardowy. W ramach akcji zawartość formularza jest odczytywana przy użyciu `MultipartReader` , który odczytuje każdą osobę `MultipartSection` , przetwarza plik lub zapisuje zawartość odpowiednio do potrzeb. Po odczytaniu sekcji wieloczęściowej akcja wykonuje własne powiązanie modelu.

Początkowa odpowiedź na stronę ładuje formularz i zapisuje token antysfałszowany w cookie (za pośrednictwem `GenerateAntiforgeryTokenCookieAttribute` atrybutu). Ten atrybut używa wbudowanej [obsługi przed fałszowaniem](xref:security/anti-request-forgery) ASP.NET Core, aby ustawić cookie token żądania:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Służy `DisableFormValueModelBindingAttribute` do wyłączania powiązania modelu:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

W przykładowej aplikacji `GenerateAntiforgeryTokenCookieAttribute` i `DisableFormValueModelBindingAttribute` są stosowane jako filtry do modeli aplikacji strony `/StreamedSingleFileUploadDb` i w ramach `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor Konwencji stron](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

Ponieważ powiązanie modelu nie odczytuje formularza, parametry, które są powiązane z formularza nie są powiązane (zapytania, trasy i nagłówki nadal pracują). Metoda akcji działa bezpośrednio z `Request` właściwością. `MultipartReader`Jest używany do odczytywania każdej sekcji. Dane klucza/wartości są przechowywane w `KeyValueAccumulator` . Po odczytaniu wieloczęściowych sekcji zawartość `KeyValueAccumulator` jest używana do powiązania danych formularza z typem modelu.

Pełna `StreamingController.UploadDatabase` Metoda przesyłania strumieniowego do bazy danych z EF Core:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Narzędzia/MultipartRequestHelper. cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Pełna `StreamingController.UploadPhysical` Metoda przesyłania strumieniowego do lokalizacji fizycznej:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

W przykładowej aplikacji sprawdzanie poprawności jest obsługiwane przez program `FileHelpers.ProcessStreamedFile` .

## <a name="validation"></a>Walidacja

Klasa przykładowej aplikacji `FileHelpers` pokazuje kilka testów dla buforowanych <xref:Microsoft.AspNetCore.Http.IFormFile> i przesyłanych strumieniowo przekazywania plików. Aby przetwarzać <xref:Microsoft.AspNetCore.Http.IFormFile> buforowane operacje przekazywania plików w aplikacji przykładowej, zobacz `ProcessFormFile` metodę w pliku *Utilities/FileHelpers. cs* . Aby można było przetwarzać pliki przesyłane strumieniowo, zobacz `ProcessStreamedFile` metodę w tym samym pliku.

> [!WARNING]
> Metody przetwarzania walidacji przedstawione w przykładowej aplikacji nie skanują zawartości przekazanych plików. W większości scenariuszy produkcyjnych do pliku jest używany interfejs API skanera wirusów/złośliwego oprogramowania przed udostępnieniem go użytkownikom lub innym systemom.
>
> Chociaż przykład tematu zawiera przykładowy technikę sprawdzania poprawności, nie należy implementować `FileHelpers` klasy w aplikacji produkcyjnej, chyba że:
>
> * W pełni zapoznaj się z implementacją.
> * Zmodyfikuj implementację odpowiednio do środowiska i specyfikacji aplikacji.
>
> **Nigdy nie należy wdrażać kodu zabezpieczeń w aplikacji bez konieczności ich rozwiązywania.**

### <a name="content-validation"></a>Weryfikacja zawartości

**Użyj interfejsu API skanowania wirusa/złośliwego oprogramowania innej firmy dla przekazanej zawartości.**

Skanowanie plików wymaga użycia zasobów serwera w scenariuszach o dużych ilościach. Jeśli wydajność przetwarzania żądań jest zmniejszana ze względu na skanowanie plików, rozważ odciążenie pracy skanowania do [usługi w tle](xref:fundamentals/host/hosted-services), prawdopodobnie usługi uruchomionej na serwerze innym niż serwer aplikacji. Zwykle przekazane pliki są przechowywane w obszarze poddany kwarantannie, dopóki skaner wirusów w tle nie sprawdzi ich. Gdy plik kończy się, plik zostanie przeniesiony do normalnej lokalizacji przechowywania plików. Te kroki są zwykle wykonywane w połączeniu z rekordem bazy danych, który wskazuje na stan skanowania pliku. Korzystając z takiego podejścia, aplikacja i serwer aplikacji pozostają skoncentrowane na odpowiedzi na żądania.

### <a name="file-extension-validation"></a>Weryfikacja rozszerzenia pliku

Rozszerzenie przekazanego pliku powinno być sprawdzane względem listy dozwolonych rozszerzeń. Na przykład:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Walidacja podpisu pliku

Sygnatura pliku jest określana przez pierwsze kilka bajtów na początku pliku. Te bajty mogą być używane do wskazywania, czy rozszerzenie jest zgodne z zawartością pliku. Przykładowa aplikacja sprawdza podpisy plików dla kilku popularnych typów plików. W poniższym przykładzie podpis pliku dla obrazu JPEG jest sprawdzany pod kątem pliku:

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

Aby uzyskać dodatkowe podpisy plików, zapoznaj się z [bazami danych sygnatury plików](https://www.filesignatures.net/) i oficjalnymi specyfikacjami plików.

### <a name="file-name-security"></a>Zabezpieczenia nazw plików

Nigdy nie należy używać nazwy pliku dostarczonej przez klienta do zapisywania plików w magazynie fizycznym. Utwórz bezpieczną nazwę pliku dla pliku przy użyciu [ścieżki. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) lub [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby utworzyć pełną ścieżkę (łącznie z nazwą pliku) dla magazynu tymczasowego.

Razor automatycznie koduje wartości właściwości w kodzie HTML do wyświetlenia. Następujący kod jest bezpieczny w użyciu:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Poza Razor , zawsze <xref:System.Net.WebUtility.HtmlEncode*> zawartość nazwy pliku z żądania użytkownika.

Wiele implementacji musi zawierać sprawdzenie, czy plik istnieje; w przeciwnym razie plik zostanie zastąpiony przez plik o tej samej nazwie. Podaj dodatkową logikę, aby spełnić wymagania dotyczące Twojej aplikacji.

### <a name="size-validation"></a>Sprawdzanie poprawności rozmiaru

Ogranicz rozmiar przekazanych plików.

W przykładowej aplikacji rozmiar pliku jest ograniczony do 2 MB (wyrażony w bajtach). Limit jest dostarczany przez [konfigurację](xref:fundamentals/configuration/index) z *appsettings.json* pliku:

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit`Jest wstrzykiwana do `PageModel` klas:

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

Gdy rozmiar pliku przekracza limit, plik zostanie odrzucony:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Dopasuj wartość atrybutu Name do nazwy parametru metody POST

W nie Razor formularzach, które publikują dane formularza lub używają `FormData` bezpośrednio języka JavaScript, nazwa określona w elemencie formularza lub `FormData` musi być zgodna z nazwą parametru w akcji kontrolera.

W poniższym przykładzie:

* W przypadku używania `<input>` elementu `name` atrybut ma ustawioną wartość `battlePlans` :

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* W przypadku używania `FormData` w języku JavaScript nazwa jest ustawiana na wartość `battlePlans` :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Użyj zgodnej nazwy dla parametru metody C# ( `battlePlans` ):

* Dla Razor metody obsługi strony strony o nazwie `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Dla metody akcji składnika MVC Controller:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Konfiguracja serwera i aplikacji

### <a name="multipart-body-length-limit"></a>Limit długości treści wieloczęściowej

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> ustawia limit długości każdej wieloczęściowej treści. Sekcje formularza, które przekraczają ten limit, generują <xref:System.IO.InvalidDataException> podczas analizowania. Wartość domyślna to 134 217 728 (128 MB). Dostosuj limit przy użyciu <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> Ustawienia w `Startup.ConfigureServices` :

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

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> służy do ustawiania <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> dla jednej strony lub akcji.

W Razor aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
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

W Razor aplikacji strony lub aplikacji MVC Zastosuj filtr do modelu strony lub metody akcji:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Maksymalny rozmiar treści żądania Kestrel

W przypadku aplikacji hostowanych przez Kestrel domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB. Dostosuj limit przy użyciu opcji serwera [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) Kestrel:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> służy do ustawiania [MaxRequestBodySize](xref:fundamentals/servers/kestrel/options#maximum-request-body-size) dla pojedynczej strony lub akcji.

W Razor aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
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

W Razor aplikacji strony lub aplikacji MVC Zastosuj filtr do klasy procedury obsługi stron lub metody akcji:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

`RequestSizeLimitAttribute`Można go również zastosować przy użyciu [`@attribute`](xref:mvc/views/razor#attribute) Razor dyrektywy:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Inne limity Kestrel

Inne limity Kestrel mogą dotyczyć aplikacji hostowanych przez Kestrel:

* [Maksymalna liczba połączeń klienta](xref:fundamentals/servers/kestrel/options#maximum-client-connections)
* [Stawki danych żądań i odpowiedzi](xref:fundamentals/servers/kestrel/options#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

Domyślny limit żądań ( `maxAllowedContentLength` ) to 30 000 000 bajtów, czyli około 28,6 MB. Dostosuj limit w `web.config` pliku. W poniższym przykładzie limit jest ustawiony na 50 MB (52 428 800 bajtów):

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

To `maxAllowedContentLength` ustawienie dotyczy tylko usług IIS. Aby uzyskać więcej informacji, zobacz [limity `<requestLimits>` żądań ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Poniżej przedstawiono niektóre typowe problemy, które można napotkać podczas pracy z przekazywaniem plików i ich możliwymi rozwiązaniami.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Nie znaleziono błędu w przypadku wdrożenia na serwerze usług IIS

Następujący błąd wskazuje, że przekazany plik przekracza skonfigurowaną długość zawartości serwera:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .

### <a name="connection-failure"></a>Błąd połączenia

Wystąpił błąd połączenia i połączenie z serwerem resetowania prawdopodobnie wskazuje, że przekazany plik przekracza maksymalny rozmiar treści żądania Kestrel. Aby uzyskać więcej informacji, zobacz sekcję [Maksymalny rozmiar treści żądania Kestrel](#kestrel-maximum-request-body-size) . Limity połączeń klienta Kestrel mogą również wymagać korekty.

### <a name="null-reference-exception-with-iformfile"></a>Wyjątek odwołania o wartości null z IFormFile

Jeśli kontroler akceptuje przekazane pliki przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> , ale wartość jest `null` , upewnij się, że w formularzu HTML jest określana `enctype` wartość `multipart/form-data` . Jeśli ten atrybut nie jest ustawiony dla `<form>` elementu, przekazywanie pliku nie wystąpi i wszystkie powiązane <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty są `null` . Sprawdź również, czy [Nazwa przekazywania w postaci danych jest zgodna z nazewnictwem aplikacji](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Strumień był zbyt długi

Przykłady w tym temacie polegają na zapełnieniu <xref:System.IO.MemoryStream> zawartości przekazanego pliku. Limit rozmiaru `MemoryStream` wynosi `int.MaxValue` . Jeśli scenariusz przekazywania plików aplikacji wymaga przechowywania zawartości pliku o rozmiarze większym niż 50 MB, użyj alternatywnego podejścia, które nie polega na pojedynczym `MemoryStream` przeniesieniu zawartości przekazanego pliku.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

ASP.NET Core obsługuje przekazywanie co najmniej jednego pliku przy użyciu powiązania z buforowanym modelem dla mniejszych plików i przesyłania strumieniowego z buforem dla większych plików.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer. Osoby atakujące mogą próbować:

* Wykonywanie ataków [typu "odmowa usługi"](/windows-hardware/drivers/ifs/denial-of-service) .
* Przekazuj wirusy lub złośliwe oprogramowanie.
* Naruszyć bezpieczeństwo sieci i serwerów w inny sposób.

Kroki zabezpieczeń, które zmniejszają prawdopodobieństwo pomyślnego ataku:

* Przekaż pliki do dedykowanego obszaru przekazywania plików, najlepiej do dysku niesystemowego. Dedykowana lokalizacja ułatwia nakładanie ograniczeń zabezpieczeń na przekazane pliki. Wyłącz uprawnienia do wykonywania w lokalizacji przekazywania pliku.&dagger;
* **Nie** Utrwalaj przekazanych plików w tym samym drzewie katalogów co aplikacja.&dagger;
* Użyj bezpiecznej nazwy pliku, która jest określana przez aplikację. Nie należy używać nazwy pliku dostarczonej przez użytkownika lub niezaufanej nazwy pliku przekazanego pliku. &dagger; KOD HTML zakodowania niezaufanej nazwy pliku podczas jego wyświetlania. Na przykład rejestrowanie nazwy pliku lub wyświetlanie w interfejsie użytkownika ( Razor automatyczne kodowanie HTML kodu wyjściowego).
* Zezwalaj tylko na zatwierdzone rozszerzenia plików dla specyfikacji projektu aplikacji.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Sprawdź, czy testy po stronie klienta są wykonywane na serwerze. &dagger; Sprawdzanie po stronie klienta można łatwo obejść.
* Sprawdź rozmiar przekazanego pliku. Ustaw maksymalny limit rozmiaru, aby zapobiec dużej ilości operacji przekazywania.&dagger;
* Jeśli pliki nie powinny być zastąpione przez przekazany plik o tej samej nazwie, przed przekazaniem pliku Sprawdź nazwę pliku względem bazy danych lub magazynu fizycznego.
* **Przed zapisaniem pliku Uruchom skaner wirusów/złośliwego oprogramowania dla przekazanej zawartości.**

&dagger;Przykładowa aplikacja pokazuje podejście, które spełnia kryteria.

> [!WARNING]
> Przekazywanie złośliwego kodu do systemu jest często pierwszym krokiem do wykonania kodu, który może:
>
> * Całkowicie przejąć kontrolę nad systemem.
> * Przeciąż system z wynikiem awarii systemu.
> * Naruszanie danych użytkownika lub systemu.
> * Zastosuj graffiti do publicznego interfejsu użytkownika.
>
> Aby uzyskać informacje na temat zmniejszania obszaru ataków podczas akceptowania plików od użytkowników, zobacz następujące zasoby:
>
> * [Przekazywanie plików bez ograniczeń](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Zabezpieczenia platformy Azure: Upewnij się, że podczas akceptowania plików od użytkowników są stosowane odpowiednie kontrolki](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Aby uzyskać więcej informacji na temat implementowania środków zabezpieczeń, w tym przykładów z przykładowej aplikacji, zobacz sekcję [Walidacja](#validation) .

## <a name="storage-scenarios"></a>Scenariusze magazynu

Typowe opcje magazynu dla plików to:

* baza danych

  * W przypadku małych operacji przekazywania plików baza danych jest często szybsza niż opcje magazynu fizycznego (systemu plików lub udziału sieciowego).
  * Baza danych jest często bardziej wygodna niż opcje magazynu fizycznego, ponieważ Pobieranie rekordu bazy danych dla danych użytkownika może jednocześnie dostarczyć zawartość pliku (na przykład obraz awatara).
  * Baza danych może być tańsza niż użycie usługi magazynu danych.

* Magazyn fizyczny (system plików lub udział sieciowy)

  * W przypadku dużych operacji przekazywania plików:
    * Limity bazy danych mogą ograniczać rozmiar przekazywania.
    * Magazyn fizyczny jest często mniej ekonomiczny niż magazyn w bazie danych.
  * Magazyn fizyczny jest prawdopodobnie tańszy niż użycie usługi magazynu danych.
  * Proces aplikacji musi mieć uprawnienia do odczytu i zapisu w lokalizacji magazynu. **Nigdy nie udzielaj uprawnienia EXECUTE.**

* Usługa magazynu danych (na przykład [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * Usługa zazwyczaj oferuje ulepszoną skalowalność i odporność w rozwiązaniach lokalnych, które zwykle podlegają pojedynczym punktom awarii.
  * Usługi są potencjalnie tańsze w dużych scenariuszach infrastruktury magazynu.

  Aby uzyskać więcej informacji, zobacz [Szybki Start: korzystanie z platformy .NET do tworzenia obiektów BLOB w magazynie obiektów](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Scenariusze przekazywania plików

Dwie ogólne podejścia do przekazywania plików to buforowanie i przesyłanie strumieniowe.

**Buforowania**

Cały plik jest odczytywany do <xref:Microsoft.AspNetCore.Http.IFormFile> , który jest reprezentacją języka C# pliku używanego do przetwarzania lub zapisywania pliku.

Zasoby (dysk, pamięć) używane przez operacje przekazywania plików zależą od liczby i rozmiaru współbieżnych przekazywania plików. Jeśli aplikacja próbuje buforować zbyt wiele przeciążeń, lokacja uległa awarii, gdy zabraknie pamięci lub miejsca na dysku. Jeśli rozmiar lub częstotliwość przekazywania plików powoduje wyczerpanie zasobów aplikacji, należy użyć przesyłania strumieniowego.

> [!NOTE]
> Każdy pojedynczy buforowany plik przekraczający 64 KB jest przenoszony z pamięci do pliku tymczasowego na dysku.

Buforowanie małych plików zostało omówione w następujących sekcjach tego tematu:

* [Magazyn fizyczny](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Database](#upload-small-files-with-buffered-model-binding-to-a-database) (Baza danych)

**Przesyłanie strumieniowe**

Plik jest odbierany od żądania wieloczęściowego i bezpośrednio przetwarzany lub zapisywany przez aplikację. Przesyłanie strumieniowe nie poprawia znacząco wydajności. Przesyłanie strumieniowe zmniejsza wymagania dotyczące pamięci lub miejsca na dysku podczas przekazywania plików.

Przesyłanie strumieniowe dużych plików jest omówione w sekcji [przekazywanie dużych plików z przesyłaniem strumieniowym](#upload-large-files-with-streaming) .

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Przekazywanie małych plików z buforowanym powiązaniem modelu z magazynem fizycznym

Aby przekazać małe pliki, użyj formularza wieloczęściowego lub Skonstruuj żądanie POST przy użyciu języka JavaScript.

Poniższy przykład ilustruje użycie Razor formularza stron do przekazywania pojedynczego pliku (*Pages/BufferedSingleFileUploadPhysical. cshtml* w przykładowej aplikacji):

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

Poniższy przykład jest analogiczny do poprzedniego przykładu, z wyjątkiem tego, że:

* Kod JavaScript ([interfejs API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API)) służy do przesyłania danych formularza.
* Nie istnieje weryfikacja.

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

Aby wykonać formularz POST w języku JavaScript dla klientów, którzy [nie obsługują interfejsu API pobierania](https://caniuse.com/#feat=fetch), należy użyć jednej z następujących metod:

* Użyj wypełniania pobierania (na przykład [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).
* Użyj polecenia `XMLHttpRequest`. Na przykład:

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

W celu obsługi przekazywania plików formularze HTML muszą określać typ kodowania ( `enctype` ) `multipart/form-data` .

Aby `files` element wejściowy obsługiwał przekazywanie wielu plików, podaj `multiple` atrybut w `<input>` elemencie:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Do poszczególnych plików przekazanych do serwera można uzyskać dostęp za pośrednictwem [powiązania modelu](xref:mvc/models/model-binding) przy użyciu polecenia <xref:Microsoft.AspNetCore.Http.IFormFile> . Przykładowa aplikacja pokazuje wiele buforowanych operacji przekazywania plików dla scenariuszy bazy danych i magazynu fizycznego.

<a name="filename"></a>

> [!WARNING]
> **Nie** należy używać `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> innej niż do wyświetlania i rejestrowania. Podczas wyświetlania lub rejestrowania, kod HTML koduje nazwę pliku. Osoba atakująca może dostarczyć złośliwą nazwę pliku, w tym pełne ścieżki lub ścieżki względne. Aplikacje powinny:
>
> * Usuń ścieżkę z nazwy pliku dostarczonej przez użytkownika.
> * Zapisz plik w formacie HTML, który został usunięty z ścieżką dla interfejsu użytkownika lub rejestrowania.
> * Wygeneruj nową losową nazwę pliku dla magazynu.
>
> Poniższy kod usuwa ścieżkę z nazwy pliku:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Przykłady udostępnione w ten sposób nie uwzględniają zagadnień związanych z bezpieczeństwem. Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Zagadnienia dotyczące bezpieczeństwa](#security-considerations)
> * [Walidacja](#validation)

Podczas przekazywania plików przy użyciu powiązania modelu i <xref:Microsoft.AspNetCore.Http.IFormFile> , Metoda akcji może przyjmować:

* Jeden <xref:Microsoft.AspNetCore.Http.IFormFile> .
* Dowolne z następujących kolekcji, które reprezentują kilka plików:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Staw](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Powiązanie dopasowuje pliki formularza według nazwy. Na przykład `name` wartość HTML w `<input type="file" name="formFile">` musi być zgodna z parametrem lub właściwością języka C# ( `FormFile` ). Aby uzyskać więcej informacji, zobacz sekcję [dopasowanie wartości atrybutu do nazwy parametru w sekcji Metoda post](#match-name-attribute-value-to-parameter-name-of-post-method) .

Poniższy przykład:

* Pętle przez co najmniej jeden przekazany plik.
* Używa funkcji [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby zwrócić pełną ścieżkę do pliku, łącznie z nazwą pliku. 
* Zapisuje pliki w lokalnym systemie plików przy użyciu nazwy pliku wygenerowanej przez aplikację.
* Zwraca łączną liczbę i rozmiar przekazanych plików.

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

Użyj `Path.GetRandomFileName` do wygenerowania nazwy pliku bez ścieżki. W poniższym przykładzie ścieżka jest uzyskiwana z konfiguracji:

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

Ścieżka przenoszona do <xref:System.IO.FileStream> *musi* zawierać nazwę pliku. Jeśli nie podano nazwy pliku, <xref:System.UnauthorizedAccessException> jest generowany w czasie wykonywania.

Pliki przekazane przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> techniki są buforowane w pamięci lub na dysku na serwerze przed przetworzeniem. Wewnątrz metody akcji <xref:Microsoft.AspNetCore.Http.IFormFile> zawartość jest dostępna jako <xref:System.IO.Stream> . Oprócz lokalnego systemu plików można zapisywać pliki w udziale sieciowym lub w usłudze magazynu plików, na przykład w [usłudze Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Aby uzyskać inny przykład pętli dla wielu plików na potrzeby przekazywania i używania bezpiecznych nazw plików, zobacz *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* w przykładowej aplikacji.

> [!WARNING]
> [Ścieżka. GetTempFileName](xref:System.IO.Path.GetTempFileName*) zgłasza, <xref:System.IO.IOException> że tworzone są więcej niż 65 535 plików bez usuwania poprzednich plików tymczasowych. Limit 65 535 plików jest limitem dla serwera. Aby uzyskać więcej informacji na temat tego limitu dla systemu operacyjnego Windows, zobacz uwagi w następujących tematach:
>
> * [GetTempFileNameA, funkcja](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Przekazywanie małych plików z buforowanym powiązaniem modelu z bazą danych

Aby przechowywać dane binarne pliku w bazie danych przy użyciu [Entity Framework](/ef/core/index), zdefiniuj <xref:System.Byte> Właściwość Array dla jednostki:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Określ właściwość modelu strony dla klasy, która zawiera <xref:Microsoft.AspNetCore.Http.IFormFile> :

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
> <xref:Microsoft.AspNetCore.Http.IFormFile> można używać bezpośrednio jako parametru metody akcji lub jako powiązanej właściwości modelu. W poprzednim przykładzie użyto powiązanej właściwości modelu.

`FileUpload`Jest używana w Razor formularzu stron:

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

Gdy formularz zostanie opublikowany na serwerze, skopiuj <xref:Microsoft.AspNetCore.Http.IFormFile> do strumienia i Zapisz go jako tablicę bajtową w bazie danych. W poniższym przykładzie `_dbContext` jest przechowywany kontekst bazy danych aplikacji:

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

Poprzedni przykład przypomina scenariusz przedstawiony w przykładowej aplikacji:

* *Pages/BufferedSingleFileUploadDb. cshtml*
* *Strony/BufferedSingleFileUploadDb. cshtml. cs*

> [!WARNING]
> Należy zachować ostrożność podczas przechowywania danych binarnych w relacyjnych bazach danych, ponieważ może to mieć negatywny wpływ na wydajność.
>
> Nie używaj ani nie ufaj `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> bez sprawdzania poprawności. `FileName`Właściwość powinna być używana tylko do celów wyświetlania i tylko po kodowaniu html.
>
> Podane przykłady nie uwzględniają zagadnień związanych z zabezpieczeniami. Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Zagadnienia dotyczące bezpieczeństwa](#security-considerations)
> * [Walidacja](#validation)

### <a name="upload-large-files-with-streaming"></a>Przekazywanie dużych plików strumieniowo

Poniższy przykład ilustruje sposób użycia języka JavaScript do przesyłania strumieniowego pliku do akcji kontrolera. Token antysfałszowany pliku jest generowany przy użyciu niestandardowego atrybutu filtru i przekazywać do nagłówków HTTP klienta zamiast w treści żądania. Ponieważ metoda akcji przetwarza przekazane dane bezpośrednio, powiązanie modelu formularza jest wyłączone przez inny filtr niestandardowy. W ramach akcji zawartość formularza jest odczytywana przy użyciu `MultipartReader` , który odczytuje każdą osobę `MultipartSection` , przetwarza plik lub zapisuje zawartość odpowiednio do potrzeb. Po odczytaniu sekcji wieloczęściowej akcja wykonuje własne powiązanie modelu.

Początkowa odpowiedź na stronę ładuje formularz i zapisuje token antysfałszowany w cookie (za pośrednictwem `GenerateAntiforgeryTokenCookieAttribute` atrybutu). Ten atrybut używa wbudowanej [obsługi przed fałszowaniem](xref:security/anti-request-forgery) ASP.NET Core, aby ustawić cookie token żądania:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Służy `DisableFormValueModelBindingAttribute` do wyłączania powiązania modelu:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

W przykładowej aplikacji `GenerateAntiforgeryTokenCookieAttribute` i `DisableFormValueModelBindingAttribute` są stosowane jako filtry do modeli aplikacji strony `/StreamedSingleFileUploadDb` i w ramach `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor Konwencji stron](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

Ponieważ powiązanie modelu nie odczytuje formularza, parametry, które są powiązane z formularza nie są powiązane (zapytania, trasy i nagłówki nadal pracują). Metoda akcji działa bezpośrednio z `Request` właściwością. `MultipartReader`Jest używany do odczytywania każdej sekcji. Dane klucza/wartości są przechowywane w `KeyValueAccumulator` . Po odczytaniu wieloczęściowych sekcji zawartość `KeyValueAccumulator` jest używana do powiązania danych formularza z typem modelu.

Pełna `StreamingController.UploadDatabase` Metoda przesyłania strumieniowego do bazy danych z EF Core:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Narzędzia/MultipartRequestHelper. cs*):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Pełna `StreamingController.UploadPhysical` Metoda przesyłania strumieniowego do lokalizacji fizycznej:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

W przykładowej aplikacji sprawdzanie poprawności jest obsługiwane przez program `FileHelpers.ProcessStreamedFile` .

## <a name="validation"></a>Walidacja

Klasa przykładowej aplikacji `FileHelpers` pokazuje kilka testów dla buforowanych <xref:Microsoft.AspNetCore.Http.IFormFile> i przesyłanych strumieniowo przekazywania plików. Aby przetwarzać <xref:Microsoft.AspNetCore.Http.IFormFile> buforowane operacje przekazywania plików w aplikacji przykładowej, zobacz `ProcessFormFile` metodę w pliku *Utilities/FileHelpers. cs* . Aby można było przetwarzać pliki przesyłane strumieniowo, zobacz `ProcessStreamedFile` metodę w tym samym pliku.

> [!WARNING]
> Metody przetwarzania walidacji przedstawione w przykładowej aplikacji nie skanują zawartości przekazanych plików. W większości scenariuszy produkcyjnych do pliku jest używany interfejs API skanera wirusów/złośliwego oprogramowania przed udostępnieniem go użytkownikom lub innym systemom.
>
> Chociaż przykład tematu zawiera przykładowy technikę sprawdzania poprawności, nie należy implementować `FileHelpers` klasy w aplikacji produkcyjnej, chyba że:
>
> * W pełni zapoznaj się z implementacją.
> * Zmodyfikuj implementację odpowiednio do środowiska i specyfikacji aplikacji.
>
> **Nigdy nie należy wdrażać kodu zabezpieczeń w aplikacji bez konieczności ich rozwiązywania.**

### <a name="content-validation"></a>Weryfikacja zawartości

**Użyj interfejsu API skanowania wirusa/złośliwego oprogramowania innej firmy dla przekazanej zawartości.**

Skanowanie plików wymaga użycia zasobów serwera w scenariuszach o dużych ilościach. Jeśli wydajność przetwarzania żądań jest zmniejszana ze względu na skanowanie plików, rozważ odciążenie pracy skanowania do [usługi w tle](xref:fundamentals/host/hosted-services), prawdopodobnie usługi uruchomionej na serwerze innym niż serwer aplikacji. Zwykle przekazane pliki są przechowywane w obszarze poddany kwarantannie, dopóki skaner wirusów w tle nie sprawdzi ich. Gdy plik kończy się, plik zostanie przeniesiony do normalnej lokalizacji przechowywania plików. Te kroki są zwykle wykonywane w połączeniu z rekordem bazy danych, który wskazuje na stan skanowania pliku. Korzystając z takiego podejścia, aplikacja i serwer aplikacji pozostają skoncentrowane na odpowiedzi na żądania.

### <a name="file-extension-validation"></a>Weryfikacja rozszerzenia pliku

Rozszerzenie przekazanego pliku powinno być sprawdzane względem listy dozwolonych rozszerzeń. Na przykład:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Walidacja podpisu pliku

Sygnatura pliku jest określana przez pierwsze kilka bajtów na początku pliku. Te bajty mogą być używane do wskazywania, czy rozszerzenie jest zgodne z zawartością pliku. Przykładowa aplikacja sprawdza podpisy plików dla kilku popularnych typów plików. W poniższym przykładzie podpis pliku dla obrazu JPEG jest sprawdzany pod kątem pliku:

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

Aby uzyskać dodatkowe podpisy plików, zapoznaj się z [bazami danych sygnatury plików](https://www.filesignatures.net/) i oficjalnymi specyfikacjami plików.

### <a name="file-name-security"></a>Zabezpieczenia nazw plików

Nigdy nie należy używać nazwy pliku dostarczonej przez klienta do zapisywania plików w magazynie fizycznym. Utwórz bezpieczną nazwę pliku dla pliku przy użyciu [ścieżki. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) lub [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby utworzyć pełną ścieżkę (łącznie z nazwą pliku) dla magazynu tymczasowego.

Razor automatycznie koduje wartości właściwości w kodzie HTML do wyświetlenia. Następujący kod jest bezpieczny w użyciu:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Poza Razor , zawsze <xref:System.Net.WebUtility.HtmlEncode*> zawartość nazwy pliku z żądania użytkownika.

Wiele implementacji musi zawierać sprawdzenie, czy plik istnieje; w przeciwnym razie plik zostanie zastąpiony przez plik o tej samej nazwie. Podaj dodatkową logikę, aby spełnić wymagania dotyczące Twojej aplikacji.

### <a name="size-validation"></a>Sprawdzanie poprawności rozmiaru

Ogranicz rozmiar przekazanych plików.

W przykładowej aplikacji rozmiar pliku jest ograniczony do 2 MB (wyrażony w bajtach). Limit jest dostarczany przez [konfigurację](xref:fundamentals/configuration/index) z *appsettings.json* pliku:

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit`Jest wstrzykiwana do `PageModel` klas:

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

Gdy rozmiar pliku przekracza limit, plik zostanie odrzucony:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Dopasuj wartość atrybutu Name do nazwy parametru metody POST

W nie Razor formularzach, które publikują dane formularza lub używają `FormData` bezpośrednio języka JavaScript, nazwa określona w elemencie formularza lub `FormData` musi być zgodna z nazwą parametru w akcji kontrolera.

W poniższym przykładzie:

* W przypadku używania `<input>` elementu `name` atrybut ma ustawioną wartość `battlePlans` :

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* W przypadku używania `FormData` w języku JavaScript nazwa jest ustawiana na wartość `battlePlans` :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Użyj zgodnej nazwy dla parametru metody C# ( `battlePlans` ):

* Dla Razor metody obsługi strony strony o nazwie `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Dla metody akcji składnika MVC Controller:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Konfiguracja serwera i aplikacji

### <a name="multipart-body-length-limit"></a>Limit długości treści wieloczęściowej

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> ustawia limit długości każdej wieloczęściowej treści. Sekcje formularza, które przekraczają ten limit, generują <xref:System.IO.InvalidDataException> podczas analizowania. Wartość domyślna to 134 217 728 (128 MB). Dostosuj limit przy użyciu <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> Ustawienia w `Startup.ConfigureServices` :

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

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> służy do ustawiania <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> dla jednej strony lub akcji.

W Razor aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
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

W Razor aplikacji strony lub aplikacji MVC Zastosuj filtr do modelu strony lub metody akcji:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Maksymalny rozmiar treści żądania Kestrel

W przypadku aplikacji hostowanych przez Kestrel domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB. Dostosuj limit przy użyciu opcji serwera [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> służy do ustawiania [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) dla pojedynczej strony lub akcji.

W Razor aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
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

W Razor aplikacji strony lub aplikacji MVC Zastosuj filtr do klasy procedury obsługi stron lub metody akcji:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

`RequestSizeLimitAttribute`Można go również zastosować przy użyciu [`@attribute`](xref:mvc/views/razor#attribute) Razor dyrektywy:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Inne limity Kestrel

Inne limity Kestrel mogą dotyczyć aplikacji hostowanych przez Kestrel:

* [Maksymalna liczba połączeń klienta](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Stawki danych żądań i odpowiedzi](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

Domyślny limit żądań ( `maxAllowedContentLength` ) to 30 000 000 bajtów, czyli około 28,6 MB. Dostosuj limit w `web.config` pliku. W poniższym przykładzie limit jest ustawiony na 50 MB (52 428 800 bajtów):

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

To `maxAllowedContentLength` ustawienie dotyczy tylko usług IIS. Aby uzyskać więcej informacji, zobacz [limity `<requestLimits>` żądań ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Zwiększ maksymalny rozmiar treści żądania dla żądania HTTP przez ustawienie <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> w `Startup.ConfigureServices` . W poniższym przykładzie limit jest ustawiony na 50 MB (52 428 800 bajtów):

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/iis/index#iis-options>.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Poniżej przedstawiono niektóre typowe problemy, które można napotkać podczas pracy z przekazywaniem plików i ich możliwymi rozwiązaniami.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Nie znaleziono błędu w przypadku wdrożenia na serwerze usług IIS

Następujący błąd wskazuje, że przekazany plik przekracza skonfigurowaną długość zawartości serwera:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .

### <a name="connection-failure"></a>Błąd połączenia

Wystąpił błąd połączenia i połączenie z serwerem resetowania prawdopodobnie wskazuje, że przekazany plik przekracza maksymalny rozmiar treści żądania Kestrel. Aby uzyskać więcej informacji, zobacz sekcję [Maksymalny rozmiar treści żądania Kestrel](#kestrel-maximum-request-body-size) . Limity połączeń klienta Kestrel mogą również wymagać korekty.

### <a name="null-reference-exception-with-iformfile"></a>Wyjątek odwołania o wartości null z IFormFile

Jeśli kontroler akceptuje przekazane pliki przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> , ale wartość jest `null` , upewnij się, że w formularzu HTML jest określana `enctype` wartość `multipart/form-data` . Jeśli ten atrybut nie jest ustawiony dla `<form>` elementu, przekazywanie pliku nie wystąpi i wszystkie powiązane <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty są `null` . Sprawdź również, czy [Nazwa przekazywania w postaci danych jest zgodna z nazewnictwem aplikacji](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Strumień był zbyt długi

Przykłady w tym temacie polegają na zapełnieniu <xref:System.IO.MemoryStream> zawartości przekazanego pliku. Limit rozmiaru `MemoryStream` wynosi `int.MaxValue` . Jeśli scenariusz przekazywania plików aplikacji wymaga przechowywania zawartości pliku o rozmiarze większym niż 50 MB, użyj alternatywnego podejścia, które nie polega na pojedynczym `MemoryStream` przeniesieniu zawartości przekazanego pliku.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core obsługuje przekazywanie co najmniej jednego pliku przy użyciu powiązania z buforowanym modelem dla mniejszych plików i przesyłania strumieniowego z buforem dla większych plików.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Należy zachować ostrożność, zapewniając użytkownikom możliwość przekazywania plików na serwer. Osoby atakujące mogą próbować:

* Wykonywanie ataków [typu "odmowa usługi"](/windows-hardware/drivers/ifs/denial-of-service) .
* Przekazuj wirusy lub złośliwe oprogramowanie.
* Naruszyć bezpieczeństwo sieci i serwerów w inny sposób.

Kroki zabezpieczeń, które zmniejszają prawdopodobieństwo pomyślnego ataku:

* Przekaż pliki do dedykowanego obszaru przekazywania plików, najlepiej do dysku niesystemowego. Dedykowana lokalizacja ułatwia nakładanie ograniczeń zabezpieczeń na przekazane pliki. Wyłącz uprawnienia do wykonywania w lokalizacji przekazywania pliku.&dagger;
* **Nie** Utrwalaj przekazanych plików w tym samym drzewie katalogów co aplikacja.&dagger;
* Użyj bezpiecznej nazwy pliku, która jest określana przez aplikację. Nie należy używać nazwy pliku dostarczonej przez użytkownika lub niezaufanej nazwy pliku przekazanego pliku. &dagger; KOD HTML zakodowania niezaufanej nazwy pliku podczas jego wyświetlania. Na przykład rejestrowanie nazwy pliku lub wyświetlanie w interfejsie użytkownika ( Razor automatyczne kodowanie HTML kodu wyjściowego).
* Zezwalaj tylko na zatwierdzone rozszerzenia plików dla specyfikacji projektu aplikacji.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Sprawdź, czy testy po stronie klienta są wykonywane na serwerze. &dagger; Sprawdzanie po stronie klienta można łatwo obejść.
* Sprawdź rozmiar przekazanego pliku. Ustaw maksymalny limit rozmiaru, aby zapobiec dużej ilości operacji przekazywania.&dagger;
* Jeśli pliki nie powinny być zastąpione przez przekazany plik o tej samej nazwie, przed przekazaniem pliku Sprawdź nazwę pliku względem bazy danych lub magazynu fizycznego.
* **Przed zapisaniem pliku Uruchom skaner wirusów/złośliwego oprogramowania dla przekazanej zawartości.**

&dagger;Przykładowa aplikacja pokazuje podejście, które spełnia kryteria.

> [!WARNING]
> Przekazywanie złośliwego kodu do systemu jest często pierwszym krokiem do wykonania kodu, który może:
>
> * Całkowicie przejąć kontrolę nad systemem.
> * Przeciąż system z wynikiem awarii systemu.
> * Naruszanie danych użytkownika lub systemu.
> * Zastosuj graffiti do publicznego interfejsu użytkownika.
>
> Aby uzyskać informacje na temat zmniejszania obszaru ataków podczas akceptowania plików od użytkowników, zobacz następujące zasoby:
>
> * [Przekazywanie plików bez ograniczeń](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Zabezpieczenia platformy Azure: Upewnij się, że podczas akceptowania plików od użytkowników są stosowane odpowiednie kontrolki](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Aby uzyskać więcej informacji na temat implementowania środków zabezpieczeń, w tym przykładów z przykładowej aplikacji, zobacz sekcję [Walidacja](#validation) .

## <a name="storage-scenarios"></a>Scenariusze magazynu

Typowe opcje magazynu dla plików to:

* baza danych

  * W przypadku małych operacji przekazywania plików baza danych jest często szybsza niż opcje magazynu fizycznego (systemu plików lub udziału sieciowego).
  * Baza danych jest często bardziej wygodna niż opcje magazynu fizycznego, ponieważ Pobieranie rekordu bazy danych dla danych użytkownika może jednocześnie dostarczyć zawartość pliku (na przykład obraz awatara).
  * Baza danych może być tańsza niż użycie usługi magazynu danych.

* Magazyn fizyczny (system plików lub udział sieciowy)

  * W przypadku dużych operacji przekazywania plików:
    * Limity bazy danych mogą ograniczać rozmiar przekazywania.
    * Magazyn fizyczny jest często mniej ekonomiczny niż magazyn w bazie danych.
  * Magazyn fizyczny jest prawdopodobnie tańszy niż użycie usługi magazynu danych.
  * Proces aplikacji musi mieć uprawnienia do odczytu i zapisu w lokalizacji magazynu. **Nigdy nie udzielaj uprawnienia EXECUTE.**

* Usługa magazynu danych (na przykład [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * Usługa zazwyczaj oferuje ulepszoną skalowalność i odporność w rozwiązaniach lokalnych, które zwykle podlegają pojedynczym punktom awarii.
  * Usługi są potencjalnie tańsze w dużych scenariuszach infrastruktury magazynu.

  Aby uzyskać więcej informacji, zobacz [Szybki Start: korzystanie z platformy .NET do tworzenia obiektów BLOB w magazynie obiektów](/azure/storage/blobs/storage-quickstart-blobs-dotnet). Temat ilustruje <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , ale <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> może służyć do zapisywania <xref:System.IO.FileStream> do magazynu obiektów BLOB podczas pracy z <xref:System.IO.Stream> .

## <a name="file-upload-scenarios"></a>Scenariusze przekazywania plików

Dwie ogólne podejścia do przekazywania plików to buforowanie i przesyłanie strumieniowe.

**Buforowania**

Cały plik jest odczytywany do <xref:Microsoft.AspNetCore.Http.IFormFile> , który jest reprezentacją języka C# pliku używanego do przetwarzania lub zapisywania pliku.

Zasoby (dysk, pamięć) używane przez operacje przekazywania plików zależą od liczby i rozmiaru współbieżnych przekazywania plików. Jeśli aplikacja próbuje buforować zbyt wiele przeciążeń, lokacja uległa awarii, gdy zabraknie pamięci lub miejsca na dysku. Jeśli rozmiar lub częstotliwość przekazywania plików powoduje wyczerpanie zasobów aplikacji, należy użyć przesyłania strumieniowego.

> [!NOTE]
> Każdy pojedynczy buforowany plik przekraczający 64 KB jest przenoszony z pamięci do pliku tymczasowego na dysku.

Buforowanie małych plików zostało omówione w następujących sekcjach tego tematu:

* [Magazyn fizyczny](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Database](#upload-small-files-with-buffered-model-binding-to-a-database) (Baza danych)

**Przesyłanie strumieniowe**

Plik jest odbierany od żądania wieloczęściowego i bezpośrednio przetwarzany lub zapisywany przez aplikację. Przesyłanie strumieniowe nie poprawia znacząco wydajności. Przesyłanie strumieniowe zmniejsza wymagania dotyczące pamięci lub miejsca na dysku podczas przekazywania plików.

Przesyłanie strumieniowe dużych plików jest omówione w sekcji [przekazywanie dużych plików z przesyłaniem strumieniowym](#upload-large-files-with-streaming) .

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Przekazywanie małych plików z buforowanym powiązaniem modelu z magazynem fizycznym

Aby przekazać małe pliki, użyj formularza wieloczęściowego lub Skonstruuj żądanie POST przy użyciu języka JavaScript.

Poniższy przykład ilustruje użycie Razor formularza stron do przekazywania pojedynczego pliku (*Pages/BufferedSingleFileUploadPhysical. cshtml* w przykładowej aplikacji):

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

Poniższy przykład jest analogiczny do poprzedniego przykładu, z wyjątkiem tego, że:

* Kod JavaScript ([interfejs API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API)) służy do przesyłania danych formularza.
* Nie istnieje weryfikacja.

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

Aby wykonać formularz POST w języku JavaScript dla klientów, którzy [nie obsługują interfejsu API pobierania](https://caniuse.com/#feat=fetch), należy użyć jednej z następujących metod:

* Użyj wypełniania pobierania (na przykład [window. Fetch Fill (GitHub/Fetch)](https://github.com/github/fetch)).
* Użyj polecenia `XMLHttpRequest`. Na przykład:

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

W celu obsługi przekazywania plików formularze HTML muszą określać typ kodowania ( `enctype` ) `multipart/form-data` .

Aby `files` element wejściowy obsługiwał przekazywanie wielu plików, podaj `multiple` atrybut w `<input>` elemencie:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Do poszczególnych plików przekazanych do serwera można uzyskać dostęp za pośrednictwem [powiązania modelu](xref:mvc/models/model-binding) przy użyciu polecenia <xref:Microsoft.AspNetCore.Http.IFormFile> . Przykładowa aplikacja pokazuje wiele buforowanych operacji przekazywania plików dla scenariuszy bazy danych i magazynu fizycznego.

<a name="filename2"></a>

> [!WARNING]
> **Nie** należy używać `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> innej niż do wyświetlania i rejestrowania. Podczas wyświetlania lub rejestrowania, kod HTML koduje nazwę pliku. Osoba atakująca może dostarczyć złośliwą nazwę pliku, w tym pełne ścieżki lub ścieżki względne. Aplikacje powinny:
>
> * Usuń ścieżkę z nazwy pliku dostarczonej przez użytkownika.
> * Zapisz plik w formacie HTML, który został usunięty z ścieżką dla interfejsu użytkownika lub rejestrowania.
> * Wygeneruj nową losową nazwę pliku dla magazynu.
>
> Poniższy kod usuwa ścieżkę z nazwy pliku:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Przykłady udostępnione w ten sposób nie uwzględniają zagadnień związanych z bezpieczeństwem. Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Zagadnienia dotyczące bezpieczeństwa](#security-considerations)
> * [Walidacja](#validation)

Podczas przekazywania plików przy użyciu powiązania modelu i <xref:Microsoft.AspNetCore.Http.IFormFile> , Metoda akcji może przyjmować:

* Jeden <xref:Microsoft.AspNetCore.Http.IFormFile> .
* Dowolne z następujących kolekcji, które reprezentują kilka plików:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [Staw](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Powiązanie dopasowuje pliki formularza według nazwy. Na przykład `name` wartość HTML w `<input type="file" name="formFile">` musi być zgodna z parametrem lub właściwością języka C# ( `FormFile` ). Aby uzyskać więcej informacji, zobacz sekcję [dopasowanie wartości atrybutu do nazwy parametru w sekcji Metoda post](#match-name-attribute-value-to-parameter-name-of-post-method) .

Poniższy przykład:

* Pętle przez co najmniej jeden przekazany plik.
* Używa funkcji [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby zwrócić pełną ścieżkę do pliku, łącznie z nazwą pliku. 
* Zapisuje pliki w lokalnym systemie plików przy użyciu nazwy pliku wygenerowanej przez aplikację.
* Zwraca łączną liczbę i rozmiar przekazanych plików.

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

Użyj `Path.GetRandomFileName` do wygenerowania nazwy pliku bez ścieżki. W poniższym przykładzie ścieżka jest uzyskiwana z konfiguracji:

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

Ścieżka przenoszona do <xref:System.IO.FileStream> *musi* zawierać nazwę pliku. Jeśli nie podano nazwy pliku, <xref:System.UnauthorizedAccessException> jest generowany w czasie wykonywania.

Pliki przekazane przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> techniki są buforowane w pamięci lub na dysku na serwerze przed przetworzeniem. Wewnątrz metody akcji <xref:Microsoft.AspNetCore.Http.IFormFile> zawartość jest dostępna jako <xref:System.IO.Stream> . Oprócz lokalnego systemu plików można zapisywać pliki w udziale sieciowym lub w usłudze magazynu plików, na przykład w [usłudze Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Aby uzyskać inny przykład pętli dla wielu plików na potrzeby przekazywania i używania bezpiecznych nazw plików, zobacz *Pages/BufferedMultipleFileUploadPhysical. cshtml. cs* w przykładowej aplikacji.

> [!WARNING]
> [Ścieżka. GetTempFileName](xref:System.IO.Path.GetTempFileName*) zgłasza, <xref:System.IO.IOException> że tworzone są więcej niż 65 535 plików bez usuwania poprzednich plików tymczasowych. Limit 65 535 plików jest limitem dla serwera. Aby uzyskać więcej informacji na temat tego limitu dla systemu operacyjnego Windows, zobacz uwagi w następujących tematach:
>
> * [GetTempFileNameA, funkcja](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Przekazywanie małych plików z buforowanym powiązaniem modelu z bazą danych

Aby przechowywać dane binarne pliku w bazie danych przy użyciu [Entity Framework](/ef/core/index), zdefiniuj <xref:System.Byte> Właściwość Array dla jednostki:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Określ właściwość modelu strony dla klasy, która zawiera <xref:Microsoft.AspNetCore.Http.IFormFile> :

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
> <xref:Microsoft.AspNetCore.Http.IFormFile> można używać bezpośrednio jako parametru metody akcji lub jako powiązanej właściwości modelu. W poprzednim przykładzie użyto powiązanej właściwości modelu.

`FileUpload`Jest używana w Razor formularzu stron:

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

Gdy formularz zostanie opublikowany na serwerze, skopiuj <xref:Microsoft.AspNetCore.Http.IFormFile> do strumienia i Zapisz go jako tablicę bajtową w bazie danych. W poniższym przykładzie `_dbContext` jest przechowywany kontekst bazy danych aplikacji:

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

Poprzedni przykład przypomina scenariusz przedstawiony w przykładowej aplikacji:

* *Pages/BufferedSingleFileUploadDb. cshtml*
* *Strony/BufferedSingleFileUploadDb. cshtml. cs*

> [!WARNING]
> Należy zachować ostrożność podczas przechowywania danych binarnych w relacyjnych bazach danych, ponieważ może to mieć negatywny wpływ na wydajność.
>
> Nie używaj ani nie ufaj `FileName` właściwości <xref:Microsoft.AspNetCore.Http.IFormFile> bez sprawdzania poprawności. `FileName`Właściwość powinna być używana tylko do celów wyświetlania i tylko po kodowaniu html.
>
> Podane przykłady nie uwzględniają zagadnień związanych z zabezpieczeniami. Dodatkowe informacje są dostarczane przez następujące sekcje i [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Zagadnienia dotyczące bezpieczeństwa](#security-considerations)
> * [Walidacja](#validation)

### <a name="upload-large-files-with-streaming"></a>Przekazywanie dużych plików strumieniowo

Poniższy przykład ilustruje sposób użycia języka JavaScript do przesyłania strumieniowego pliku do akcji kontrolera. Token antysfałszowany pliku jest generowany przy użyciu niestandardowego atrybutu filtru i przekazywać do nagłówków HTTP klienta zamiast w treści żądania. Ponieważ metoda akcji przetwarza przekazane dane bezpośrednio, powiązanie modelu formularza jest wyłączone przez inny filtr niestandardowy. W ramach akcji zawartość formularza jest odczytywana przy użyciu `MultipartReader` , który odczytuje każdą osobę `MultipartSection` , przetwarza plik lub zapisuje zawartość odpowiednio do potrzeb. Po odczytaniu sekcji wieloczęściowej akcja wykonuje własne powiązanie modelu.

Początkowa odpowiedź na stronę ładuje formularz i zapisuje token antysfałszowany w cookie (za pośrednictwem `GenerateAntiforgeryTokenCookieAttribute` atrybutu). Ten atrybut używa wbudowanej [obsługi przed fałszowaniem](xref:security/anti-request-forgery) ASP.NET Core, aby ustawić cookie token żądania:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Służy `DisableFormValueModelBindingAttribute` do wyłączania powiązania modelu:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

W przykładowej aplikacji `GenerateAntiforgeryTokenCookieAttribute` i `DisableFormValueModelBindingAttribute` są stosowane jako filtry do modeli aplikacji strony `/StreamedSingleFileUploadDb` i w ramach `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor Konwencji stron](xref:razor-pages/razor-pages-conventions):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

Ponieważ powiązanie modelu nie odczytuje formularza, parametry, które są powiązane z formularza nie są powiązane (zapytania, trasy i nagłówki nadal pracują). Metoda akcji działa bezpośrednio z `Request` właściwością. `MultipartReader`Jest używany do odczytywania każdej sekcji. Dane klucza/wartości są przechowywane w `KeyValueAccumulator` . Po odczytaniu wieloczęściowych sekcji zawartość `KeyValueAccumulator` jest używana do powiązania danych formularza z typem modelu.

Pełna `StreamingController.UploadDatabase` Metoda przesyłania strumieniowego do bazy danych z EF Core:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*Narzędzia/MultipartRequestHelper. cs*):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Pełna `StreamingController.UploadPhysical` Metoda przesyłania strumieniowego do lokalizacji fizycznej:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

W przykładowej aplikacji sprawdzanie poprawności jest obsługiwane przez program `FileHelpers.ProcessStreamedFile` .

## <a name="validation"></a>Walidacja

Klasa przykładowej aplikacji `FileHelpers` pokazuje kilka testów dla buforowanych <xref:Microsoft.AspNetCore.Http.IFormFile> i przesyłanych strumieniowo przekazywania plików. Aby przetwarzać <xref:Microsoft.AspNetCore.Http.IFormFile> buforowane operacje przekazywania plików w aplikacji przykładowej, zobacz `ProcessFormFile` metodę w pliku *Utilities/FileHelpers. cs* . Aby można było przetwarzać pliki przesyłane strumieniowo, zobacz `ProcessStreamedFile` metodę w tym samym pliku.

> [!WARNING]
> Metody przetwarzania walidacji przedstawione w przykładowej aplikacji nie skanują zawartości przekazanych plików. W większości scenariuszy produkcyjnych do pliku jest używany interfejs API skanera wirusów/złośliwego oprogramowania przed udostępnieniem go użytkownikom lub innym systemom.
>
> Chociaż przykład tematu zawiera przykładowy technikę sprawdzania poprawności, nie należy implementować `FileHelpers` klasy w aplikacji produkcyjnej, chyba że:
>
> * W pełni zapoznaj się z implementacją.
> * Zmodyfikuj implementację odpowiednio do środowiska i specyfikacji aplikacji.
>
> **Nigdy nie należy wdrażać kodu zabezpieczeń w aplikacji bez konieczności ich rozwiązywania.**

### <a name="content-validation"></a>Weryfikacja zawartości

**Użyj interfejsu API skanowania wirusa/złośliwego oprogramowania innej firmy dla przekazanej zawartości.**

Skanowanie plików wymaga użycia zasobów serwera w scenariuszach o dużych ilościach. Jeśli wydajność przetwarzania żądań jest zmniejszana ze względu na skanowanie plików, rozważ odciążenie pracy skanowania do [usługi w tle](xref:fundamentals/host/hosted-services), prawdopodobnie usługi uruchomionej na serwerze innym niż serwer aplikacji. Zwykle przekazane pliki są przechowywane w obszarze poddany kwarantannie, dopóki skaner wirusów w tle nie sprawdzi ich. Gdy plik kończy się, plik zostanie przeniesiony do normalnej lokalizacji przechowywania plików. Te kroki są zwykle wykonywane w połączeniu z rekordem bazy danych, który wskazuje na stan skanowania pliku. Korzystając z takiego podejścia, aplikacja i serwer aplikacji pozostają skoncentrowane na odpowiedzi na żądania.

### <a name="file-extension-validation"></a>Weryfikacja rozszerzenia pliku

Rozszerzenie przekazanego pliku powinno być sprawdzane względem listy dozwolonych rozszerzeń. Na przykład:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Walidacja podpisu pliku

Sygnatura pliku jest określana przez pierwsze kilka bajtów na początku pliku. Te bajty mogą być używane do wskazywania, czy rozszerzenie jest zgodne z zawartością pliku. Przykładowa aplikacja sprawdza podpisy plików dla kilku popularnych typów plików. W poniższym przykładzie podpis pliku dla obrazu JPEG jest sprawdzany pod kątem pliku:

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

Aby uzyskać dodatkowe podpisy plików, zapoznaj się z [bazami danych sygnatury plików](https://www.filesignatures.net/) i oficjalnymi specyfikacjami plików.

### <a name="file-name-security"></a>Zabezpieczenia nazw plików

Nigdy nie należy używać nazwy pliku dostarczonej przez klienta do zapisywania plików w magazynie fizycznym. Utwórz bezpieczną nazwę pliku dla pliku przy użyciu [ścieżki. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) lub [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) , aby utworzyć pełną ścieżkę (łącznie z nazwą pliku) dla magazynu tymczasowego.

Razor automatycznie koduje wartości właściwości w kodzie HTML do wyświetlenia. Następujący kod jest bezpieczny w użyciu:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Poza Razor , zawsze <xref:System.Net.WebUtility.HtmlEncode*> zawartość nazwy pliku z żądania użytkownika.

Wiele implementacji musi zawierać sprawdzenie, czy plik istnieje; w przeciwnym razie plik zostanie zastąpiony przez plik o tej samej nazwie. Podaj dodatkową logikę, aby spełnić wymagania dotyczące Twojej aplikacji.

### <a name="size-validation"></a>Sprawdzanie poprawności rozmiaru

Ogranicz rozmiar przekazanych plików.

W przykładowej aplikacji rozmiar pliku jest ograniczony do 2 MB (wyrażony w bajtach). Limit jest dostarczany przez [konfigurację](xref:fundamentals/configuration/index) z *appsettings.json* pliku:

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit`Jest wstrzykiwana do `PageModel` klas:

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

Gdy rozmiar pliku przekracza limit, plik zostanie odrzucony:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Dopasuj wartość atrybutu Name do nazwy parametru metody POST

W nie Razor formularzach, które publikują dane formularza lub używają `FormData` bezpośrednio języka JavaScript, nazwa określona w elemencie formularza lub `FormData` musi być zgodna z nazwą parametru w akcji kontrolera.

W poniższym przykładzie:

* W przypadku używania `<input>` elementu `name` atrybut ma ustawioną wartość `battlePlans` :

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* W przypadku używania `FormData` w języku JavaScript nazwa jest ustawiana na wartość `battlePlans` :

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Użyj zgodnej nazwy dla parametru metody C# ( `battlePlans` ):

* Dla Razor metody obsługi strony strony o nazwie `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Dla metody akcji składnika MVC Controller:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Konfiguracja serwera i aplikacji

### <a name="multipart-body-length-limit"></a>Limit długości treści wieloczęściowej

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> ustawia limit długości każdej wieloczęściowej treści. Sekcje formularza, które przekraczają ten limit, generują <xref:System.IO.InvalidDataException> podczas analizowania. Wartość domyślna to 134 217 728 (128 MB). Dostosuj limit przy użyciu <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> Ustawienia w `Startup.ConfigureServices` :

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

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> służy do ustawiania <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> dla jednej strony lub akcji.

W Razor aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :

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

W Razor aplikacji strony lub aplikacji MVC Zastosuj filtr do modelu strony lub metody akcji:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Maksymalny rozmiar treści żądania Kestrel

W przypadku aplikacji hostowanych przez Kestrel domyślny maksymalny rozmiar treści żądania to 30 000 000 bajtów, czyli około 28,6 MB. Dostosuj limit przy użyciu opcji serwera [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:

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

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> służy do ustawiania [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) dla pojedynczej strony lub akcji.

W Razor aplikacji strony Zastosuj filtr z [Konwencją](xref:razor-pages/razor-pages-conventions) w `Startup.ConfigureServices` :

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

W Razor aplikacji strony lub aplikacji MVC Zastosuj filtr do klasy procedury obsługi stron lub metody akcji:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>Inne limity Kestrel

Inne limity Kestrel mogą dotyczyć aplikacji hostowanych przez Kestrel:

* [Maksymalna liczba połączeń klienta](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Stawki danych żądań i odpowiedzi](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

Domyślny limit żądań ( `maxAllowedContentLength` ) to 30 000 000 bajtów, czyli około 28,6 MB. Dostosuj limit w `web.config` pliku. W poniższym przykładzie limit jest ustawiony na 50 MB (52 428 800 bajtów):

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

To `maxAllowedContentLength` ustawienie dotyczy tylko usług IIS. Aby uzyskać więcej informacji, zobacz [limity `<requestLimits>` żądań ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Zwiększ maksymalny rozmiar treści żądania dla żądania HTTP przez ustawienie <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> w `Startup.ConfigureServices` . W poniższym przykładzie limit jest ustawiony na 50 MB (52 428 800 bajtów):

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/iis/index#iis-options>.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Poniżej przedstawiono niektóre typowe problemy, które można napotkać podczas pracy z przekazywaniem plików i ich możliwymi rozwiązaniami.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Nie znaleziono błędu w przypadku wdrożenia na serwerze usług IIS

Następujący błąd wskazuje, że przekazany plik przekracza skonfigurowaną długość zawartości serwera:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Aby uzyskać więcej informacji, zobacz sekcję [usług IIS](#iis) .

### <a name="connection-failure"></a>Błąd połączenia

Wystąpił błąd połączenia i połączenie z serwerem resetowania prawdopodobnie wskazuje, że przekazany plik przekracza maksymalny rozmiar treści żądania Kestrel. Aby uzyskać więcej informacji, zobacz sekcję [Maksymalny rozmiar treści żądania Kestrel](#kestrel-maximum-request-body-size) . Limity połączeń klienta Kestrel mogą również wymagać korekty.

### <a name="null-reference-exception-with-iformfile"></a>Wyjątek odwołania o wartości null z IFormFile

Jeśli kontroler akceptuje przekazane pliki przy użyciu <xref:Microsoft.AspNetCore.Http.IFormFile> , ale wartość jest `null` , upewnij się, że w formularzu HTML jest określana `enctype` wartość `multipart/form-data` . Jeśli ten atrybut nie jest ustawiony dla `<form>` elementu, przekazywanie pliku nie wystąpi i wszystkie powiązane <xref:Microsoft.AspNetCore.Http.IFormFile> argumenty są `null` . Sprawdź również, czy [Nazwa przekazywania w postaci danych jest zgodna z nazewnictwem aplikacji](#match-name-attribute-value-to-parameter-name-of-post-method).

### <a name="stream-was-too-long"></a>Strumień był zbyt długi

Przykłady w tym temacie polegają na zapełnieniu <xref:System.IO.MemoryStream> zawartości przekazanego pliku. Limit rozmiaru `MemoryStream` wynosi `int.MaxValue` . Jeśli scenariusz przekazywania plików aplikacji wymaga przechowywania zawartości pliku o rozmiarze większym niż 50 MB, użyj alternatywnego podejścia, które nie polega na pojedynczym `MemoryStream` przeniesieniu zawartości przekazanego pliku.

::: moniker-end


## <a name="additional-resources"></a>Dodatkowe zasoby

::: moniker range="< aspnetcore-5.0"
* [Opróżnianie żądań połączenia HTTP](xref:fundamentals/servers/kestrel#http11-request-draining)
::: moniker-end
::: moniker range=">= aspnetcore-5.0"
* [Opróżnianie żądań połączenia HTTP](xref:fundamentals/servers/kestrel/request-draining)
::: moniker-end

* [Przekazywanie plików bez ograniczeń](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [Zabezpieczenia platformy Azure: ramka zabezpieczeń: sprawdzanie poprawności danych wejściowych | Środki zaradcze](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Wzorce projektowe chmury platformy Azure: wzorzec klucza portiera](/azure/architecture/patterns/valet-key)
