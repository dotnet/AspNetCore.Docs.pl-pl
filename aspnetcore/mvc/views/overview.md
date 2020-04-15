---
title: Widoki w ASP.NET Core MVC
author: ardalis
description: Dowiedz się, jak widoki obsługują prezentację danych aplikacji i interakcję użytkownika w ASP.NET Core MVC.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/views/overview
ms.openlocfilehash: 70b8c2c01a28f99dd384351041a3b77d23f46a48
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384070"
---
# <a name="views-in-aspnet-core-mvc"></a>Widoki w ASP.NET Core MVC

Przez [Steve Smith](https://ardalis.com/)

W tym dokumencie wyjaśniono widoki używane w ASP.NET podstawowych aplikacji MVC. Aby uzyskać informacje na temat stron Razor, zobacz [Wprowadzenie do stron Razor](xref:razor-pages/index).

W wzorcu model-view-controller (MVC) *widok* obsługuje prezentacji danych aplikacji i interakcji użytkownika. Widok jest szablonem HTML z osadzonym [znacznikiem Razor](xref:mvc/views/razor). Znacznik maszynki do golenia to kod, który współdziała z znacznikami HTML w celu stworzenia strony sieci Web, która jest wysyłana do klienta.

W ASP.NET Core MVC widoki są plikami *cshtml,* które używają [języka programowania C#](/dotnet/csharp/) w znacznikach Razor. Zazwyczaj pliki widoku są pogrupowane w foldery nazwane dla każdego [z kontrolerów](xref:mvc/controllers/actions)aplikacji. Foldery są przechowywane w folderze *Widoki* w katalogu głównym aplikacji:

![Folder Widoki w Eksploratorze rozwiązań programu Visual Studio jest otwarty z otwartym folderem Strona główna, aby wyświetlić pliki About.cshtml, Contact.cshtml i Index.cshtml](overview/_static/views_solution_explorer.png)

Kontroler *macierzysty* jest reprezentowany przez folder *home* wewnątrz folderu *Widoki.* Folder *Strona główna* zawiera widoki stron sieci Web *Informacje,* *Kontakt*i *Indeks* (strona główna). Gdy użytkownik żąda jednej z tych trzech stron sieci Web, akcje kontrolera w kontrolerze *macierzystym* określają, który z trzech widoków jest używany do tworzenia i zwracania strony sieci Web do użytkownika.

Układy umożliwiają [spójne](xref:mvc/views/layout) sekcje stron sieci Web i zmniejszają powtarzanie kodu. Układy często zawierają elementy nagłówka, nawigacji i menu oraz stopkę. Nagłówek i stopka zwykle zawierają znaczniki standardowego dla wielu elementów metadanych i łącza do zasobów skryptu i stylu. Układy pomagają uniknąć tego znaczników kotłowych w widokach.

[Widoki częściowe](xref:mvc/views/partial) zmniejszają powielanie kodu przez zarządzanie częściami widoków wielokrotnegoużytnia. Na przykład widok częściowy jest przydatny w przypadku biografii autora w witrynie sieci Web bloga, która pojawia się w kilku widokach. Biografia autora jest zwykłą zawartością widoku i nie wymaga kodu do wykonania w celu wytworzenia zawartości dla strony internetowej. Zawartość biografii autora jest dostępna dla widoku przez sam powiązanie modelu, więc użycie widoku częściowego dla tego typu zawartości jest idealne.

[Składniki widoku](xref:mvc/views/view-components) są podobne do widoków częściowych, ponieważ umożliwiają zmniejszenie powtarzającego się kodu, ale są odpowiednie dla zawartości widoku, która wymaga uruchomienia kodu na serwerze w celu renderowania strony sieci Web. Wyświetl składniki są przydatne, gdy renderowana zawartość wymaga interakcji z bazą danych, na przykład dla koszyka witryny sieci Web. Wyświetl składniki nie są ograniczone do powiązania modelu w celu uzyskania danych wyjściowych strony sieci Web.

## <a name="benefits-of-using-views"></a>Korzyści z korzystania z widoków

Widoki pomagają ustalić [oddzielenie problemów](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) w aplikacji MVC, oddzielając znaczniki interfejsu użytkownika od innych części aplikacji. Zgodnie z projektem SoC sprawia, że aplikacja jest modułowa, co zapewnia kilka korzyści:

* Aplikacja jest łatwiejsza w utrzymaniu, ponieważ jest lepiej zorganizowana. Widoki są zazwyczaj pogrupowane według funkcji aplikacji. Ułatwia to znajdowanie powiązanych widoków podczas pracy nad funkcją.
* Części aplikacji są luźno sprzężone. Widoki aplikacji można tworzyć i aktualizować niezależnie od składników logiki biznesowej i dostępu do danych. Można modyfikować widoki aplikacji bez konieczności aktualizowania innych części aplikacji.
* Łatwiej jest przetestować części interfejsu użytkownika aplikacji, ponieważ widoki są oddzielne jednostki.
* Ze względu na lepszą organizację jest mniej prawdopodobne, że przypadkowo powtórzysz sekcje interfejsu użytkownika.

## <a name="creating-a-view"></a>Tworzenie widoku

Widoki specyficzne dla kontrolera są tworzone w folderze *Widoki/[Nazwa kontrolera].* Widoki współużytkowane przez kontrolery są umieszczane w folderze *Widoki/Udostępnione.* Aby utworzyć widok, dodaj nowy plik i nadaj mu taką samą nazwę jak skojarzona z nim akcja kontrolera z rozszerzeniem pliku *cshtml.* Aby utworzyć widok odpowiadający akcji *Informacje* na kontrolerze *macierzystym,* utwórz plik *About.cshtml* w folderze *Widoki/Strona główna:*

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Znacznik brzytwy* zaczyna `@` się od symbolu. Uruchom instrukcje C#, umieszczając kod C# w [blokach kodu Razor](xref:mvc/views/razor#razor-code-blocks) ustawionych przez nawiasy klamrowe (`{ ... }`). Na przykład zobacz przypisanie "Informacje", aby `ViewData["Title"]` pokazano powyżej. Wartości w formacie HTML można wyświetlać, `@` po prostu odwołując się do wartości za pomocą symbolu. Zobacz zawartość `<h2>` i `<h3>` elementy powyżej.

Zawartość widoku pokazana powyżej jest tylko częścią całej strony sieci Web, która jest renderowana dla użytkownika. Pozostałe elementy układu strony i inne typowe aspekty widoku są określone w innych plikach widoku. Aby dowiedzieć się więcej, zobacz [temat Układ](xref:mvc/views/layout).

## <a name="how-controllers-specify-views"></a>Jak kontrolerzy określają widoki

Widoki są zazwyczaj zwracane z akcji jako [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), który jest typem [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult). Metoda akcji można utworzyć `ViewResult` i zwrócić bezpośrednio, ale to nie jest powszechnie wykonywane. Ponieważ większość kontrolerów dziedziczy po [kontrolerze,](/dotnet/api/microsoft.aspnetcore.mvc.controller)wystarczy użyć metody `View` pomocnika, aby zwrócić `ViewResult`:

*HomeController.cs*

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

Gdy ta akcja powróci, widok *About.cshtml* wyświetlany w ostatniej sekcji jest renderowany jako następująca strona sieci Web:

![Strona wyrenderowana w przeglądarce Edge — informacje](overview/_static/about-page.png)

Metoda `View` pomocnika ma kilka przeciążeń. Opcjonalnie można określić:

* Jawny widok do zwrócenia:

  ```csharp
  return View("Orders");
  ```

* [Model,](xref:mvc/models/model-binding) który ma przejść do widoku:

  ```csharp
  return View(Orders);
  ```

* Zarówno widok, jak i model:

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>Wyświetlanie odnajdowania

Gdy akcja zwraca widok, odbywa się proces o nazwie *odnajdowanie widoku.* Ten proces określa, który plik widoku jest używany na podstawie nazwy widoku. 

Domyślnym zachowaniem `View` metody`return View();`( ) jest zwrócenie widoku o takiej samej nazwie jak metoda akcji, z której jest wywoływana. Na przykład nazwa metody *Informacje* `ActionResult` kontrolera służy do wyszukiwania pliku widoku o nazwie *About.cshtml*. Najpierw środowisko wykonawcze szuka w *views/[ControllerName]* folderu dla widoku. Jeśli nie znajdzie tam pasującego widoku, przeszukuje widok w folderze *Udostępniony.*

Nie ma znaczenia, czy niejawnie `ViewResult` zwracasz nazwę widoku `return View();` z `View` lub `return View("<ViewName>");`jawnie przekazujesz do metody za pomocą . W obu przypadkach widok odnajdywania wyszukuje pasujący plik widoku w tej kolejności:

   1. *Widoki/\[Nazwa kontrolera]/\[ViewName].cshtml*
   1. *Widoki/Udostępnione/\[ViewName].cshtml*

Zamiast nazwy widoku można podać ścieżkę pliku widoku. Jeśli używasz ścieżki bezwzględnej rozpoczynającej się w katalogu głównym aplikacji (opcjonalnie zaczynając od "/" lub "~/"), należy określić rozszerzenie *.cshtml:*

```csharp
return View("Views/Home/About.cshtml");
```

Można również użyć ścieżki względnej, aby określić widoki w różnych katalogach bez rozszerzenia *cshtml.* Wewnątrz `HomeController`widoku , można zwrócić widok *indeksu* widoków *zarządzania* ścieżką względną:

```csharp
return View("../Manage/Index");
```

Podobnie można wskazać bieżący katalog specyficzny dla kontrolera za pomocą prefiksu "./":

```csharp
return View("./About");
```

[Widoki częściowe](xref:mvc/views/partial) i [komponenty widoku](xref:mvc/views/view-components) używają podobnych (ale nie identycznych) mechanizmów odnajdowania.

Domyślną konwencję można dostosować do sposobu, w jaki widoki znajdują się w aplikacji, korzystając z niestandardowego [programu IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).

Odnajdowanie widoku zależy od znajdowania plików widoku według nazwy pliku. Jeśli w podstawowym systemie plików rozróżniana jest wielkość liter, w nazwach widoku prawdopodobnie rozróżniana jest wielkość liter. Aby uzyskać zgodność między systemami operacyjnymi, dopasuj przypadek między nazwami kontrolerów i akcji oraz skojarzonymi folderami widoku i nazwami plików. Jeśli wystąpi błąd, że plik widoku nie można znaleźć podczas pracy z systemem plików z uwzględnieniem wielkości liter, upewnij się, że wielkość liter jest zgodna między żądanym plikiem widoku a rzeczywistą nazwą pliku widoku.

Postępuj zgodnie z najlepszymi praktykami organizowania struktury plików dla widoków, aby odzwierciedlić relacje między kontrolerami, akcjami i widokami, aby zapewnić łatwość konserwacji i przejrzystość.

## <a name="passing-data-to-views"></a>Przekazywanie danych do widoków

Przekazywanie danych do widoków przy użyciu kilku metod:

* Silnie wpisane dane: viewmodel
* Słabo wpisane dane
  * `ViewData` (`ViewDataAttribute`)
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a>Silnie wpisane dane (viewmodel)

Najbardziej niezawodne podejście jest określenie typu [modelu](xref:mvc/models/model-binding) w widoku. Ten model jest powszechnie określany jako *model widoku*. Można przekazać wystąpienie typu viewmodel do widoku z akcji.

Za pomocą modelu widoku do przekazywania danych do widoku umożliwia widok, aby skorzystać z *silnego* sprawdzania typu. *Silne wpisywanie* (lub *silnie wpisane)* oznacza, że każda zmienna `string`i `int`stała `DateTime`ma wyraźnie zdefiniowany typ (na przykład , , lub ). Ważność typów używanych w widoku jest sprawdzana w czasie kompilacji.

[Visual Studio](https://visualstudio.microsoft.com) i [Visual Studio Code](https://code.visualstudio.com/) listy silnie wpisanych członków klasy za pomocą funkcji o nazwie [IntelliSense](/visualstudio/ide/using-intellisense). Aby wyświetlić właściwości systemu viewmodel, wpisz nazwę zmiennej dla asetu,`.`po którym następuje kropka ( ). Pomaga to pisać kod szybciej przy mniejszej liczbie błędów.

Określ model `@model` przy użyciu dyrektywy. Użyj modelu `@Model`z:

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Aby udostępnić model do widoku, kontroler przekazuje go jako parametr:

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

Nie ma żadnych ograniczeń dotyczących typów modeli, które można podać do widoku. Zaleca się używanie zwykłych starych obiektów CLR (POCO) z małym lub żadnym zachowaniem (metody) zdefiniowane. Zazwyczaj viewmodel klasy są przechowywane w *models* folderu lub oddzielny *viewmodels* folderu w katalogu głównym aplikacji. System viewmodel *adresu* używany w powyższym przykładzie to program widoku POCO przechowywany w pliku o nazwie *Address.cs:*

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

Nic nie stoi na przeszkodzie, aby używać tych samych klas zarówno dla typów modelu widoku, jak i typów modeli biznesowych. Jednak przy użyciu oddzielnych modeli umożliwia widoki różnią się niezależnie od logiki biznesowej i części dostępu do danych aplikacji. Separacja modeli i viewmodels oferuje również korzyści zabezpieczeń, gdy modele używają [powiązania modelu](xref:mvc/models/model-binding) i [sprawdzania poprawności](xref:mvc/models/validation) danych wysyłanych do aplikacji przez użytkownika.

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a>Słabo wpisane dane (ViewData, ViewData i ViewBag)

`ViewBag`*nie jest dostępna w aplikacji Razor Pages.*

Oprócz widoków silnie typizowanych widoki mają dostęp do *słabo wpisanego* (nazywanego również *luźno wpisanym)* zbioru danych. W przeciwieństwie do typów *silnych, słabe typy* (lub *typy luźne)* oznacza, że nie jawnie zadeklarować typ danych, których używasz. Można użyć kolekcji słabo wpisanych danych do przekazywania niewielkich ilości danych do i z kontrolerów i widoków.

| Przekazywanie danych między ...                        | Przykład                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| Kontroler i widok                             | Wypełnianie listy rozwijanej danymi.                                          |
| Widok i [widok układu](xref:mvc/views/layout)   | Ustawienie ** \<tytułu>** zawartości elementu w widoku układu z pliku widoku.  |
| [Widok częściowy](xref:mvc/views/partial) i widok | Widżet wyświetlany na podstawie strony sieci Web żądanej przez użytkownika.      |

Do tej kolekcji można odwoływać się za pośrednictwem `ViewData` właściwości lub `ViewBag` na kontrolerach i widokach. Właściwość `ViewData` jest słownikiem słabo wpisanych obiektów. Właściwość `ViewBag` jest otoka `ViewData` wokół, która zapewnia `ViewData` właściwości dynamiczne dla podstawowej kolekcji. Uwaga: Wyszukiwania klawiszy są niewrażliwe na `ViewData` te `ViewBag`argumenty zarówno dla i .

`ViewData`i `ViewBag` są dynamicznie rozwiązywane w czasie wykonywania. Ponieważ nie oferują one sprawdzanie typu kompilacji czasu, oba są na ogół bardziej podatne na błędy niż przy użyciu viewmodel. Z tego powodu niektórzy deweloperzy wolą `ViewData` minimalnie lub nigdy nie używać i `ViewBag`.

<a name="VD"></a>

**ViewData (Wyświetl dane)**

`ViewData`jest [obiektem ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) `string` dostępnym za pośrednictwem kluczy. Dane ciągu mogą być przechowywane i używane bezpośrednio bez konieczności rzutnia, ale należy rzutować inne `ViewData` wartości obiektów do określonych typów podczas ich wyodrębniania. Za pomocą `ViewData` funkcji przekazywania danych z kontrolerów do widoków i widoków, w tym [widoków częściowych](xref:mvc/views/partial) i [układów.](xref:mvc/views/layout)

Oto przykład, który ustawia wartości powitania i `ViewData` adresu przy użyciu w akcji:

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

Praca z danymi w widoku:

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

**Atrybut ViewData**

Innym podejściem, które używa [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) jest [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Właściwości na kontrolerach lub razor Page `[ViewData]` modele oznaczone atrybutem mają swoje wartości przechowywane i ładowane ze słownika.

W poniższym przykładzie kontroler `Title` home zawiera `[ViewData]`właściwość oznaczoną symbolem . Metoda `About` ustawia tytuł dla widoku Informacje:

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

**Torba ViewBag**

`ViewBag`*nie jest dostępna w aplikacji Razor Pages.*

`ViewBag`jest obiektem [DynamicViewData,](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) który zapewnia dynamiczny `ViewData`dostęp do obiektów przechowywanych w programie . `ViewBag`może być wygodniejszy w pracy, ponieważ nie wymaga odlewania. W poniższym przykładzie `ViewBag` pokazano, jak `ViewData` używać z tym samym wynikiem, co przy użyciu powyżej:

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

**Jednoczesne korzystanie z viewdata i ViewBag**

`ViewBag`*nie jest dostępna w aplikacji Razor Pages.*

Ponieważ `ViewData` `ViewBag` i odnoszą się `ViewData` do tej samej `ViewBag` kolekcji podstawowej, można użyć i `ViewData` mieszać i dopasowywać między nimi podczas odczytu i pisania wartości.

Ustaw użycie `ViewBag` tytułu i `ViewData` opisu w górnej części widoku *About.cshtml:*

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

Odczytuj właściwości, ale `ViewData` odwróć użycie i `ViewBag`. W pliku *_Layout.cshtml,* uzyskać tytuł `ViewData` za pomocą i `ViewBag`uzyskać opis za pomocą :

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

Pamiętaj, że ciągi nie wymagają `ViewData`rzutu dla . Można używać `@ViewData["Title"]` bez odlewania.

Korzystanie `ViewData` zarówno `ViewBag` i w tym samym czasie działa, podobnie jak mieszanie i dopasowywanie odczytu i pisania właściwości. Renderowane są następujące znaczniki:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**Podsumowanie różnic między ViewData i ViewBag**

 `ViewBag`nie jest dostępna na stronach Razor.

* `ViewData`
  * Pochodzi z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), więc ma właściwości słownika, które `ContainsKey` `Add`mogą `Remove`być `Clear`przydatne, takie jak , , i .
  * Klucze w słowniku są ciągami znaków, więc odstępy są dozwolone. Przykład: `ViewData["Some Key With Whitespace"]`
  * Każdy typ inny `string` niż a musi być `ViewData`rzutowy w widoku, aby użyć .
* `ViewBag`
  * Wywodzi się z [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), dzięki czemu umożliwia tworzenie`@ViewBag.SomeKey = <value or object>`właściwości dynamicznych przy użyciu notacji kropkowej ( ), a rzutowanie nie jest wymagane. Składnia `ViewBag` sprawia, że szybciej dodać do kontrolerów i widoków.
  * Prostsze, aby sprawdzić wartości null. Przykład: `@ViewBag.Person?.Name`

**Kiedy używać ViewData lub ViewBag**

Oba `ViewData` `ViewBag` i są równie prawidłowe podejścia do przekazywania niewielkich ilości danych między kontrolerami i widoków. Wybór, którego z nich użyć, zależy od preferencji. Można mieszać `ViewData` i `ViewBag` dopasowywać i obiekty, jednak kod jest łatwiejsze do odczytania i utrzymania z jednego podejścia używane konsekwentnie. Oba podejścia są dynamicznie rozpoznawane w czasie wykonywania, a tym samym podatne na wywoływanie błędów środowiska uruchomieniowego. Niektóre zespoły programistyczne ich unikają.

### <a name="dynamic-views"></a>Widoki dynamiczne

Widoki, które nie deklarują typu `@model` modelu przy użyciu, ale które mają `return View(Address);`wystąpienie modelu przekazywane do nich (na przykład ) można odwoływać się do właściwości wystąpienia dynamicznie:

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Ta funkcja oferuje elastyczność, ale nie oferuje ochrony kompilacji ani IntelliSense. Jeśli właściwość nie istnieje, generowanie strony sieci Web kończy się niepowodzeniem w czasie wykonywania.

## <a name="more-view-features"></a>Więcej funkcji widoku

[Pomocnik tagów](xref:mvc/views/tag-helpers/intro) ułatwia dodawanie zachowania po stronie serwera do istniejących tagów HTML. Korzystanie z Pomocników tagów pozwala uniknąć konieczności pisania niestandardowego kodu lub pomocników w widokach. Pomocników tagów są stosowane jako atrybuty do elementów HTML i są ignorowane przez edytory, które nie mogą ich przetworzyć. Dzięki temu można edytować i renderować znaczniki widoku w różnych narzędziach.

Generowanie niestandardowych znaczników HTML można osiągnąć za pomocą wielu wbudowanych pomocników HTML. Bardziej złożona logika interfejsu użytkownika może być obsługiwana przez [składniki widoku](xref:mvc/views/view-components). Składniki widoku zapewniają ten sam SoC, który oferują kontrolery i widoki. Mogą one wyeliminować potrzebę akcji i widoków, które dotyczą danych używanych przez typowe elementy interfejsu użytkownika.

Podobnie jak wiele innych aspektów ASP.NET Core, widoki obsługują [iniekcję zależności,](xref:fundamentals/dependency-injection)umożliwiając usługi, które mają być [wstrzykiwane do widoków.](xref:mvc/views/dependency-injection)
