---
title: BlazorRouting ASP.NET Core
author: guardrex
description: Dowiedz się, jak zarządzać routingiem żądań w aplikacjach oraz jak używać składnika NavLink w Blazor aplikacjach do nawigacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: ec183f4aadc6bafd8e77f9d97291ba3d47bd92f5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506932"
---
# <a name="aspnet-core-no-locblazor-routing"></a>BlazorRouting ASP.NET Core

Autor [Luke Latham](https://github.com/guardrex)

W tym artykule dowiesz się, jak zarządzać routingiem żądań oraz jak używać <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składnika do tworzenia linków nawigacji w Blazor aplikacjach.

## <a name="route-templates"></a>Szablony tras

<xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik włącza Routing do Razor składników w Blazor aplikacji. <xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik jest używany w `App` składniku Blazor aplikacji.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App1.razor)]

::: moniker-end

Gdy Razor składnik ( `.razor` ) z [ `@page` dyrektywą](xref:mvc/views/razor#page) jest kompilowany, wygenerowana Klasa składnika jest dostarczana z <xref:Microsoft.AspNetCore.Components.RouteAttribute> określeniem szablonu trasy składnika.

Po uruchomieniu aplikacji zestaw określony jako router `AppAssembly` jest skanowany w celu zebrania informacji o trasie dla składników aplikacji, które mają <xref:Microsoft.AspNetCore.Components.RouteAttribute> .

W środowisku uruchomieniowym <xref:Microsoft.AspNetCore.Components.RouteView> składnik:

* Odbiera <xref:Microsoft.AspNetCore.Components.RouteData> od siebie wraz <xref:Microsoft.AspNetCore.Components.Routing.Router> z dowolnymi parametrami trasy.
* Renderuje określony składnik przy użyciu [układu](xref:blazor/layouts), w tym wszelkich dalszych zagnieżdżonych układów.

Opcjonalnie można określić <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parametr z klasą układu dla składników, które nie określają układu z [ `@layout` dyrektywą](xref:blazor/layouts#specify-a-layout-in-a-component). BlazorSzablony projektu struktury określają `MainLayout` składnik ( `Shared/MainLayout.razor` ) jako domyślny układ aplikacji. Aby uzyskać więcej informacji na temat układów, zobacz <xref:blazor/layouts> .

Składniki obsługują wiele szablonów tras przy użyciu wielu [ `@page` dyrektyw](xref:mvc/views/razor#page). Poniższy przykładowy składnik ładuje się na żądania dla `/BlazorRoute` i `/DifferentBlazorRoute` .

`Pages/BlazorRoute.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> Aby adresy URL zostały poprawnie rozpoznane, aplikacja musi zawierać `<base>` tag w `wwwroot/index.html` pliku ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` pliku ( Blazor Server ) z ścieżką bazową aplikacji określoną w `href` atrybucie. Aby uzyskać więcej informacji, zobacz <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Podaj zawartość niestandardową, jeśli nie można odnaleźć zawartości

<xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik umożliwia aplikacji określenie zawartości niestandardowej, jeśli nie można odnaleźć zawartości dla żądanej trasy.

W `App` składniku Ustaw zawartość niestandardową w <xref:Microsoft.AspNetCore.Components.Routing.Router> szablonie składnika <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> .

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App2.razor?highlight=5-8)]

::: moniker-end

Dowolne elementy są obsługiwane jako zawartość `<NotFound>` tagów, takich jak inne składniki interaktywne. Aby zastosować domyślny układ do <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> zawartości, zobacz <xref:blazor/layouts#default-layout> .

## <a name="route-to-components-from-multiple-assemblies"></a>Kierowanie do składników z wielu zestawów

Użyj <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parametru, aby określić dodatkowe zestawy dla <xref:Microsoft.AspNetCore.Components.Routing.Router> składnika do uwzględnienia podczas wyszukiwania składników routingu. Dodatkowe zestawy są skanowane oprócz zestawu określonego dla `AppAssembly` . W poniższym przykładzie `Component1` jest składnikiem rutowanym zdefiniowanym w [bibliotece klas składników](xref:blazor/components/class-libraries), do której się odwołuje. W poniższym <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> przykładzie przedstawiono obsługę routingu dla programu `Component1` .

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App3.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App3.razor)]

::: moniker-end

## <a name="route-parameters"></a>Parametry trasy

Router używa parametrów trasy do wypełniania odpowiednich [parametrów składnika](xref:blazor/components/index#component-parameters) o tej samej nazwie. W nazwach parametrów trasy jest rozróżniana wielkość liter. W poniższym przykładzie `text` parametr przypisuje wartość segmentu trasy do `Text` właściwości składnika. Gdy jest wykonywane żądanie `/RouteParameter/amazing` , `<h1>` zawartość tagu jest renderowana jako `Blazor is amazing!` .

`Pages/RouteParameter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

Parametry opcjonalne są obsługiwane. W poniższym przykładzie `text` opcjonalny parametr przypisuje wartość segmentu trasy do `Text` właściwości składnika. Jeśli segment nie istnieje, wartość `Text` jest ustawiana na `fantastic` .

`Pages/RouteParameter.razor`:

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Parametry opcjonalne nie są obsługiwane. W poniższym przykładzie są stosowane dwie [ `@page` dyrektywy](xref:mvc/views/razor#page) . Pierwsza dyrektywa umożliwia nawigowanie do składnika bez parametru. Druga dyrektywa przypisuje `{text}` wartość parametru trasy do `Text` właściwości składnika.

`Pages/RouteParameter.razor`:

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter2.razor?highlight=2)]

::: moniker-end

Użyj [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) zamiast, [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) Aby zezwolić aplikacji na nawigowanie do tego samego składnika przy użyciu innej opcjonalnej wartości parametru. Na podstawie powyższego przykładu należy użyć, `OnParametersSet` gdy użytkownik powinien przejść od `/RouteParameter` do `/RouteParameter/amazing` lub z `/RouteParameter/amazing` do `/RouteParameter` :

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a>Ograniczenia trasy

Ograniczenie trasy wymusza dopasowanie typu w segmencie trasy do składnika.

W poniższym przykładzie trasa do `User` składnika dopasowuje się tylko wtedy, gdy:

* `Id`Segment trasy jest obecny w adresie URL żądania.
* `Id`Segment jest typu Integer ( `int` ).

`Pages/User.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/User.razor?highlight=1)]

::: moniker-end

Dostępne są ograniczenia trasy podane w poniższej tabeli. W przypadku ograniczeń trasy, które pasują do niezmiennej kultury, zobacz ostrzeżenie poniżej tabeli, aby uzyskać więcej informacji.

| Typu | Przykład           | Przykładowe dopasowania                                                                  | Niezmiennej<br>kultura<br>parowanie |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Nie                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Nie                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |

> [!WARNING]
> Ograniczenia trasy, które weryfikują adres URL i są konwertowane na typ CLR (takie jak `int` lub <xref:System.DateTime> ), zawsze używają niezmiennej kultury. W tych ograniczeniach przyjęto założenie, że adres URL nie jest Lokalizowalny.

## <a name="routing-with-urls-that-contain-dots"></a>Routing z adresami URL zawierającymi kropki

W przypadku hostowanych Blazor WebAssembly i Blazor Server aplikacji szablon trasy domyślnej po stronie serwera zakłada, że jeśli ostatni segment adresu URL żądania zawiera kropkę ( `.` ), że plik jest żądany. Na przykład adres URL `https://localhost.com:5001/example/some.thing` jest interpretowany przez router jako żądanie pliku o nazwie `some.thing` . Bez dodatkowej konfiguracji aplikacja zwraca *Nieznalezioną odpowiedź 404* , jeśli `some.thing` była przeznaczona do skierowania do składnika z [ `@page` dyrektywą](xref:mvc/views/razor#page) i `some.thing` jest wartością parametru trasy. Aby użyć trasy z co najmniej jednym parametrem zawierającym kropkę, aplikacja musi skonfigurować trasę z szablonem niestandardowym.

Rozważmy poniższy `Example` składnik, który może odbierać parametr trasy z ostatniego segmentu adresu URL.

`Pages/Example.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Example.razor?highlight=2)]

::: moniker-end

Aby zezwolić *`Server`* aplikacji hostowanego Blazor WebAssembly rozwiązania na kierowanie żądania z kropką w `param` parametrze trasy, należy dodać szablon "rezerwowy" trasy pliku z opcjonalnym parametrem w `Startup.Configure` .

`Startup.cs`:

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

Aby skonfigurować Blazor Server aplikację do kierowania żądania z kropką w `param` parametrze trasy, Dodaj szablon trasy strony rezerwowej z opcjonalnym parametrem w `Startup.Configure` .

`Startup.cs`:

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.

## <a name="catch-all-route-parameters"></a>Catch-wszystkie parametry tras

::: moniker range=">= aspnetcore-5.0"

Wszystkie parametry trasy, które przechwytują ścieżki między wieloma granicami folderów, są obsługiwane w składnikach.

Wszystkie parametry trasy są następujące:

* Nazwa jest zgodna z nazwą segmentu trasy. W nazewnictwie nie jest rozróżniana wielkość liter.
* `string`Typ. Struktura nie zapewnia automatycznego rzutowania.
* Na końcu adresu URL.

`Pages/CatchAll.razor`:

[!code-razor[](routing/samples_snapshot/5.x/CatchAll.razor)]

Dla adresu URL `/catch-all/this/is/a/test` z szablonem trasy dla `/catch-all/{*pageRoute}` , wartość `PageRoute` jest ustawiona na `this/is/a/test` .

Ukośniki i segmenty ścieżki przechwyconej są zdekodowane. Dla szablonu trasy w programie `/catch-all/{*pageRoute}` adres URL `/catch-all/this/is/a%2Ftest%2A` daje wartość `this/is/a/test*` .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Wszystkie parametry tras są obsługiwane w ASP.NET Core 5,0 lub nowszych. Aby uzyskać więcej informacji, wybierz wersję 5,0 tego artykułu.

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a>Pomoc dotycząca stanu identyfikatora URI i nawigacji

Służy <xref:Microsoft.AspNetCore.Components.NavigationManager> do zarządzania identyfikatorami URI i nawigacją w kodzie C#. <xref:Microsoft.AspNetCore.Components.NavigationManager> zawiera zdarzenie i metody przedstawione w poniższej tabeli.

| Członek | Opis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Pobiera bieżący bezwzględny identyfikator URI. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Pobiera podstawowy identyfikator URI (z końcowym ukośnikiem), który można dołączać do względnych ścieżek URI w celu utworzenia bezwzględnego identyfikatora URI. Zazwyczaj <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> odpowiada `href` atrybutowi `<base>` elementu dokumentu w `wwwroot/index.html` ( Blazor WebAssembly ) lub `Pages/_Host.cshtml` ( Blazor Server ). |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Przechodzi do określonego identyfikatora URI. Jeśli `forceLoad` jest `true` :<ul><li>Routing po stronie klienta jest pomijany.</li><li>W przeglądarce wymuszone jest załadowanie nowej strony z serwera, niezależnie od tego, czy identyfikator URI jest zwykle obsługiwany przez router po stronie klienta.</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Zdarzenie, które jest wyzwalane po zmianie lokalizacji nawigacji. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Konwertuje względny identyfikator URI na bezwzględny identyfikator URI. |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Przy użyciu podstawowego identyfikatora URI (na przykład identyfikatora URI zwróconego wcześniej przez <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), program konwertuje bezwzględny identyfikator URI na identyfikator URI względem podstawowego prefiksu URI. |

W przypadku <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> zdarzenia program <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> dostarcza następujące informacje dotyczące zdarzeń nawigacyjnych:

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Adres URL nowej lokalizacji.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Jeśli `true` Blazor przechwytuje nawigację z przeglądarki. Jeśli `false` <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> wystąpiła nawigacja.

Następujący składnik:

* Przechodzi do `Counter` składnika aplikacji ( `Pages/Counter.razor` ), gdy przycisk jest wybierany przy użyciu <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> .
* Obsługuje zdarzenie zmiany lokalizacji przez subskrybowanie do <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .
  * Metoda jest odłączana, `HandleLocationChanged` gdy `Dispose` jest wywoływana przez platformę. Odłączanie metody zezwala na wyrzucanie elementów bezużytecznych składnika.
  * Implementacja rejestratora rejestruje następujące informacje po wybraniu przycisku:

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

`Pages/Navigate.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Navigate.razor)]

::: moniker-end

Aby uzyskać więcej informacji na temat usuwania składników, zobacz <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .

## <a name="query-string-and-parse-parameters"></a>Ciąg zapytania i parametry analizy

Ciąg zapytania żądania jest uzyskiwany z <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> Właściwości:

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

Aby przeanalizować parametry ciągu zapytania:

* Aplikacja może korzystać z <xref:Microsoft.AspNetCore.WebUtilities> interfejsu API. Jeśli interfejs API nie jest dostępny dla aplikacji, Dodaj odwołanie do pakietu w pliku projektu aplikacji dla [Microsoft. AspNetCore. webutilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).
* Uzyskaj wartość po przeanalizowaniu ciągu zapytania za pomocą elementu <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .

Poniższy `ParseQueryString` przykład składnika analizuje kod parametru ciągu zapytania o nazwie `ship` . Na przykład para klucz-wartość ciągu zapytania adresu URL `?ship=Tardis` przechwytuje wartość `Tardis` w `queryValue` . W poniższym przykładzie przejdź do aplikacji przy użyciu adresu URL `https://localhost:5001/parse-query-string?ship=Tardis` .

`Pages/ParseQueryString.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-component"></a>`NavLink` cm6long

<xref:Microsoft.AspNetCore.Components.Routing.NavLink>Podczas tworzenia linków nawigacji Użyj składnika zamiast elementów hiperlinków HTML ( `<a>` ). <xref:Microsoft.AspNetCore.Components.Routing.NavLink>Składnik zachowuje się jak `<a>` element, z wyjątkiem przełączenia `active` klasy CSS w zależności od tego, czy jest on `href` zgodny z bieżącym adresem URL. `active`Klasa pomaga użytkownikowi zrozumieć, która strona jest aktywną stroną między wyświetlanymi łączami nawigacji. Opcjonalnie Przypisz nazwę klasy CSS do, aby <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> zastosować niestandardową klasę CSS do renderowanego łącza, gdy bieżąca trasa jest zgodna z `href` .

Poniższy `NavMenu` składnik tworzy [`Bootstrap`](https://getbootstrap.com/docs/) pasek nawigacyjny, który pokazuje, jak używać <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składników:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

> [!NOTE]
> `NavMenu`Składnik ( `NavMenu.razor` ) znajduje się w `Shared` folderze aplikacji wygenerowanej na podstawie Blazor szablonów projektu.

Dostępne są dwie <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> Opcje, które można przypisać do `Match` atrybutu `<NavLink>` elementu:

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Jest aktywny, gdy jest zgodny z całym bieżącym adresem URL.
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*ustawienie domyślne*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> jest aktywny, gdy pasuje do dowolnego PREFIKSU bieżącego adresu URL.

W poprzednim przykładzie Strona główna <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` odpowiada GŁÓWNEmu adresowi URL i odbiera tylko `active` klasy CSS przy domyślnym adresie URL ścieżki podstawowej aplikacji (na przykład `https://localhost:5001/` ). Druga <xref:Microsoft.AspNetCore.Components.Routing.NavLink> otrzymuje klasę, `active` gdy użytkownik odwiedzi dowolny adres URL z `component` prefiksem (na przykład `https://localhost:5001/component` i `https://localhost:5001/component/another-segment` ).

Dodatkowe <xref:Microsoft.AspNetCore.Components.Routing.NavLink> atrybuty składników są przenoszone do renderowanego tagu zakotwiczenia. W poniższym przykładzie <xref:Microsoft.AspNetCore.Components.Routing.NavLink> składnik zawiera `target` atrybut:

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

Renderuje następujący znacznik HTML:

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> Ze względu na sposób, który Blazor renderuje zawartość podrzędną, `NavLink` składniki renderowania wewnątrz `for` pętli wymagają zmiennej lokalnego indeksu, jeśli zmienna pętli zwiększania jest używana w `NavLink` zawartości składnika (potomne):
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> Użycie zmiennej index w tym scenariuszu jest wymaganiem dla **dowolnego** składnika podrzędnego, który używa zmiennej pętli w jej [zawartości podrzędnej](xref:blazor/components/index#child-content), a nie tylko `NavLink` składnika.
>
> Alternatywnie możesz użyć `foreach` pętli z <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core integracja z routingiem punktu końcowego

*Ta sekcja dotyczy tylko Blazor Server aplikacji.*

Blazor Server Program jest zintegrowany z [ASP.NET Core routingiem punktów końcowych](xref:fundamentals/routing). Aplikacja ASP.NET Core jest skonfigurowana do akceptowania połączeń przychodzących dla składników interaktywnych za pomocą <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> programu w programie `Startup.Configure` .

`Startup.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/5.x/Startup.cs?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

::: moniker-end

Typowa konfiguracja polega na kierowaniu wszystkich żądań do Razor strony, które pełnią rolę hosta dla części aplikacji po stronie serwera Blazor Server . Zgodnie z Konwencją strona *hosta* jest zazwyczaj nazywana `_Host.cshtml` w `Pages` folderze aplikacji.

Trasa określona w pliku hosta jest nazywana *trasą rezerwową* , ponieważ działa z niskim priorytetem w dopasowaniu tras. Trasa rezerwowa jest używana, gdy inne trasy nie są zgodne. Dzięki temu aplikacja może korzystać z innych kontrolerów i stron bez zakłócania routingu składników w Blazor Server aplikacji.

Aby uzyskać informacje na temat konfigurowania <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> dla hosta niebędącego głównym adresem URL, zobacz <xref:blazor/host-and-deploy/index#app-base-path> .
