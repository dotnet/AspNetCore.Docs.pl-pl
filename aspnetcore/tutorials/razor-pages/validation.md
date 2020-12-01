---
title: Część 8, Dodawanie walidacji
author: rick-anderson
description: Część 8 serii samouczków na Razor stronach.
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
no-loc:
- Index
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f155922c9cb5ea7fdbad0963221ceddd19f4fe60
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96419957"
---
# <a name="part-8-of-tutorial-series-on-no-locrazor-pages"></a>Część 8 serii samouczków na Razor stronach.

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W tej sekcji logika walidacji jest dodawana do `Movie` modelu. Reguły sprawdzania poprawności są wymuszane za każdym razem, gdy użytkownik tworzy lub edytuje film.

## <a name="validation"></a>Walidacja

Kluczową cechą rozwoju oprogramowania jest nazywana [sucha](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D** on't **R** EPEAT **Y** ourself"). Razor Strony zachęcają do programowania, w którym funkcje są określone raz i są widoczne w całej aplikacji. SUCHy może pomóc:

* Zmniejsz ilość kodu w aplikacji.
* Spraw, aby kod był mniej podatny na błędy i łatwiejszy do testowania i konserwowania.

Obsługa walidacji zapewniana przez Razor strony i Entity Framework jest dobrym przykładem zasady sucha:

* Reguły sprawdzania poprawności są deklaratywnie określone w jednym miejscu, w klasie model.
* Reguły są wymuszane wszędzie w aplikacji.

## <a name="add-validation-rules-to-the-movie-model"></a>Dodawanie reguł walidacji do modelu filmu

`DataAnnotations`Przestrzeń nazw zawiera:

* Zestaw wbudowanych atrybutów walidacji, które są stosowane deklaratywnie do klasy lub właściwości.
* Atrybuty formatowania podobne do `[DataType]` formatowania i nie zapewniają weryfikacji.

Zaktualizuj `Movie` klasę, aby skorzystać z wbudowanych `[Required]` `[StringLength]` atrybutów,, `[RegularExpression]` i `[Range]` walidacji.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Atrybuty walidacji określają zachowanie do wymuszania na właściwościach modelu, do których są stosowane:

* `[Required]`Atrybuty i `[MinimumLength]` wskazują, że właściwość musi mieć wartość. Nic nie zapobiega wprowadzaniu przez użytkownika białych znaków w celu zaspokojenia tej walidacji.
* Ten `[RegularExpression]` atrybut służy do ograniczania, jakie znaki mogą być wprowadzane. W powyższym kodzie `Genre` :

  * Należy używać tylko liter.
  * Pierwsza litera musi być wielką literą. Odstępy, cyfry i znaki specjalne są niedozwolone.

* `RegularExpression` `Rating` :

  * Wymaga, aby pierwszy znak był wielką literą.
  * Zezwala na znaki specjalne i cyfry w kolejnych odstępach. "PG-13" jest prawidłowy dla oceny, ale kończy się niepowodzeniem `Genre` .

* Atrybut `[Range]` ogranicza wartość do określonego zakresu.
* `[StringLength]`Atrybut może ustawiać maksymalną długość właściwości ciągu i opcjonalnie jej długość minimalną.
* Typy wartości, takie jak,,,, `decimal` `int` są z `float` `DateTime` założenia wymagane i nie potrzebują `[Required]` atrybutu.

Poprzednie reguły walidacji są używane w celu pokazania, ale nie są optymalne dla systemu produkcyjnego. Na przykład powyższe zapobiega wprowadzaniu filmu z tylko dwoma znakami i nie zezwala na znaki specjalne w `Genre` .

Automatyczne Wymuszanie reguł sprawdzania poprawności przez ASP.NET Core ułatwia:

* Pomaga zwiększyć niezawodność aplikacji.
* Zmniejsz prawdopodobieństwo zapisania nieprawidłowych danych w bazie danych.

### <a name="validation-error-ui-in-no-locrazor-pages"></a>Interfejs użytkownika błędu walidacji na Razor stronach

Uruchom aplikację i przejdź do stron/filmów.

Wybierz łącze **Utwórz nowy** . Wypełnij formularz z nieprawidłowymi wartościami. Gdy program jQuery po stronie klienta wykryje błąd, zostanie wyświetlony komunikat o błędzie.

![Formularz widoku filmu z wieloma błędami walidacji po stronie klienta jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Zwróć uwagę, jak formularz automatycznie renderuje komunikat o błędzie walidacji w każdym polu zawierającym nieprawidłową wartość. Błędy są wymuszane po stronie klienta, przy użyciu języków JavaScript i jQuery oraz po stronie serwera, gdy użytkownik ma wyłączone JavaScript.

Znacząca korzyść polega na tym, że zmiany kodu **nie** były wymagane na stronach tworzenia i edytowania. Po zastosowaniu adnotacji danych do modelu interfejs użytkownika weryfikacji został włączony. RazorStrony utworzone w tym samouczku automatycznie pobierają reguły sprawdzania poprawności, używając atrybutów walidacji we właściwościach `Movie` klasy modelu. Sprawdzanie poprawności testu za pomocą strony Edycja, to samo sprawdzanie poprawności jest stosowane.

Dane formularza nie są ogłaszane na serwerze, dopóki nie zostaną wykryte błędy weryfikacji po stronie klienta. Sprawdź, czy dane formularza nie zostały ogłoszone przy użyciu co najmniej jednej z następujących metod:

* Umieść punkt przerwania w `OnPostAsync` metodzie. Prześlij formularz, wybierając pozycję **Utwórz** lub **Zapisz**. Punkt przerwania nigdy nie trafi.
* Użyj [Narzędzia programu Fiddler](https://www.telerik.com/fiddler).
* Użyj narzędzi deweloperskich przeglądarki do monitorowania ruchu sieciowego.

### <a name="server-side-validation"></a>Weryfikacja po stronie serwera

Gdy język JavaScript jest wyłączony w przeglądarce, przesłanie formularza z błędami spowoduje opublikowanie na serwerze.

Opcjonalna, testowa weryfikacja po stronie serwera:

1. Wyłącz język JavaScript w przeglądarce. Język JavaScript można wyłączyć za pomocą narzędzi deweloperskich przeglądarki. Jeśli nie można wyłączyć języka JavaScript w przeglądarce, wypróbuj inną przeglądarkę.
1. Ustaw punkt przerwania w `OnPostAsync` metodzie strony Utwórz lub Edytuj.
1. Prześlij formularz z nieprawidłowymi danymi.
1. Sprawdź, czy stan modelu jest nieprawidłowy:

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
Alternatywnie można [wyłączyć weryfikację po stronie klienta na serwerze](xref:mvc/models/validation#disable-client-side-validation).

Poniższy kod przedstawia część strony *Create. cshtml* podświetloną wcześniej w samouczku. Jest on używany przez strony Tworzenie i edytowanie na:

* Wyświetlanie formularza początkowego.
* Ponownie Wyświetl formularz w przypadku błędu.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

[Pomocnik tagu wejściowego](xref:mvc/views/working-with-forms) używa atrybutów [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) i tworzy atrybuty HTML, które są zbędne do walidacji jQuery po stronie klienta. [Pomocnik tagów walidacji](xref:mvc/views/working-with-forms#the-validation-tag-helpers) wyświetla błędy walidacji. Aby uzyskać więcej informacji, zobacz [Walidacja](xref:mvc/models/validation) .

Na stronach tworzenie i edytowanie nie są dostępne żadne reguły sprawdzania poprawności. Reguły walidacji i ciągi błędów są określone tylko w `Movie` klasie. Te reguły sprawdzania poprawności są automatycznie stosowane do Razor stron, które edytują `Movie` model.

Gdy wymagana jest zmiana logiki walidacji, jest ona wykonywana tylko w modelu. Walidacja jest stosowana spójnie w całej aplikacji. Logika walidacji jest definiowana w jednym miejscu. Sprawdzanie poprawności w jednym miejscu pomaga zachować czysty kod i ułatwić jego utrzymywanie i aktualizowanie.

## <a name="use-datatype-attributes"></a>Używanie atrybutów DataType

Zapoznaj się z `Movie` klasą. `System.ComponentModel.DataAnnotations`Przestrzeń nazw zawiera atrybuty formatowania oprócz wbudowanego zestawu atrybutów walidacji. Atrybut `[DataType]` jest stosowany do właściwości `ReleaseDate` i `Price`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

`[DataType]`Atrybuty zapewniają:

* Wskazówki dotyczące aparatu widoku do formatowania danych.
* Dostarcza atrybuty, takie jak `<a>` adresy URL i `<a href="mailto:EmailAddress.com">` wiadomości e-mail.

Użyj `[RegularExpression]` atrybutu, aby sprawdzić poprawność formatu danych. Ten `[DataType]` atrybut służy do określania typu danych, który jest bardziej szczegółowy niż typ wewnętrzny bazy danych. `[DataType]` atrybuty nie są atrybutami walidacji. W przykładowej aplikacji tylko data jest wyświetlana bez czasu.

`DataType`Wyliczenie zawiera wiele typów danych, takich jak `Date` , `Time` ,,, i nie `PhoneNumber` `Currency` `EmailAddress` tylko. 

`[DataType]`Atrybuty:

* Może umożliwić aplikacji automatyczne udostępnianie funkcji specyficznych dla typu. Na przykład `mailto:` można utworzyć link dla `DataType.EmailAddress` .
* Może udostępniać selektor daty `DataType.Date` w przeglądarkach, które obsługują HTML5.
* Emituje kod HTML 5 `data-` , wymawiany "kreska danych", atrybuty zużywane przez przeglądarki HTML 5.
* **Nie** należy podawać żadnej weryfikacji.

`DataType.Date` nie określa formatu wyświetlanej daty. Domyślnie pole dane jest wyświetlane zgodnie z domyślnymi formatami opartymi na serwerze `CultureInfo` .

`[Column(TypeName = "decimal(18, 2)")]`Adnotacja danych jest wymagana, aby Entity Framework Core prawidłowo mapować `Price` do waluty w bazie danych. Aby uzyskać więcej informacji, zobacz [typy danych](/ef/core/modeling/relational/data-types).

Ten `[DisplayFormat]` atrybut służy do jawnego określenia formatu daty:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode`Ustawienie określa, że formatowanie zostanie zastosowane, gdy wartość zostanie wyświetlona do edycji. Takie zachowanie może nie być potrzebne w przypadku niektórych pól. Na przykład w przypadku wartości walut symbol waluty zwykle nie jest wymagany w interfejsie użytkownika edycji.

Ten `[DisplayFormat]` atrybut może być używany przez siebie, ale zazwyczaj dobrym pomysłem jest użycie `[DataType]` atrybutu. Ten `[DataType]` atrybut przekazuje semantykę danych w przeciwieństwie do sposobu renderowania na ekranie. Ten `[DataType]` atrybut zapewnia następujące korzyści, które nie są dostępne w programie `[DisplayFormat]` :

* Przeglądarka może włączyć funkcje HTML5, na przykład wyświetlić kontrolkę kalendarz, symbol waluty odpowiedni dla ustawień regionalnych, linki e-mail itp.
* Domyślnie przeglądarka renderuje dane przy użyciu poprawnego formatu na podstawie jego ustawień regionalnych.
* Ten `[DataType]` atrybut może umożliwić usłudze ASP.NET Core Framework wybranie odpowiedniego szablonu pola w celu renderowania danych. `DisplayFormat`, Jeśli używany przez siebie, używa szablonu ciągu.

**Uwaga:** Walidacja jQuery nie działa z `[Range]` atrybutem i `DateTime` . Na przykład poniższy kod zawsze będzie wyświetlał błąd walidacji po stronie klienta, nawet wtedy, gdy data jest w określonym zakresie:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Najlepszym rozwiązaniem jest uniknięcie kompilowania dat twardych w modelach, dlatego przy użyciu `[Range]` atrybutu i `DateTime` nie jest to odradzane. Użyj [konfiguracji](xref:fundamentals/configuration/index) dla zakresów dat i innych wartości, które podlegają częstym zmianom, zamiast określania ich w kodzie.

Poniższy kod ilustruje łączenie atrybutów w jednym wierszu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Wprowadzenie do Razor Na stronach i EF Core](xref:data/ef-rp/intro) są wyświetlane zaawansowane operacje EF Core z Razor stronami.

### <a name="apply-migrations"></a>Zastosuj migracje

Adnotacje zastosowane do klasy zmieniają schemat. Na przykład, adnotacje zastosowane do `Title` pola:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* Ogranicza znaki do 60.
* Nie zezwala na `null` wartość.

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

`Movie`Tabela ma obecnie następujący schemat:

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

`Update-Database` uruchamia `Up` metody `New_DataAnnotations` klasy. Przeanalizuj metodę `Up`:

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

Aby uzyskać informacje na temat wdrażania na platformie Azure, zobacz [Samouczek: Tworzenie aplikacji ASP.NET Core na platformie Azure przy użyciu SQL Database](/azure/app-service/tutorial-dotnetcore-sqldb-app).

Dziękujemy za zakończenie tego wprowadzenia do Razor stron. [Wprowadzenie do Razor Strony i EF Core](xref:data/ef-rp/intro) są doskonałym zaobserwują się z tym samouczkiem.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [Poprzedni: Dodaj nowe pole](xref:tutorials/razor-pages/new-field)
