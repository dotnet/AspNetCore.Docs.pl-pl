---
title: Widoki w ASP.NET Core MVC
author: ardalis
description: Dowiedz się, w jaki sposób widoki obsługują prezentację danych aplikacji i interakcję użytkownika w ASP.NET Core MVC.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/overview
ms.openlocfilehash: bda00a416ac34883e0a70a265156fa3ddcde3c6f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777140"
---
# <a name="views-in-aspnet-core-mvc"></a>Widoki w ASP.NET Core MVC

Przez [Steve Smith](https://ardalis.com/)

W tym dokumencie objaśniono widoki używane w aplikacjach ASP.NET Core MVC. Aby uzyskać informacje na temat Razor Pages, zobacz [wprowadzenie do Razor Pages](xref:razor-pages/index).

W wzorcu Model-View-Controller (MVC) *Widok* obsługuje prezentację danych aplikacji i interakcję użytkownika. Widok to szablon HTML z osadzonym [znacznikiem Razor](xref:mvc/views/razor). Znaczniki Razor to kod, który współdziała ze znacznikiem HTML, aby utworzyć stronę sieci Web, która jest wysyłana do klienta.

W ASP.NET Core MVC widoki są plikami *. cshtml* , które używają [języka programowania C#](/dotnet/csharp/) w znaczniku Razor. Zazwyczaj pliki widoku są pogrupowane w foldery o nazwie dla każdej z [kontrolerów](xref:mvc/controllers/actions)aplikacji. Foldery są przechowywane w folderze *widoki* w katalogu głównym aplikacji:

![Folder widoki w Eksplorator rozwiązań programu Visual Studio jest otwarty z folderem macierzystym otwartym, aby pokazać informacje o plikach. cshtml, Contact. cshtml i index. cshtml](overview/_static/views_solution_explorer.png)

Kontroler *główny* jest reprezentowany przez folder *macierzysty* w folderze *widoki* . Folder *macierzysty* zawiera widoki stron sieci Web *Informacje o*programie, *kontakt*i *indeks* (Strona główna). Gdy użytkownik zażąda jednej z tych trzech stron sieci Web, akcje kontrolera w kontrolerze *głównym* określają, które z tych trzech widoków są używane do kompilowania i zwracania strony internetowej do użytkownika.

Użyj [układów](xref:mvc/views/layout) , aby zapewnić spójne sekcje stron i ograniczyć powtarzanie kodu. Układy często zawierają nagłówek, nawigację i elementy menu oraz stopkę. Nagłówek i stopka zazwyczaj zawierają standardowe znaczniki dla wielu elementów metadanych i linki do zasobów skryptu i stylu. Układy umożliwiają uniknięcie tego standardowego znacznika w widokach.

[Częściowe widoki](xref:mvc/views/partial) redukują duplikowanie kodu przez zarządzanie częścią widoków wielokrotnego użytku. Na przykład widok częściowy jest przydatny w przypadku życiorysu autora w witrynie sieci Web blogu, która pojawia się w kilku widokach. Biografia autora to zwykła zawartość widoku i nie wymaga wykonania kodu w celu utworzenia zawartości dla strony sieci Web. Zawartość biografii autora jest dostępna dla samego powiązania widoku przez model, więc użycie widoku częściowego dla tego typu zawartości jest idealne.

[Składniki widoku](xref:mvc/views/view-components) są podobne do widoków częściowych, które umożliwiają zredukowanie powtarzalnego kodu, ale są odpowiednie do wyświetlania zawartości, która wymaga uruchomienia kodu na serwerze w celu renderowania strony sieci Web. Składniki widoku są przydatne, gdy renderowana zawartość wymaga interakcji z bazą danych, na przykład w przypadku koszyka w witrynie sieci Web. Składniki widoku nie są ograniczone do powiązania modelu, aby można było utworzyć dane wyjściowe strony sieci Web.

## <a name="benefits-of-using-views"></a>Zalety korzystania z widoków

Wyświetla pomoc w celu ustalenia [rozdzielenia problemów](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) w aplikacji MVC przez oddzielenie znaczników interfejsu użytkownika od innych części aplikacji. Następujący projekt SoC umożliwia modularną aplikację, która zapewnia kilka korzyści:

* Aplikacja jest łatwiejsza w obsłudze, ponieważ jest lepiej zorganizowana. Widoki są zazwyczaj pogrupowane według funkcji aplikacji. Ułatwia to Znajdowanie powiązanych widoków podczas pracy nad funkcją.
* Części aplikacji są luźno powiązane. Widoki aplikacji można kompilować i aktualizować niezależnie od składników logiki biznesowej i dostępu do danych. Widoki aplikacji można modyfikować bez konieczności aktualizowania innych części aplikacji.
* Łatwiejsze jest przetestowanie części interfejsu użytkownika aplikacji, ponieważ widoki są osobnymi jednostkami.
* Ze względu na lepszą organizację nie można przypadkowo powtarzać sekcji interfejsu użytkownika.

## <a name="creating-a-view"></a>Tworzenie widoku

Widoki, które są specyficzne dla kontrolera, są tworzone w folderze *widoki/[ControllerName]* . Widoki, które są współużytkowane przez kontrolery, są umieszczane w folderze *widoki/udostępnione* . Aby utworzyć widok, Dodaj nowy plik i nadaj mu taką samą nazwę jak skojarzona z nim akcja kontrolera z rozszerzeniem *. cshtml* . Aby utworzyć widok, który odpowiada akcji *about* w kontrolerze *głównym* , Utwórz plik *about. cshtml* w folderze *widoki/Home* :

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

Znaczniki *Razor* zaczynają się `@` od symbolu. Uruchamiaj instrukcje języka C#, umieszczając kod w języku C# w [blokach kodu Razor](xref:mvc/views/razor#razor-code-blocks) ,`{ ... }`które są określone przez nawiasy klamrowe (). Na przykład zapoznaj się z tematem przypisywanie elementu " `ViewData["Title"]` informacje". Możesz wyświetlić wartości w kodzie HTML, po prostu przywołując wartość `@` symbolem. Zapoznaj się z zawartością `<h2>` elementów `<h3>` i powyżej.

Zawartość widoku pokazana powyżej jest tylko częścią całej strony sieci Web, która jest renderowana dla użytkownika. Pozostała część układu strony i inne typowe aspekty widoku są określone w innych plikach widoku. Aby dowiedzieć się więcej, zobacz [temat układ](xref:mvc/views/layout).

## <a name="how-controllers-specify-views"></a>Jak kontrolery określają widoki

Widoki są zwykle zwracane z akcji jako [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), które są typu [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult). Metoda działania może tworzyć i zwracać `ViewResult` bezpośrednio, ale nie jest to powszechnie wykonywane. Ponieważ większość kontrolerów dziedziczy po [kontrolerze](/dotnet/api/microsoft.aspnetcore.mvc.controller), wystarczy użyć metody `View` pomocnika do zwrócenia: `ViewResult`

*HomeController.cs*

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

Po powrocie tej akcji widok *Informacje o. cshtml* widoczny w ostatniej sekcji jest renderowany jako następująca strona sieci Web:

![Informacje o stronie renderowanej w przeglądarce Edge](overview/_static/about-page.png)

Metoda `View` pomocnika ma kilka przeciążeń. Opcjonalnie można określić:

* Jawny widok do zwrócenia:

  ```csharp
  return View("Orders");
  ```

* [Model](xref:mvc/models/model-binding) do przekazania do widoku:

  ```csharp
  return View(Orders);
  ```

* Zarówno widok, jak i model:

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>Wyświetlanie odnajdywania

Gdy akcja zwraca widok, odbywa się proces o nazwie *odnajdywanie widoku* . Ten proces określa, który plik widoku jest używany na podstawie nazwy widoku. 

Domyślne zachowanie `View` metody (`return View();`) ma zwrócić widok o tej samej nazwie co Metoda akcji, z której jest wywoływana. Na przykład nazwa metody *informacji* `ActionResult` kontrolera służy do wyszukiwania pliku widoku o nazwie *about. cshtml*. Najpierw środowisko uruchomieniowe przeszukuje folder *widoki/[ControllerName]* dla widoku. Jeśli w tym miejscu nie zostanie znaleziony pasujący widok, przeszukiwany jest folder *udostępniony* dla tego widoku.

Nie `ViewResult` ma znaczenia, `return View();` czy niejawnie zwracają nazwę widoku do `View` metody z. `return View("<ViewName>");` W obu przypadkach należy wyświetlić wyszukiwanie w poszukiwaniu zgodnego pliku widoku w następującej kolejności:

   1. *Widoki/\[kontrolername]/\[viewName]. cshtml*
   1. *Widoki/udostępnione/\[viewName]. cshtml*

Zamiast nazwy widoku można podać ścieżkę pliku widoku. Jeśli używana jest ścieżka bezwzględna rozpoczynająca się od elementu głównego aplikacji (opcjonalnie rozpoczynając od "/" lub "~/"), należy określić rozszerzenie *cshtml* :

```csharp
return View("Views/Home/About.cshtml");
```

Możesz również użyć ścieżki względnej, aby określić widoki w różnych katalogach bez rozszerzenia *. cshtml* . `HomeController`Wewnątrz można zwrócić widok *indeksu* widoków *Zarządzanie* ze ścieżką względną:

```csharp
return View("../Manage/Index");
```

Analogicznie, można wskazać bieżący katalog specyficzny dla kontrolera z prefiksem "./":

```csharp
return View("./About");
```

[Widoki częściowe](xref:mvc/views/partial) i [składniki widoku](xref:mvc/views/view-components) używają podobnych mechanizmów odnajdywania (ale nie identycznych).

Można dostosować domyślną Konwencję dotyczącą sposobu, w jaki widoki znajdują się w aplikacji, przy użyciu niestandardowego [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).

Widok odnajdywania polega na znalezieniu plików widoku według nazwy pliku. Jeśli w podstawowym systemie plików jest rozróżniana wielkość liter, nazwy widoków są prawdopodobnie rozróżniane. Aby zapewnić zgodność między systemami operacyjnymi, Uwzględnij wielkość liter między nazwami kontrolerów i akcji oraz skojarzonymi folderami widoków i nazwami plików. Jeśli wystąpi błąd, że nie można odnaleźć pliku widoku podczas pracy z systemem plików z uwzględnieniem wielkości liter, upewnij się, że wielkość liter jest zgodna między żądanym plikiem widoku a rzeczywistą nazwą pliku widoku.

Postępuj zgodnie z najlepszymi rozwiązaniami dotyczącymi organizowania struktury plików dla widoków, aby odzwierciedlały relacje między kontrolerami, działaniami i widokami w celu utrzymania i przejrzystości.

## <a name="passing-data-to-views"></a>Przekazywanie danych do widoków

Przekazywanie danych do widoków przy użyciu kilku metod:

* Dane silnie wpisane: ViewModel
* Dane niejednoznacznie wpisane
  * `ViewData` (`ViewDataAttribute`)
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a>Dane silnie wpisane (ViewModel)

Najbardziej niezawodne podejście polega na określeniu typu [modelu](xref:mvc/models/model-binding) w widoku. Ten model jest często określany jako *ViewModel*. Wystąpienie typu ViewModel można przekazać do widoku z akcji.

Użycie ViewModel do przekazywania danych do widoku pozwala widokowi korzystać z funkcji sprawdzania *silnych* typów. *Silne wpisywanie* (lub *silnie wpisane*) oznacza, że każda zmienna i stała ma jawnie zdefiniowany typ (na `string`przykład `int`,, `DateTime`lub). Ważność typów używanych w widoku jest sprawdzana w czasie kompilacji.

[Program Visual Studio](https://visualstudio.microsoft.com) i lista [Visual Studio Code](https://code.visualstudio.com/) grupy o jednoznacznie określonym typie przy użyciu funkcji o nazwie [IntelliSense](/visualstudio/ide/using-intellisense). Aby wyświetlić właściwości ViewModel, wpisz nazwę zmiennej dla ViewModel, a następnie kropkę (`.`). Dzięki temu można szybciej napisać kod z mniejszą liczbą błędów.

Określ model przy użyciu `@model` dyrektywy. Użyj modelu z `@Model`:

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Aby zapewnić modelowi widok, kontroler przekazuje go jako parametr:

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

Nie ma żadnych ograniczeń dotyczących typów modelu, które można dostarczyć do widoku. Zalecamy używanie zwykłego starego obiektu CLR (POCO) modele widoków z niewielkimi lub żadnymi zachowaniami (metodami). Zazwyczaj klasy ViewModel są przechowywane w folderze *models* lub w oddzielnym folderze *modele widoków* w katalogu głównym aplikacji. *Adres* ViewModel używany w powyższym przykładzie to poco ViewModel przechowywany w pliku o nazwie *Address.cs*:

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

Nic nie pozwala na korzystanie z tych samych klas zarówno dla typów ViewModel, jak i typów modelu biznesowego. Korzystanie z oddzielnych modeli pozwala jednak na różne widoki niezależnie od logiki biznesowej i elementów dostępu do danych aplikacji. Rozdzielenie modeli i modele widoków zapewnia także korzyści z używania [modelu powiązania](xref:mvc/models/model-binding) i [walidacji](xref:mvc/models/validation) danych wysyłanych do aplikacji przez użytkownika.

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a>Dane słabo wpisane (ViewData, ViewData Attribute i ViewBag)

`ViewBag`*nie jest dostępny w Razor Pages.*

W przypadku widoków o jednoznacznie określonym typie widoki mają dostęp do jednoznacznie *wpisanej* kolekcji (nazywanej również *luźno wpisaną*) kolekcją danych. W przeciwieństwie do mocnych typów, *słabych typów* (lub *luźnych typów*) oznacza, że nie deklaruje jawnie typu danych, z których korzystasz. Możesz użyć kolekcji nieokreślonych danych do przekazywania małych ilości danych do i z kontrolerów i widoków.

| Przekazywanie danych między...                        | Przykład                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| Kontroler i widok                             | Wypełnianie listy rozwijanej danymi.                                          |
| Widok i widok [układu](xref:mvc/views/layout)   | Ustawianie ** \<tytułu>** zawartości elementu w widoku układu z pliku widoku.  |
| [Widok częściowy](xref:mvc/views/partial) i widok | Widżet wyświetlający dane na podstawie strony sieci Web, której zażądał użytkownik.      |

Do tej kolekcji można odwoływać się za `ViewData` pomocą `ViewBag` właściwości lub na kontrolerach i w widokach. `ViewData` Właściwość jest słownikiem obiektów o nieprawidłowym typie. `ViewBag` Właściwość to otoka `ViewData` , która udostępnia właściwości dynamiczne dla kolekcji źródłowej `ViewData` . Uwaga: w przypadku wyszukiwania kluczy nie jest rozróżniana wielkość liter `ViewData` w `ViewBag`obu i.

`ViewData`i `ViewBag` są dynamicznie rozwiązywane w czasie wykonywania. Ponieważ nie oferują sprawdzania typu w czasie kompilacji, obie są zwykle bardziej podatne na błędy niż przy użyciu ViewModel. Z tego powodu niektórzy deweloperzy wolą do minimalnej lub nigdy nie używać `ViewData` i. `ViewBag`

<a name="VD"></a>

**ViewData**

`ViewData`jest obiektem [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) dostępnym `string` za pomocą kluczy. Dane ciągu mogą być przechowywane i używane bezpośrednio bez konieczności rzutowania, ale należy rzutować inne `ViewData` wartości obiektów na określone typy podczas ich wyodrębniania. Można użyć `ViewData` do przekazywania danych z kontrolerów do widoków i w widokach, w tym [częściowych widoków](xref:mvc/views/partial) i [układów](xref:mvc/views/layout).

Poniżej znajduje się przykład, który ustawia wartości dla powitania i adresu przy użyciu `ViewData` w akcji:

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

Pracuj z danymi w widoku:

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

**ViewData — atrybut**

Innym podejściem korzystającym z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) jest [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Właściwości na kontrolerach Razor lub modelach stron oznaczonych `[ViewData]` atrybutem są przechowywane i ładowane ze słownika.

W poniższym przykładzie kontroler Home zawiera `Title` Właściwość oznaczoną przy użyciu. `[ViewData]` `About` Metoda ustawia tytuł dla widoku informacje:

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

W układzie tytuł jest odczytywany ze słownika ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

**ViewBag**

`ViewBag`*nie jest dostępny Razor na stronach.*

`ViewBag`jest obiektem [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) , który zapewnia dynamiczny dostęp do obiektów przechowywanych w `ViewData`. `ViewBag`może być wygodniejszy do pracy z, ponieważ nie wymaga rzutowania. Poniższy przykład pokazuje, jak używać `ViewBag` tego samego wyniku, jak w przypadku `ViewData` użycia powyżej:

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

**Jednoczesne korzystanie z ViewData i ViewBag**

`ViewBag`*nie jest dostępny Razor na stronach.*

Ponieważ `ViewData` i `ViewBag` zapoznaj się z tą `ViewData` samą kolekcją podstawową, można `ViewData` użyć `ViewBag` obu i i kombinacji między nimi podczas odczytywania i zapisywania wartości.

Ustaw tytuł przy użyciu `ViewBag` i opis przy użyciu `ViewData` w górnej części widoku *Informacje o. cshtml* :

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

Odczytaj właściwości, ale Cofnij użycie `ViewData` i. `ViewBag` W pliku *_Layout. cshtml* Uzyskaj tytuł przy użyciu `ViewData` i uzyskaj opis przy użyciu: `ViewBag`

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

Należy pamiętać, że ciągi nie wymagają rzutowania dla `ViewData`. Można używać `@ViewData["Title"]` bez rzutowania.

Użycie jednocześnie `ViewData` i `ViewBag` w tym samym czasie działa tak, jak mieszanie i odczytywanie właściwości. Renderowane są następujące znaczniki:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**Podsumowanie różnic między ViewData i ViewBag**

 `ViewBag`nie jest dostępny na Razor stronach.

* `ViewData`
  * Pochodzi z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), więc ma właściwości słownika, które mogą być przydatne, takie jak `ContainsKey`, `Add`, `Remove`, i `Clear`.
  * Klucze w słowniku są ciągami, więc odstępy są dozwolone. Przykład: `ViewData["Some Key With Whitespace"]`
  * Każdy typ inny niż element `string` musi być rzutowany w widoku, który `ViewData`ma być używany.
* `ViewBag`
  * Pochodzi z [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), dzięki czemu umożliwia tworzenie właściwości dynamicznych przy użyciu notacji kropek (`@ViewBag.SomeKey = <value or object>`), a rzutowanie nie jest wymagane. Składnia `ViewBag` umożliwia szybsze Dodawanie do kontrolerów i widoków.
  * Łatwiejszy do sprawdzenia pod kątem wartości null. Przykład: `@ViewBag.Person?.Name`

**Kiedy używać ViewData lub ViewBag**

Obie `ViewData` i `ViewBag` są równie ważnym podejściem do przekazywania małych ilości danych między kontrolerami i widokami. Wybór, który ma być używany, jest oparty na preferencjach. Możliwe jest mieszanie i `ViewData` Dopasowywanie `ViewBag` obiektów oraz obiekty, jednak kod jest łatwiejszy do odczytania i utrzymania przy użyciu jednego podejścia stosowanego spójnie. Oba podejścia są dynamicznie rozwiązywane w czasie wykonywania i w ten sposób podatne na błędy środowiska uruchomieniowego. Niektóre zespoły programistyczne ich nie mają.

### <a name="dynamic-views"></a>Widoki dynamiczne

Widoki, które nie deklarują typu modelu `@model` przy użyciu, ale mają do nich przekazanie wystąpienie modelu (na `return View(Address);`przykład), mogą dynamicznie odwoływać się do właściwości wystąpienia:

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Ta funkcja oferuje elastyczność, ale nie oferuje funkcji ochrony kompilacji ani technologii IntelliSense. Jeśli właściwość nie istnieje, generowanie strony sieci Web kończy się niepowodzeniem w czasie wykonywania.

## <a name="more-view-features"></a>Więcej funkcji widoku

[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) ułatwiają dodawanie zachowań po stronie serwera do istniejących tagów HTML. Korzystanie z pomocników tagów pozwala uniknąć konieczności pisania niestandardowego kodu lub pomocników w widokach. Pomocnicy tagów są stosowane jako atrybuty do elementów HTML i są ignorowane przez edytory, które nie mogą ich przetworzyć. Dzięki temu można edytować i renderować znaczniki widoku w różnych narzędziach.

Generowanie niestandardowego znacznika HTML można osiągnąć za pomocą wielu wbudowanych pomocników HTML. Bardziej złożona logika interfejsu użytkownika może być obsługiwana przez [składniki widoku](xref:mvc/views/view-components). Składniki widoku zapewniają ten sam SoC, który oferuje kontrolery i widoki. Mogą wyeliminować potrzebę wykonywania działań i widoków, które zajmują się danymi używanymi przez wspólne elementy interfejsu użytkownika.

Podobnie jak w przypadku wielu innych aspektów ASP.NET Core, widoki obsługują [iniekcję zależności](xref:fundamentals/dependency-injection), umożliwiając dodawanie usług do [widoków](xref:mvc/views/dependency-injection).
