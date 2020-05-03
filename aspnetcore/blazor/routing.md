---
title: Routing Blazor ASP.NET Core
author: guardrex
description: Dowiedz się, jak kierować żądania w aplikacjach i informacje o składniku NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 291ec33f951008df10f85336c7abd0b3d0a50bbc
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727716"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Routing Blazor

Autor [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Dowiedz się, jak kierować żądania oraz jak używać `NavLink` składnika do tworzenia linków nawigacji w aplikacjach Blazor.

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core integracja z routingiem punktu końcowego

Serwer Blazor jest zintegrowany z [routingiem punktu końcowego ASP.NET Core](xref:fundamentals/routing). Aplikacja ASP.NET Core jest skonfigurowana do akceptowania połączeń przychodzących dla składników interaktywnych za `MapBlazorHub` pomocą `Startup.Configure`programu w programie:

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

Najbardziej typową konfiguracją jest kierowanie wszystkich żądań do strony Razor, która działa jako host dla części serwerowej aplikacji Blazor Server. Zgodnie z Konwencją strona *hosta* ma zwykle nazwę *_Host. cshtml*. Trasa określona w pliku hosta jest nazywana *trasą rezerwową* , ponieważ działa z niskim priorytetem w dopasowaniu tras. Trasa rezerwowa jest brana pod uwagę, gdy inne trasy nie są zgodne. Dzięki temu aplikacja może korzystać z innych kontrolerów i stron bez zakłócania działania aplikacji serwera Blazor.

## <a name="route-templates"></a>Szablony tras

`Router` Składnik umożliwia kierowanie do każdego składnika z określoną trasą. `Router` Składnik pojawi się w pliku *App. Razor* :

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

Gdy plik *Razor* z `@page` dyrektywą jest kompilowany, wygenerowana Klasa jest dostarczana z <xref:Microsoft.AspNetCore.Components.RouteAttribute> określeniem szablonu trasy.

W środowisku uruchomieniowym `RouteView` składnik:

* Odbiera `RouteData` od siebie `Router` wraz z dowolnymi żądanymi parametrami.
* Renderuje określony składnik za pomocą układu (lub opcjonalnego układu domyślnego) przy użyciu określonych parametrów.

Opcjonalnie można określić `DefaultLayout` parametr z klasą układu, która ma być używana dla składników, które nie określają układu. Domyślne szablony Blazor określają `MainLayout` składnik. *MainLayout. Razor* znajduje się w folderze *udostępnionym* projektu szablonu. Aby uzyskać więcej informacji na temat układów <xref:blazor/layouts>, zobacz.

Do składnika można zastosować wiele szablonów tras. Poniższy składnik odpowiada na żądania dla `/BlazorRoute` i: `/DifferentBlazorRoute`

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Aby adresy URL zostały poprawnie rozpoznane, aplikacja musi `<base>` zawierać tag w pliku *wwwroot/index.html* (Blazor Webassembly) lub *Pages/_Host. cshtml* (Blazor Server) z ścieżką bazową aplikacji określoną w `href` atrybucie (`<base href="/">`). Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Podaj zawartość niestandardową, jeśli nie można odnaleźć zawartości

`Router` Składnik umożliwia aplikacji określenie zawartości niestandardowej, jeśli nie można odnaleźć zawartości dla żądanej trasy.

W pliku *App. Razor* Ustaw zawartość niestandardową w parametrze `NotFound` szablonu `Router` składnika:

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

Zawartość `<NotFound>` tagów może zawierać dowolne elementy, takie jak inne składniki interaktywne. Aby zastosować domyślny układ do `NotFound` zawartości, zobacz. <xref:blazor/layouts>

## <a name="route-to-components-from-multiple-assemblies"></a>Kierowanie do składników z wielu zestawów

Użyj parametru `AdditionalAssemblies` , aby określić dodatkowe zestawy dla `Router` składnika do uwzględnienia podczas wyszukiwania składników routingu. Określone zestawy są traktowane jako uzupełnienie `AppAssembly`określonego zestawu. W poniższym przykładzie `Component1` jest składnikiem rutowanym zdefiniowanym w bibliotece klas, do której się odwołuje. W poniższym `AdditionalAssemblies` przykładzie przedstawiono obsługę routingu dla `Component1`:

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Parametry trasy

Router używa parametrów trasy do wypełniania odpowiednich parametrów składnika o tej samej nazwie (bez uwzględniania wielkości liter):

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

Parametry opcjonalne nie są obsługiwane. W `@page` poprzednim przykładzie są stosowane dwie dyrektywy. Pierwszy zezwala na nawigowanie do składnika bez parametru. Druga `@page` dyrektywa przyjmuje parametr `{text}` Route i przypisuje wartość do `Text` właściwości.

## <a name="route-constraints"></a>Ograniczenia trasy

Ograniczenie trasy wymusza dopasowanie typu w segmencie trasy do składnika.

W poniższym przykładzie trasa do `Users` składnika dopasowuje się tylko wtedy, gdy:

* Segment `Id` trasy jest obecny w adresie URL żądania.
* `Id` Segment jest liczbą całkowitą (`int`).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

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
> Ograniczenia trasy, które weryfikują adres URL i są konwertowane na typ CLR (takie `int` jak `DateTime`lub), zawsze używają niezmiennej kultury. W tych ograniczeniach przyjęto założenie, że adres URL nie jest Lokalizowalny.

### <a name="routing-with-urls-that-contain-dots"></a>Routing z adresami URL zawierającymi kropki

W aplikacjach serwera Blazor domyślną trasą w *_Host. cshtml* jest `/` (`@page "/"`). Adres URL żądania, który zawiera kropkę`.`() nie pasuje do trasy domyślnej, ponieważ adres URL wygląda na żądanie pliku. Aplikacja Blazor zwraca *404 — nie odnaleziono* odpowiedzi dla pliku statycznego, który nie istnieje. Aby użyć tras zawierających kropkę, skonfiguruj *_Host. cshtml* przy użyciu następującego szablonu trasy:

```cshtml
@page "/{**path}"
```

`"/{**path}"` Szablon zawiera:

* Podwójna gwiazdka *catch-all* (`**`) do przechwytywania ścieżki między wieloma granicami folderów bez kodowania ukośników (`/`).
* `path`Nazwa parametru trasy.

> [!NOTE]
> Składnia *catch-all* parametru (`*`/`**`) **nie** jest obsługiwana w składnikach Razor (*. Razor*).

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Składnik NavLink

Podczas tworzenia `NavLink` linków nawigacji Użyj składnika zamiast elementów hiperlinków HTML`<a>`(). Składnik zachowuje się jak `<a>` element, z wyjątkiem przełączenia klasy `active` CSS w zależności od tego, czy `href` jest on zgodny z bieżącym adresem URL. `NavLink` `active` Klasa pomaga użytkownikowi zrozumieć, która strona jest aktywną stroną między wyświetlanymi łączami nawigacji.

Poniższy `NavMenu` składnik tworzy pasek nawigacyjny [Bootstrap](https://getbootstrap.com/docs/) , który pokazuje, jak używać `NavLink` składników:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Dostępne są dwie `NavLinkMatch` opcje, które można przypisać do `Match` atrybutu `<NavLink>` elementu:

* `NavLinkMatch.All`&ndash; Jest `NavLink` aktywny, gdy jest zgodny z całym bieżącym adresem URL.
* `NavLinkMatch.Prefix`(*ustawienie domyślne*) &ndash; Jest `NavLink` aktywny, gdy pasuje do dowolnego prefiksu bieżącego adresu URL.

W poprzednim przykładzie Strona główna `NavLink` `href=""` odpowiada głównemu adresowi URL i odbiera tylko klasy `active` CSS przy domyślnym adresie URL ścieżki podstawowej aplikacji (na przykład `https://localhost:5001/`). Druga `NavLink` otrzymuje klasę, `active` gdy użytkownik odwiedzi dowolny adres URL z `MyComponent` prefiksem (na przykład `https://localhost:5001/MyComponent` i `https://localhost:5001/MyComponent/AnotherSegment`).

Dodatkowe `NavLink` atrybuty składników są przenoszone do renderowanego tagu zakotwiczenia. W poniższym przykładzie `NavLink` składnik zawiera `target` atrybut:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Renderuje następujący znacznik HTML:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>Pomoc dotycząca stanu identyfikatora URI i nawigacji

Służy <xref:Microsoft.AspNetCore.Components.NavigationManager> do pracy z identyfikatorami URI i nawigacją w kodzie C#. `NavigationManager`zawiera zdarzenie i metody przedstawione w poniższej tabeli.

| Członek | Opis |
| ------ | ----------- |
| Adresu | Pobiera bieżący bezwzględny identyfikator URI. |
| BaseUri | Pobiera podstawowy identyfikator URI (z końcowym ukośnikiem), który można dołączać do względnych ścieżek URI w celu utworzenia bezwzględnego identyfikatora URI. `BaseUri` Zazwyczaj `href` odpowiada atrybutowi `<base>` elementu dokumentu w *wwwroot/index.html* (Blazor webassembly) lub *Pages/_Host. cshtml* (Blazor serwer). |
| Typu NavigateTo | Przechodzi do określonego identyfikatora URI. Jeśli `forceLoad` jest `true`:<ul><li>Routing po stronie klienta jest pomijany.</li><li>W przeglądarce wymuszone jest załadowanie nowej strony z serwera, niezależnie od tego, czy identyfikator URI jest zwykle obsługiwany przez router po stronie klienta.</li></ul> |
| LocationChanged | Zdarzenie, które jest wyzwalane po zmianie lokalizacji nawigacji. |
| ToAbsoluteUri | Konwertuje względny identyfikator URI na bezwzględny identyfikator URI. |
| <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Przy użyciu podstawowego identyfikatora URI (na przykład identyfikatora URI zwróconego wcześniej `GetBaseUri`przez), program konwertuje bezwzględny identyfikator URI na identyfikator URI względem podstawowego prefiksu URI. |

Poniższy składnik przechodzi do `Counter` składnika aplikacji po wybraniu przycisku:

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

Poniższy składnik obsługuje zdarzenie zmiany lokalizacji. `HandleLocationChanged` Metoda jest odłączana, gdy `Dispose` jest wywoływana przez platformę. Odłączanie metody zezwala na wyrzucanie elementów bezużytecznych składnika.

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs>zawiera następujące informacje o zdarzeniu:

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>&ndash; Adres URL nowej lokalizacji.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; `true`Jeśli Blazor przechwycono nawigację z przeglądarki. Jeśli `false`, element [nawigacyjny. typu NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) spowodował wystąpienie nawigacji.

Aby uzyskać więcej informacji na temat usuwania składników <xref:blazor/lifecycle#component-disposal-with-idisposable>, zobacz.
