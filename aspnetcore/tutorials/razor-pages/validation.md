---
title: Dodawanie sprawdzania poprawności do ASP.NET podstawowej strony razor
author: rick-anderson
description: Dowiedz się, jak dodać sprawdzanie poprawności do strony Razor w ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f283234ed8a32dc9b7904bc6fee1cc9c04741029
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666020"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a>Dodawanie sprawdzania poprawności do ASP.NET podstawowej strony razor

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W tej sekcji logiki sprawdzania `Movie` poprawności jest dodawany do modelu. Reguły sprawdzania poprawności są wymuszane za każdym razem, gdy użytkownik tworzy lub edytuje film.

## <a name="validation"></a>Walidacja

Kluczowym elementem rozwoju oprogramowania jest [nazwa DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Yself").** Razor Pages zachęca do tworzenia, gdzie funkcjonalność jest określona raz, i jest to odzwierciedlone w całej aplikacji. DRY może pomóc:

* Zmniejsz ilość kodu w aplikacji.
* Ułatw kod mniej podatne na błędy i łatwiejsze do testowania i konserwacji.

Obsługa walidacji zapewniana przez strony Razor Pages i entity framework jest dobrym przykładem zasady DRY. Reguły sprawdzania poprawności są deklaratywnie określone w jednym miejscu (w klasie modelu), a reguły są wymuszane wszędzie w aplikacji.

## <a name="add-validation-rules-to-the-movie-model"></a>Dodawanie reguł sprawdzania poprawności do modelu filmu

Obszar nazw DataAnnotations zawiera zestaw wbudowanych atrybutów sprawdzania poprawności, które są stosowane deklaratywnie do klasy lub właściwości. DataAnnotations zawiera również atrybuty formatowania, takie jak, `DataType` które pomagają w formatowaniu i nie zapewniają sprawdzania poprawności.

Zaktualizuj `Movie` klasę, aby `Required`skorzystać `StringLength` `RegularExpression`z `Range` wbudowanych atrybutów , , i sprawdzania poprawności.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Atrybuty sprawdzania poprawności określają zachowanie, które chcesz wymusić we właściwościach modelu, do których są stosowane:

* `Required` Atrybuty `MinimumLength` i atrybuty wskazują, że właściwość musi mieć wartość; ale nic nie stoi na przeszkodzie, aby użytkownik wejdząc do białego miejsca, aby spełnić tę walidację.
* Atrybut `RegularExpression` jest używany do ograniczania, jakie znaki mogą być wprowadzane. W poprzednim kodzie "Genre":

  * Może używać tylko liter.
  * Pierwsza litera musi być wielką literą. Białe znaki, liczby i znaki specjalne nie są dozwolone.

* "Ocena": `RegularExpression`

  * Wymaga, aby pierwszy znak był wielką literą.
  * Umożliwia znaki specjalne i liczby w kolejnych spacjach. "PG-13" jest ważny dla oceny, ale nie dla "Gatunku".

* Atrybut `Range` ogranicza wartość do określonego zakresu.
* Atrybut `StringLength` umożliwia ustawienie maksymalnej długości właściwości ciągu i opcjonalnie jego minimalnej długości.
* Typy wartości `decimal`(takie `float` `DateTime`jak , `int`, , ) są `[Required]` z natury wymagane i nie potrzebują atrybutu.

Automatyczne wymuszanie reguł sprawdzania poprawności przez ASP.NET Core pomaga uczynić aplikację bardziej niezawodną. Zapewnia również, że nie można zapomnieć, aby sprawdzić poprawność czegoś i przypadkowo wpuścić złe dane do bazy danych.

### <a name="validation-error-ui-in-razor-pages"></a>Interfejs użytkownika błędu sprawdzania poprawności na stronach Razor

Uruchom aplikację i przejdź do strony/filmy.

Wybierz łącze **Utwórz nowy.** Wypełnij formularz z nieprawidłowymi wartościami. Gdy sprawdzanie poprawności po stronie klienta jQuery wykryje błąd, wyświetla komunikat o błędzie.

![Formularz widoku filmu z wieloma błędami sprawdzania poprawności po stronie klienta jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Zwróć uwagę, jak formularz automatycznie renderował komunikat o błędzie sprawdzania poprawności w każdym polu zawierającym nieprawidłową wartość. Błędy są wymuszane zarówno po stronie klienta (przy użyciu Języka JavaScript i jQuery) i po stronie serwera (gdy użytkownik ma wyłączony javascript).

Istotną korzyścią jest to, że na stronach Tworzenie lub edytowanie nie były wymagane **żadne** zmiany kodu. Po zastosowaniu dataannotations do modelu, sprawdzanie poprawności interfejsu użytkownika została włączona. Strony Razor utworzone w tym samouczku automatycznie podniósł reguły sprawdzania poprawności `Movie` (przy użyciu atrybutów sprawdzania poprawności właściwości klasy modelu). Sprawdzanie poprawności testu przy użyciu strony Edytuj, ta sama weryfikacja jest stosowana.

Dane formularza nie są księgowane na serwerze, dopóki nie ma żadnych błędów sprawdzania poprawności po stronie klienta. Sprawdź, czy dane formularza nie są publikowane przez co najmniej jedną z następujących metod:

* Umieść punkt przerwania `OnPostAsync` w metodzie. Prześlij formularz (wybierz **utwórz** lub **zapisz).** Break point nigdy nie zostanie trafiony.
* Użyj [narzędzia Skrzypek](https://www.telerik.com/fiddler).
* Narzędzia programistyczne przeglądarki są używane do monitorowania ruchu sieciowego.

### <a name="server-side-validation"></a>Sprawdzanie poprawności po stronie serwera

Gdy JavaScript jest wyłączony w przeglądarce, przesłanie formularza z błędami zostanie zaksięgowany na serwerze.

Opcjonalne sprawdzanie poprawności po stronie serwera:

* Wyłącz obsługę JavaScript w przeglądarce. Możesz wyłączyć JavaScript za pomocą narzędzi programistycznych przeglądarki. Jeśli nie możesz wyłączyć javascriptu w przeglądarce, spróbuj użyć innej przeglądarki.
* Ustaw punkt przerwania `OnPostAsync` w metodzie strony Utwórz lub Edytuj.
* Prześlij formularz z nieprawidłowymi danymi.
* Sprawdź, czy stan modelu jest nieprawidłowy:

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
Alternatywnie można [wyłączyć sprawdzanie poprawności po stronie klienta na serwerze](xref:mvc/models/validation#disable-client-side-validation).

Poniższy kod przedstawia część *Create.cshtml* strony szkieletu wcześniej w samouczku. Jest on używany przez strony Tworzenie i edytowanie do wyświetlania formularza początkowego i ponownego wyświetlania formularza w przypadku błędu.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

[Pomocnik znacznika wejściowego](xref:mvc/views/working-with-forms) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML potrzebne do sprawdzania poprawności jQuery po stronie klienta. [Pomocnik tagu sprawdzania poprawności](xref:mvc/views/working-with-forms#the-validation-tag-helpers) wyświetla błędy sprawdzania poprawności. Zobacz [Sprawdzanie poprawności,](xref:mvc/models/validation) aby uzyskać więcej informacji.

Strony Tworzenie i edytowanie nie mają w nich reguł sprawdzania poprawności. Reguły sprawdzania poprawności i ciągi błędów są określone tylko w `Movie` klasie. Te reguły sprawdzania poprawności są automatycznie stosowane `Movie` do stron Razor, które edytują model.

Gdy logika sprawdzania poprawności musi się zmienić, odbywa się tylko w modelu. Sprawdzanie poprawności jest stosowane konsekwentnie w całej aplikacji (logika sprawdzania poprawności jest zdefiniowana w jednym miejscu). Sprawdzanie poprawności w jednym miejscu pomaga utrzymać kod w czystości i ułatwia utrzymanie i aktualizację.

## <a name="using-datatype-attributes"></a>Korzystanie z atrybutów datatype

Sprawdź `Movie` klasę. Obszar `System.ComponentModel.DataAnnotations` nazw udostępnia atrybuty formatowania oprócz wbudowanego zestawu atrybutów sprawdzania poprawności. Atrybut `DataType` jest stosowany do właściwości `ReleaseDate` i `Price`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Atrybuty `DataType` zawierają tylko wskazówki dla aparatu widoku, aby sformatować dane (i dostarcza atrybuty, takie jak `<a>` adres URL i `<a href="mailto:EmailAddress.com">` dla poczty e-mail). Użyj `RegularExpression` atrybutu, aby sprawdzić poprawność formatu danych. Atrybut `DataType` jest używany do określenia typu danych, który jest bardziej szczegółowy niż typ wewnętrznego bazy danych. `DataType`atrybuty nie są atrybutami sprawdzania poprawności. W przykładowej aplikacji wyświetlana jest tylko data bez czasu.

Wyliczenie `DataType` zawiera wiele typów danych, takich jak Data, Godzina, Numer telefonu, Waluta, EmailAddress i inne. Atrybut `DataType` może również włączyć aplikację, aby automatycznie udostępniać funkcje specyficzne dla typu. Na przykład `mailto:` można utworzyć łącze dla pliku `DataType.EmailAddress`. Selektor daty może być `DataType.Date` podany w przeglądarkach obsługujących html5. Atrybuty `DataType` emitują atrybuty HTML 5 `data-` (wymawiane rozdzielenie danych), które zużywają przeglądarki HTML 5. Atrybuty `DataType` **nie** zapewniają sprawdzania poprawności.

`DataType.Date`nie określa formatu daty, która jest wyświetlana. Domyślnie pole danych jest wyświetlane zgodnie z domyślnymi formatami `CultureInfo`opartymi na serwerze .

Adnotacja `[Column(TypeName = "decimal(18, 2)")]` danych jest wymagana, aby entity `Price` Framework Core można poprawnie mapować do waluty w bazie danych. Aby uzyskać więcej informacji, zobacz [Typy danych](/ef/core/modeling/relational/data-types).

Atrybut `DisplayFormat` jest używany do jawnego określenia formatu daty:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

Ustawienie `ApplyFormatInEditMode` określa, że formatowanie powinno być stosowane, gdy wartość jest wyświetlana do edycji. Możesz nie chcieć tego zachowania dla niektórych pól. Na przykład w wartościach walutowych prawdopodobnie symbol waluty w interfejsie użytkownika edycji jest prawdopodobnie nieokiedy.

Atrybut `DisplayFormat` może być używany przez siebie, ale ogólnie jest to `DataType` dobry pomysł, aby użyć atrybutu. Atrybut `DataType` przekazuje semantykę danych, w przeciwieństwie do sposobu renderowania na ekranie i zapewnia następujące korzyści, które nie są uchybienia z DisplayFormat:

* Przeglądarka może włączyć funkcje HTML5 (na przykład, aby wyświetlić formant kalendarza, symbol waluty odpowiednie dla ustawień regionalnych, linki e-mail, itp.)
* Domyślnie przeglądarka będzie renderować dane przy użyciu odpowiedniego formatu na podstawie ustawień regionalnych.
* Atrybut `DataType` może włączyć ASP.NET Core framework, aby wybrać odpowiedni szablon pola do renderowania danych. Jeśli `DisplayFormat` jest używany przez siebie używa szablonu ciągu.

Uwaga: sprawdzanie poprawności jQuery nie `Range` działa `DateTime`z atrybutem i . Na przykład następujący kod zawsze będzie wyświetlać błąd sprawdzania poprawności po stronie klienta, nawet jeśli data znajduje się w określonym zakresie:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Zazwyczaj nie jest dobrą praktyką do kompilowania twardych dat `Range` w `DateTime` modelach, więc przy użyciu atrybutu i jest odradzane.

Poniższy kod pokazuje łączenie atrybutów w jednym wierszu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Wprowadzenie do stron Razor i EF Core](xref:data/ef-rp/intro) pokazuje zaawansowane operacje EF Core ze stronami Razor.

### <a name="apply-migrations"></a>Stosowanie migracji

DataAnnotations zastosowane do klasy zmienić schemat. Na przykład dataAnnotations stosowane do `Title` pola:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* Ogranicza znaki do 60.
* Nie zezwala na `null` wartość.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Tabela `Movie` ma obecnie następujący schemat:

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

Poprzednie zmiany schematu nie powodują EF zgłosić wyjątek. Jednak utworzyć migrację, więc schemat jest zgodny z modelem.

Z menu **Narzędzia** wybierz polecenie **Menedżer pakietów NuGet > Konsola Menedżera pakietów**.
W pmc wprowadź następujące polecenia:

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

`Update-Database`uruchamia `Up` metody `New_DataAnnotations` klasy. Zbadaj `Up` metodę:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

Zaktualizowana `Movie` tabela ma następujący schemat:

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Migracje nie są wymagane dla SQLite.

---

### <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Aby uzyskać informacje na temat wdrażania na platformie Azure, zobacz [Samouczek: Tworzenie aplikacji ASP.NET Core na platformie Azure za pomocą bazy danych SQL.](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

Dziękujemy za ukończenie tego wprowadzenia do stron Razor. [Zacznij ować rezygnować Razor Strony i EF Rdzeń](xref:data/ef-rp/intro) jest an an znakomity następować po ten wychowawczy.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [Wersja tego samouczka w YouTube](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [Poprzedni: Dodawanie nowego pola](xref:tutorials/razor-pages/new-field)
