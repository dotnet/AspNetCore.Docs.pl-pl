---
title: Dodawanie walidacji do strony Razor ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak dodać weryfikację do Razor strony w ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 91f0ac5fcd607f2423f9fc4647413b2bbb2336fc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773778"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a>Dodawanie walidacji do ASP.NET Core stronie Razor

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W tej sekcji logika walidacji jest dodawana `Movie` do modelu. Reguły sprawdzania poprawności są wymuszane za każdym razem, gdy użytkownik tworzy lub edytuje film.

## <a name="validation"></a>Walidacja

Kluczową cechą rozwoju oprogramowania jest nazywana [sucha](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**EPEAT **Y**ourself"). Razor Pages zachęca do programowania, w którym funkcje są określone raz i są widoczne w całej aplikacji. SUCHy może pomóc:

* Zmniejsz ilość kodu w aplikacji.
* Spraw, aby kod był mniej podatny na błędy i łatwiejszy do testowania i konserwowania.

Pomoc techniczna dotycząca walidacji świadczona przez Razor Pages i Entity Framework jest dobrym przykładem zasady SUCHEj. Reguły sprawdzania poprawności są deklaratywnie określone w jednym miejscu (w klasie modelu), a reguły są wymuszane wszędzie w aplikacji.

## <a name="add-validation-rules-to-the-movie-model"></a>Dodawanie reguł walidacji do modelu filmu

Przestrzeń nazw DataAnnotations zawiera zestaw wbudowanych atrybutów walidacji, które są stosowane deklaratywnie do klasy lub właściwości. Adnotacje DataAnnotation zawierają również atrybuty `DataType` formatowania, takie jak pomoc dotycząca formatowania i nie zapewniają weryfikacji.

`Movie` Zaktualizuj klasę, aby skorzystać z wbudowanych atrybutów `Required`, `StringLength`, `RegularExpression`i `Range` walidacji.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Atrybuty walidacji określają zachowanie, które chcesz wymusić na właściwościach modelu, do których są stosowane:

* Atrybuty `Required` i `MinimumLength` wskazują, że właściwość musi mieć wartość; jednak nic nie zapobiega wprowadzaniu przez użytkownika białych znaków w celu zaspokojenia tej walidacji.
* Ten `RegularExpression` atrybut służy do ograniczania, jakie znaki mogą być wprowadzane. W poprzednim kodzie "gatunek":

  * Należy używać tylko liter.
  * Pierwsza litera musi być wielką literą. Odstępy, cyfry i znaki specjalne są niedozwolone.

* `RegularExpression` "Ocena":

  * Wymaga, aby pierwszy znak był wielką literą.
  * Zezwala na znaki specjalne i cyfry w kolejnych odstępach. "PG-13" jest prawidłowy dla oceny, ale kończy się niepowodzeniem dla "gatunku".

* Atrybut `Range` ogranicza wartość do określonego zakresu.
* Ten `StringLength` atrybut pozwala ustawić maksymalną długość właściwości ciągu i opcjonalnie jej długość minimalną.
* Typy wartości (takie jak `decimal`, `int` `float`,, `DateTime`) są z założenia wymagane i nie wymagają `[Required]` atrybutu.

Automatyczne Wymuszanie reguł sprawdzania poprawności przez ASP.NET Core pomaga zwiększyć niezawodność aplikacji. Gwarantuje to również, że nie można zapomnieć, aby zweryfikować coś i przypadkowo umożliwić niewłaściwe dane w bazie danych.

### <a name="validation-error-ui-in-razor-pages"></a>Interfejs użytkownika błędu walidacji w Razor Pages

Uruchom aplikację i przejdź do stron/filmów.

Wybierz łącze **Utwórz nowy** . Wypełnij formularz z nieprawidłowymi wartościami. Gdy program jQuery po stronie klienta wykryje błąd, zostanie wyświetlony komunikat o błędzie.

![Formularz widoku filmu z wieloma błędami walidacji po stronie klienta jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Zwróć uwagę, jak formularz automatycznie renderuje komunikat o błędzie walidacji w każdym polu zawierającym nieprawidłową wartość. Błędy są wymuszane po stronie klienta (przy użyciu języków JavaScript i jQuery) i po stronie serwera (gdy użytkownik ma wyłączony kod JavaScript).

Znacząca korzyść polega na tym, że zmiany kodu **nie** były wymagane na stronach tworzenia i edytowania. Gdy do modelu zastosowano adnotacje DataAnnotations, interfejs użytkownika weryfikacji został włączony. Razor Pages utworzone w tym samouczku automatycznie pobiera reguły walidacji (przy użyciu atrybutów walidacji we właściwościach klasy `Movie` modelu). Sprawdzanie poprawności testu za pomocą strony Edycja, to samo sprawdzanie poprawności jest stosowane.

Dane formularza nie są ogłaszane na serwerze, dopóki nie zostaną wykryte błędy weryfikacji po stronie klienta. Sprawdź, czy dane formularza nie zostały ogłoszone przy użyciu co najmniej jednej z następujących metod:

* Umieść punkt przerwania w `OnPostAsync` metodzie. Prześlij formularz (wybierz pozycję **Utwórz** lub **Zapisz**). Punkt przerwania nigdy nie trafi.
* Użyj [Narzędzia programu Fiddler](https://www.telerik.com/fiddler).
* Użyj narzędzi deweloperskich przeglądarki do monitorowania ruchu sieciowego.

### <a name="server-side-validation"></a>Weryfikacja po stronie serwera

Gdy język JavaScript jest wyłączony w przeglądarce, przesłanie formularza z błędami spowoduje opublikowanie na serwerze.

Opcjonalna, testowa weryfikacja po stronie serwera:

* Wyłącz język JavaScript w przeglądarce. Język JavaScript można wyłączyć przy użyciu narzędzi deweloperskich w przeglądarce. Jeśli nie możesz wyłączyć języka JavaScript w przeglądarce, wypróbuj inną przeglądarkę.
* Ustaw punkt przerwania w `OnPostAsync` metodzie strony Utwórz lub Edytuj.
* Prześlij formularz z nieprawidłowymi danymi.
* Sprawdź, czy stan modelu jest nieprawidłowy:

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
Alternatywnie można [wyłączyć weryfikację po stronie klienta na serwerze](xref:mvc/models/validation#disable-client-side-validation).

Poniższy kod przedstawia część strony *Create. cshtml* podświetloną wcześniej w samouczku. Jest on używany przez strony Tworzenie i edytowanie, aby wyświetlić początkowy formularz i ponownie wyświetlić formularz w przypadku błędu.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

[Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML, które są zbędne do walidacji jQuery po stronie klienta. [Pomocnik tagów walidacji](xref:mvc/views/working-with-forms#the-validation-tag-helpers) wyświetla błędy walidacji. Aby uzyskać więcej informacji, zobacz [Walidacja](xref:mvc/models/validation) .

Na stronach tworzenie i edytowanie nie są dostępne żadne reguły sprawdzania poprawności. Reguły walidacji i ciągi błędów są określone tylko w `Movie` klasie. Te reguły sprawdzania poprawności są automatycznie stosowane do Razor Pages, które `Movie` edytują model.

Gdy wymagana jest zmiana logiki walidacji, jest ona wykonywana tylko w modelu. Walidacja jest stosowana spójnie w całej aplikacji (logika walidacji jest definiowana w jednym miejscu). Sprawdzanie poprawności w jednym miejscu pomaga zachować czysty kod i ułatwić jego utrzymywanie i aktualizowanie.

## <a name="using-datatype-attributes"></a>Używanie atrybutów DataType

Zapoznaj `Movie` się z klasą. `System.ComponentModel.DataAnnotations` Przestrzeń nazw zawiera atrybuty formatowania oprócz wbudowanego zestawu atrybutów walidacji. Atrybut `DataType` jest stosowany do właściwości `ReleaseDate` i `Price`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

`DataType` Atrybuty zawierają tylko wskazówki dla aparatu widoku do formatowania danych (i udostępniają atrybuty, takie jak `<a>` adresy URL i `<a href="mailto:EmailAddress.com">` wiadomości e-mail). Użyj atrybutu `RegularExpression` , aby sprawdzić poprawność formatu danych. Ten `DataType` atrybut służy do określania typu danych, który jest bardziej szczegółowy niż typ wewnętrzny bazy danych. `DataType`atrybuty nie są atrybutami walidacji. W przykładowej aplikacji tylko data jest wyświetlana bez czasu.

`DataType` Wyliczenie zawiera wiele typów danych, takich jak data, godzina, numer telefonu, waluta, EmailAddress i inne. Ten `DataType` atrybut może również umożliwić aplikacji automatyczne udostępnianie funkcji specyficznych dla typu. Na przykład można utworzyć `mailto:` link dla `DataType.EmailAddress`. `DataType.Date` W przeglądarkach, które obsługują HTML5, można podać selektor daty. `DataType` Atrybuty EMITUJĄ pliki HTML 5 `data-` (wymawiane kreski danych) używane przez przeglądarki HTML 5. `DataType` Atrybuty **nie zapewniają żadnej** weryfikacji.

`DataType.Date`nie określa formatu wyświetlanej daty. Domyślnie pole dane jest wyświetlane zgodnie z domyślnymi formatami opartymi na serwerze `CultureInfo`.

Adnotacja `[Column(TypeName = "decimal(18, 2)")]` danych jest wymagana, aby Entity Framework Core prawidłowo mapować `Price` do waluty w bazie danych. Aby uzyskać więcej informacji, zobacz [typy danych](/ef/core/modeling/relational/data-types).

Ten `DisplayFormat` atrybut służy do jawnego określenia formatu daty:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode` Ustawienie określa, że formatowanie ma być stosowane, gdy wartość jest wyświetlana do edycji. Takie zachowanie może nie być konieczne w przypadku niektórych pól. Na przykład w przypadku wartości walut prawdopodobnie nie potrzebujesz symbolu waluty w interfejsie użytkownika edycji.

Ten `DisplayFormat` atrybut może być używany przez siebie, ale zazwyczaj dobrym pomysłem jest użycie `DataType` atrybutu. Ten `DataType` atrybut przekazuje semantykę danych w przeciwieństwie do sposobu renderowania na ekranie i zapewnia następujące korzyści, których nie można uzyskać za pomocą DisplayFormat:

* Przeglądarka może włączać funkcje HTML5 (na przykład w celu wyświetlania kontrolki kalendarza, symbolu waluty właściwej dla ustawień regionalnych, linków e-mail itp.).
* Domyślnie przeglądarka będzie renderować dane przy użyciu poprawnego formatu na podstawie ustawień regionalnych.
* Ten `DataType` atrybut może umożliwić usłudze ASP.NET Core Framework wybranie odpowiedniego szablonu pola w celu renderowania danych. `DisplayFormat` Używany przez siebie sam używa szablonu ciągu.

Uwaga: Walidacja jQuery nie działa z `Range` atrybutem `DateTime`i. Na przykład poniższy kod zawsze będzie wyświetlał błąd walidacji po stronie klienta, nawet wtedy, gdy data jest w określonym zakresie:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Ogólnie rzecz biorąc, nie jest dobrym sposobem kompilowania dat stałych w modelach, dlatego przy użyciu `Range` atrybutu i `DateTime` nie jest to odradzane.

Poniższy kod ilustruje łączenie atrybutów w jednym wierszu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Wprowadzenie do Razor Pages i EF Core](xref:data/ef-rp/intro) przedstawia zaawansowane operacje EF Core z Razor Pages.

### <a name="apply-migrations"></a>Zastosuj migracje

Adnotacje zastosowane do klasy zmieniają schemat. Na przykład, adnotacje zastosowane do `Title` pola:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* Ogranicza znaki do 60.
* Nie zezwala na `null` wartość.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

`Movie` Tabela ma obecnie następujący schemat:

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

Powyższe zmiany schematu nie powodują wygenerowania wyjątku przez EF. Należy jednak utworzyć migrację, aby schemat był spójny z modelem.

W menu **Narzędzia** wybierz kolejno pozycje **menedżer pakietów NuGet > konsola Menedżera pakietów**.
W obszarze PMC wprowadź następujące polecenia:

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

`Update-Database`uruchamia `Up` metody `New_DataAnnotations` klasy. Badanie `Up` metody:

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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

Migracja nie jest wymagana w przypadku oprogramowania SQLite.

---

### <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Aby uzyskać informacje na temat wdrażania na platformie Azure, zobacz [Samouczek: Tworzenie aplikacji ASP.NET Core na platformie Azure przy użyciu SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).

Dziękujemy za zakończenie tego wprowadzenia do Razor stron. [Wprowadzenie do Razor stron i EF Core](xref:data/ef-rp/intro) jest doskonałym postępowaniem z tym samouczkiem.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [Wersja tego samouczka usługi YouTube](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [Poprzedni: Dodawanie nowego pola](xref:tutorials/razor-pages/new-field)
