---
title: Routing ASP.NET Blazor Core
author: guardrex
description: Dowiedz się, jak rozsyłać żądania w aplikacjach i o składniku NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 87579c88a37e0258921e199db2b5d8c7627f5499
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218898"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET rdzeń Blazor routingu

Przez [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Dowiedz się, jak rozsyłać żądania i jak używać tego składnika `NavLink` do tworzenia łączy nawigacyjnych w aplikacjach Blazor.

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET integracja routingu punktów końcowych core

Blazor Server jest zintegrowany [z ASP.NET Core Endpoint Routing](xref:fundamentals/routing). Aplikacja ASP.NET Core jest skonfigurowana do akceptowania połączeń przychodzących dla składników interaktywnych z `MapBlazorHub` w: `Startup.Configure`

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

Najbardziej typową konfiguracją jest kierowanie wszystkich żądań do strony Razor, która działa jako host dla części po stronie serwera aplikacji Blazor Server. Zgodnie z konwencją strona *hosta* nosi zwykle nazwę *_Host.cshtml*. Trasa określona w pliku hosta jest nazywana *trasą rezerwową,* ponieważ działa z niskim priorytetem w dopasowywaniu trasy. Trasa rezerwowa jest brana pod uwagę, gdy inne trasy nie są zgodne. Dzięki temu aplikacja może korzystać z innych kontrolerów i stron bez zakłócania aplikacji Blazor Server.

## <a name="route-templates"></a>Szablony tras

Komponent `Router` umożliwia routing do każdego komponentu o określonej trasie. Składnik `Router` pojawi się w pliku *App.razor:*

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

Po skompilowaniu pliku `@page` *.brzytwa* z dyrektywą <xref:Microsoft.AspNetCore.Components.RouteAttribute> wygenerowana klasa jest podana określająca szablon trasy.

W czasie wykonywania `RouteView` składnik:

* Odbiera `RouteData` od `Router` wraz z dowolnymi żądanymi parametrami.
* Renderuje określony komponent z jego układem (lub opcjonalnym układem domyślnym) przy użyciu określonych parametrów.

Opcjonalnie można określić `DefaultLayout` parametr z klasą układu, która będzie używana dla składników, które nie określają układu. Domyślne szablony Blazor `MainLayout` określają składnik. *MainLayout.brzytwa* znajduje się w folderze *udostępnionym* projektu szablonu. Aby uzyskać więcej informacji <xref:blazor/layouts>na temat układów, zobacz .

Do komponentu można zastosować wiele szablonów marszrut. Następujący składnik odpowiada na `/BlazorRoute` żądania `/DifferentBlazorRoute`i:

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Aby adresy URL zostały poprawnie rozwiązane, `<base>` aplikacja musi zawierać znacznik w pliku *wwwroot/index.html* (Blazor WebAssembly) lub *Pages/_Host.cshtml* (Blazor Server) ze ścieżką podstawową aplikacji określoną w `href` atrybucie (`<base href="/">`). Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Dostarczanie zawartości niestandardowej, gdy nie zostanie znaleziona zawartość

Składnik `Router` umożliwia aplikacji określenie zawartości niestandardowej, jeśli zawartość nie zostanie znaleziona dla żądanej trasy.

W pliku *App.razor* ustaw niestandardową `NotFound` zawartość w `Router` parametrze szablonu składnika:

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

Zawartość tagów `<NotFound>` może zawierać dowolne elementy, takie jak inne składniki interaktywne. Aby zastosować domyślny `NotFound` układ <xref:blazor/layouts>do zawartości, zobacz .

## <a name="route-to-components-from-multiple-assemblies"></a>Droga do komponentów z wielu złożeń

Parametr `AdditionalAssemblies` służy do określania dodatkowych `Router` złożeń dla komponentu, które mają być uwzględniane podczas wyszukiwania komponentów rutowalnych. Określone zestawy są uważane za `AppAssembly`oprócz -specified zestawu. W poniższym `Component1` przykładzie jest rutowalnym składnikiem zdefiniowanym w bibliotece klas, do których istnieje odwołanie. Poniższy `AdditionalAssemblies` przykład powoduje obsługę `Component1`routingu dla:

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Parametry trasy

Router używa parametrów trasy do wypełniania odpowiednich parametrów komponentu o tej samej nazwie (bez uwzględniania wielkości liter):

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

Parametry opcjonalne nie są obsługiwane. W `@page` poprzednim przykładzie stosowane są dwie dyrektywy. Pierwszy umożliwia nawigację do składnika bez parametru. Druga `@page` dyrektywa przyjmuje `{text}` parametr trasy i przypisuje `Text` wartość do właściwości.

## <a name="route-constraints"></a>Ograniczenia trasy

Ograniczenie trasy wymusza dopasowywanie typu w segmencie trasy do komponentu.

W poniższym przykładzie trasa `Users` do komponentu jest zgodna tylko wtedy, gdy:

* Segment `Id` trasy znajduje się w adresie URL żądania.
* Segment `Id` jest całkowitej liczby`int`( ).

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

Ograniczenia trasy pokazane w poniższej tabeli są dostępne. Aby uzyskać ograniczenia trasy, które pasują do kultury niezmiennej, zobacz ostrzeżenie poniżej tabeli, aby uzyskać więcej informacji.

| Ograniczenie | Przykład           | Przykładowe dopasowania                                                                  | Niezmienna<br>kultura<br>parowanie |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Nie                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Tak                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Tak                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Tak                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Tak                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Nie                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Tak                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Tak                              |

> [!WARNING]
> Ograniczenia trasy, które weryfikują adres URL i są `int` `DateTime`konwertowane na typ CLR (na przykład lub ) zawsze używają kultury niezmiennej. Te ograniczenia zakładają, że adres URL nie jest zlokalizowany.

### <a name="routing-with-urls-that-contain-dots"></a>Routing z adresami URL zawierającymi kropki

W aplikacjach Blazor Server domyślną trasą w *_Host.cshtml* jest `/` (`@page "/"`). Adres URL żądania zawierający`.`kropkę ( ) nie jest dopasowyany do domyślnej trasy, ponieważ adres URL wydaje się żądać pliku. Aplikacja Blazor zwraca *odpowiedź 404 — nie znaleziono* dla pliku statycznego, który nie istnieje. Aby użyć tras zawierających kropkę, należy skonfigurować *_Host.cshtml* z następującym szablonem trasy:

```cshtml
@page "/{**path}"
```

Szablon `"/{**path}"` zawiera:

* Podwójna gwiazdka *catch-all* składni`**`( ) do przechwytywania ścieżki przez wiele granic`/`folderów bez kodowania ukośniki do przodu ( ).
* `path`nazwę parametru trasy.

> [!NOTE]
> *Składnia parametrów catch-all* (`*`/`**`) **nie** jest obsługiwana w składnikach Razor (*.brzytwa*).

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Komponent NavLink

Podczas `NavLink` tworzenia łączy nawigacyjnych należy`<a>`używać składnika zamiast elementów hiperłącza HTML ( ) . Składnik `NavLink` zachowuje się `<a>` jak element, z tą `active` różnicą, że `href` przełącza klasę CSS na podstawie tego, czy jest zgodna z bieżącym adresem URL. Klasa `active` pomaga użytkownikowi zrozumieć, która strona jest aktywną stroną wśród wyświetlanych łączy nawigacyjnych.

Następujący `NavMenu` składnik tworzy pasek nawigacyjny [Bootstrap,](https://getbootstrap.com/docs/) który `NavLink` pokazuje, jak używać składników:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Istnieją dwie `NavLinkMatch` opcje, które można `Match` przypisać do `<NavLink>` atrybutu elementu:

* `NavLinkMatch.All`&ndash; Jest `NavLink` aktywny, gdy pasuje do całego bieżącego adresu URL.
* `NavLinkMatch.Prefix`(*domyślnie*) &ndash; Jest `NavLink` aktywny, gdy pasuje do dowolnego prefiksu bieżącego adresu URL.

W poprzednim przykładzie strona `NavLink` `href=""` główna pasuje do domowego `active` adresu URL i odbiera tylko klasę CSS `https://localhost:5001/`przy domyślnym adresie URL ścieżki podstawowej aplikacji (na przykład ). `NavLink` Drugi otrzymuje `active` klasę, gdy użytkownik odwiedza `MyComponent` dowolny adres URL `https://localhost:5001/MyComponent` z `https://localhost:5001/MyComponent/AnotherSegment`prefiksem (na przykład i ).

Dodatkowe `NavLink` atrybuty składnika są przekazywane do renderowanego tagu zakotwiczenia. W poniższym przykładzie `NavLink` składnik `target` zawiera atrybut:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Renderowane są następujące znaczniki HTML:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>Pomocnicy URI i stanu nawigacji

Służy <xref:Microsoft.AspNetCore.Components.NavigationManager> do pracy z identyfikatorami URI i nawigacji w kodzie języka C#. `NavigationManager`zawiera zdarzenie i metody pokazane w poniższej tabeli.

| Członek | Opis |
| ------ | ----------- |
| Identyfikator uri | Pobiera bieżący bezwzględny identyfikator URI. |
| Baseuri | Pobiera podstawowy identyfikator URI (z ukośnikiem kończącym), który może być dołączany do względnych ścieżek URI w celu wytworzenia bezwzględnego identyfikatora URI. `BaseUri` Zazwyczaj odpowiada atrybutowi `href` `<base>` elementu dokumentu w *wwwroot/index.html* (WebAssembly)Blazor lub *Pages/_Host.cshtml* (Blazor Serwer). |
| Navigateto | Przechodzi do określonego identyfikatora URI. Jeśli `forceLoad` `true`jest:<ul><li>Routing po stronie klienta jest pomijany.</li><li>Przeglądarka jest zmuszona do załadowania nowej strony z serwera, niezależnie od tego, czy identyfikator URI jest zwykle obsługiwany przez router po stronie klienta.</li></ul> |
| Locationchanged | Zdarzenie, które uruchamia się po zmianie lokalizacji nawigacji. |
| ToAbsoluteUri | Konwertuje względny identyfikator URI na bezwzględny identyfikator URI. |
| <span style="word-break:normal;word-wrap:normal">Ścieżka ToBaseRelativePath</span> | Biorąc pod uwagę podstawowy identyfikator URI (na `GetBaseUri`przykład identyfikator URI wcześniej zwrócony przez), konwertuje bezwzględny identyfikator URI na identyfikator URI względem podstawowego prefiksu URI. |

Następujący składnik przechodzi do składnika `Counter` aplikacji po wybraniu przycisku:

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

Poniższy składnik obsługuje zdarzenie zmiany lokalizacji. Metoda `HandleLocationChanged` jest odłączana, `Dispose` gdy jest wywoływana przez strukturę. Odłączanie metody zezwala na wyrzucanie elementów bezużytecznych składnika.

```razor
@implement IDisposable
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
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>&ndash; Jeśli `true` Blazor , przechwycono nawigację z przeglądarki. Jeśli `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) spowodowało nawigację.

Aby uzyskać więcej informacji <xref:blazor/lifecycle#component-disposal-with-idisposable>na temat usuwania komponentów, zobacz .
