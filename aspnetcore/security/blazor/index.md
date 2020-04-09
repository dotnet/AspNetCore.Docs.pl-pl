---
title: uwierzytelnianie i Blazor autoryzacja ASP.NET Core
author: guardrex
description: Dowiedz Blazor się więcej o scenariuszach uwierzytelniania i autoryzacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: 04bbf20d1d848edfa98e719f316b790c812bfd95
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80501313"
---
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a>uwierzytelnianie i Blazor autoryzacja ASP.NET Core

Przez [Steve Sanderson](https://github.com/SteveSandersonMS) i [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> Aby uzyskać wskazówki zawarte w Blazor tym artykule, który ma zastosowanie Blazor do WebAssembly, ASP.NET Core WebAssembly szablon w wersji 3.2 lub nowszej jest wymagane. Jeśli nie używasz programu Visual Studio w wersji 16.6 Blazor Preview 2 lub nowszej, <xref:blazor/get-started>uzyskaj najnowszy szablon WebAssembly, postępając zgodnie ze wskazówkami w programie .

ASP.NET Core obsługuje konfigurację i zarządzanie Blazor zabezpieczeniami w aplikacjach.

Scenariusze zabezpieczeń Blazor różnią Blazor się między aplikacjami Server i WebAssembly. Ponieważ Blazor aplikacje serwera działają na serwerze, kontrole autoryzacji są w stanie określić:

* Opcje interfejsu użytkownika prezentowane użytkownikowi (na przykład, które pozycje menu są dostępne dla użytkownika).
* Reguły dostępu dla obszarów aplikacji i składników.

BlazorAplikacje WebAssembly są uruchamiane na kliencie. Autoryzacja jest używana *tylko* do określenia, które opcje interfejsu użytkownika mają być wyświetlane. Ponieważ kontrole po stronie klienta mogą być modyfikowane Blazor lub pomijane przez użytkownika, aplikacja WebAssembly nie może wymuszać reguł dostępu do autoryzacji.

[Konwencje autoryzacji stron maszynki do golenia](xref:security/authorization/razor-pages-authorization) nie mają zastosowania do składników razor rutable. Jeśli składnik Razor niesydywalny jest [osadzony na stronie,](xref:blazor/integrate-components#render-components-from-a-page-or-view)konwencje autoryzacji strony pośrednio wpływają na składnik Razor wraz z resztą zawartości strony.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601>i <xref:Microsoft.AspNetCore.Identity.UserManager%601> nie są obsługiwane w komponentach Razor.

## <a name="authentication"></a>Authentication

Blazorużywa istniejących mechanizmów uwierzytelniania ASP.NET Core w celu ustalenia tożsamości użytkownika. Dokładny mechanizm zależy od Blazor sposobu hostowanie Blazor aplikacji, WebAssembly lub Blazor Server.

### <a name="opno-locblazor-webassembly-authentication"></a>BlazorUwierzytelnianie WebAssembly

W Blazor aplikacjach WebAssembly można pominąć sprawdzanie uwierzytelniania, ponieważ cały kod po stronie klienta może być modyfikowany przez użytkowników. To samo dotyczy wszystkich technologii aplikacji po stronie klienta, w tym struktur JavaScript SPA lub aplikacji natywnych dla dowolnego systemu operacyjnego.

Dodaj następujące elementy:

* Odwołanie do pakietu dla [pliku projektu Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) do pliku projektu aplikacji.
* Obszar `Microsoft.AspNetCore.Components.Authorization` nazw do pliku *_Imports.razor* aplikacji.

Aby obsłużyć uwierzytelnianie, `AuthenticationStateProvider` implementacja wbudowanej lub niestandardowej usługi jest omówiona w poniższych sekcjach.

Aby uzyskać więcej informacji na temat <xref:security/blazor/webassembly/index>tworzenia aplikacji i konfiguracji, zobacz .

### <a name="opno-locblazor-server-authentication"></a>BlazorUwierzytelnianie serwera

BlazorAplikacje serwerowe działają za pośrednictwem połączenia SignalRw czasie rzeczywistym, które jest tworzone przy użyciu . [Uwierzytelnianie SignalRw aplikacjach opartych](xref:signalr/authn-and-authz) na aplikacjach jest obsługiwane po nawiązaniu połączenia. Uwierzytelnianie może opierać się na pliku cookie lub innym tokenie nośnika.

Aby uzyskać więcej informacji na temat <xref:security/blazor/server>tworzenia aplikacji i konfiguracji, zobacz .

## <a name="authenticationstateprovider-service"></a>Usługa AuthenticationStateProvider

Wbudowana `AuthenticationStateProvider` usługa uzyskuje dane o stanie uwierzytelniania z `HttpContext.User`ASP.NET Core. W ten sposób stan uwierzytelniania integruje się z istniejącymi mechanizmami uwierzytelniania ASP.NET Core.

`AuthenticationStateProvider`jest podstawową usługą `AuthorizeView` używaną przez składnik i `CascadingAuthenticationState` składnik do uzyskania stanu uwierzytelniania.

Zazwyczaj nie używasz `AuthenticationStateProvider` bezpośrednio. Użyj [authorizeView składnika](#authorizeview-component) lub [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) podejścia opisane w dalszej części tego artykułu. Główną wadą `AuthenticationStateProvider` przy użyciu bezpośrednio jest to, że składnik nie jest powiadamiany automatycznie, jeśli podstawowe dane stanu uwierzytelniania zmiany.

Usługa `AuthenticationStateProvider` może dostarczyć dane bieżącego <xref:System.Security.Claims.ClaimsPrincipal> użytkownika, jak pokazano w poniższym przykładzie:

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type &ndash; @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

Jeśli `user.Identity.IsAuthenticated` `true` jest i ponieważ <xref:System.Security.Claims.ClaimsPrincipal>użytkownik jest , oświadczenia mogą być wyliczone i członkostwa w rolach oceniane.

Aby uzyskać więcej informacji na temat iniekcji zależności (DI) i usług, zobacz <xref:blazor/dependency-injection> i <xref:fundamentals/dependency-injection>.

## <a name="implement-a-custom-authenticationstateprovider"></a>Implementowanie niestandardowego narzędzia authenticationstateprovider

Jeśli aplikacja wymaga niestandardowego `AuthenticationStateProvider` dostawcy, zaimplementuj `GetAuthenticationStateAsync`i zastąp:

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

W Blazor aplikacji WebAssembly `CustomAuthStateProvider` usługa jest `Main` zarejestrowana w *Program.cs:*

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

W Blazor aplikacji Server `CustomAuthStateProvider` usługa jest `Startup.ConfigureServices`zarejestrowana w :

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

`CustomAuthStateProvider` W poprzednim przykładzie wszyscy użytkownicy są uwierzytelnieni `mrfibuli`przy użyciu nazwy użytkownika .

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Uwidacznianie stanu uwierzytelniania jako parametru kaskadowego

Jeśli dane o stanie uwierzytelniania są wymagane dla logiki proceduralnej, na przykład podczas wykonywania akcji wyzwalanej `Task<AuthenticationState>`przez użytkownika, uzyskaj dane stanu uwierzytelniania, definiując parametr kaskadowy typu:

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

Jeśli `user.Identity.IsAuthenticated` `true`tak , oświadczenia mogą być wyliczone i członkostwa w rolach oceniane.

Konfigurowanie `Task<AuthenticationState>` parametru kaskadowego `AuthorizeRouteView` `CascadingAuthenticationState` przy użyciu `App` i komponentów w składniku (*App.brzytwa):*

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

W Blazor aplikacji WebAssembly dodaj usługi dla `Program.Main`opcji i autoryzacji do:

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

W Blazor aplikacji Server usługi dla opcji i autoryzacji są już obecne, więc nie są wymagane żadne dalsze działania.

## <a name="authorization"></a>Autoryzacja

Po uwierzytelnieniu użytkownika *reguły autoryzacji* są stosowane do kontrolowania, co użytkownik może zrobić.

Dostęp jest zazwyczaj przyznawany lub odmawiany na podstawie tego, czy:

* Użytkownik jest uwierzytelniony (zalogowany).
* Użytkownik jest w *roli*.
* Użytkownik ma *oświadczenie*.
* *Zasada* jest spełniona.

Każda z tych koncepcji jest taka sama jak w aplikacji ASP.NET Core MVC lub Razor Pages. Aby uzyskać więcej informacji na temat ASP.NET podstawowych zabezpieczeń, zobacz artykuły w obszarze [ASP.NET Bezpieczeństwo podstawowe i tożsamość](xref:security/index).

## <a name="authorizeview-component"></a>Autoryzując składnikView

Składnik `AuthorizeView` wybiórczo wyświetla interfejs użytkownika w zależności od tego, czy użytkownik jest upoważniony do jego wyświetlania. Takie podejście jest przydatne, gdy wystarczy *wyświetlić* dane dla użytkownika i nie trzeba używać tożsamości użytkownika w logice proceduralnej.

Składnik udostępnia zmienną `context` typu `AuthenticationState`, której można użyć, aby uzyskać dostęp do informacji o zalogowanym użytkowniku:

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Można również podać inną zawartość do wyświetlania, jeśli użytkownik nie jest uwierzytelniony:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

Składnik `AuthorizeView` może być używany `NavMenu` w składniku (*Shared/NavMenu.brzytwa*) do wyświetlania elementu listy (`<li>...</li>`) dla `NavLink`, ale należy pamiętać, że takie podejście usuwa tylko element listy z renderowanego wyjścia. Nie uniemożliwia użytkownikowi przechodzenia do składnika.

Zawartość `<Authorized>` i `<NotAuthorized>` tagi mogą zawierać dowolne elementy, takie jak inne składniki interaktywne.

Warunki autoryzacji, takie jak role lub zasady sterujące opcjami interfejsu użytkownika lub dostępem, są opisane w sekcji [Autoryzacja.](#authorization)

Jeśli warunki autoryzacji nie `AuthorizeView` są określone, używa zasad domyślnych i traktuje:

* Uwierzytelnieni (zalogowani) użytkownicy jako autoryzowani.
* Nieuwierzyleni (wylogowani) użytkownicy jako nieautoryzowani.

### <a name="role-based-and-policy-based-authorization"></a>Autoryzacja oparta na rolach i zasadach

Składnik `AuthorizeView` obsługuje *autoryzację opartą na rolach* lub *zasadach.*

W przypadku autoryzacji opartej na rolach należy użyć parametru: `Roles`

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Aby uzyskać więcej informacji, zobacz <xref:security/authorization/roles>.

W przypadku autoryzacji opartej na zasadach należy użyć parametru: `Policy`

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

Autoryzacja oparta na oświadczeniach jest szczególnym przypadkiem autoryzacji opartej na zasadach. Na przykład można zdefiniować zasady, które wymagają od użytkowników, aby mieć pewne oświadczenie. Aby uzyskać więcej informacji, zobacz <xref:security/authorization/policies>.

Te interfejsy API mogą być Blazor używane Blazor w aplikacjach Server lub WebAssembly.

Jeśli nie `Roles` `Policy` jest ani `AuthorizeView` nie jest określony, używa zasad domyślnych.

### <a name="content-displayed-during-asynchronous-authentication"></a>Zawartość wyświetlana podczas uwierzytelniania asynchroniowego

Blazorpozwala na *asynchroniczne określanie*stanu uwierzytelniania. Podstawowym scenariuszem tego podejścia Blazor jest w webassembly aplikacji, które sprawiają, że żądanie do zewnętrznego punktu końcowego do uwierzytelniania.

Gdy uwierzytelnianie jest `AuthorizeView` w toku, domyślnie nie wyświetla zawartości. Aby wyświetlić zawartość podczas `<Authorizing>` uwierzytelniania, należy użyć elementu:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

Takie podejście zwykle nie ma Blazor zastosowania do aplikacji serwera. BlazorAplikacje serwera znają stan uwierzytelniania zaraz po ustanowieniu stanu. `Authorizing`zawartość może być Blazor dostarczana w `AuthorizeView` składniku aplikacji Server, ale nigdy nie jest wyświetlana.

## <a name="authorize-attribute"></a>Atrybut [Autoryzuj]

Atrybut `[Authorize]` może być używany w składnikach Razor:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Używać `[Authorize]` tylko `@page` na komponentach, do których Blazor dotarł router. Autoryzacja jest wykonywana tylko jako aspekt routingu, a *nie* dla składników podrzędnych renderowanych na stronie. Aby autoryzować wyświetlanie określonych części na `AuthorizeView` stronie, należy użyć.

Atrybut `[Authorize]` obsługuje również autoryzację opartą na rolach lub zasadach. W przypadku autoryzacji opartej na rolach należy użyć parametru: `Roles`

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

W przypadku autoryzacji opartej na zasadach należy użyć parametru: `Policy`

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Jeśli nie `Roles` `Policy` jest ani, ani nie jest określony, `[Authorize]` używa domyślnej zasady, która domyślnie jest do leczenia:

* Uwierzytelnieni (zalogowani) użytkownicy jako autoryzowani.
* Nieuwierzyleni (wylogowani) użytkownicy jako nieautoryzowani.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Dostosowywanie nieautoryzowanej zawartości za pomocą składnika Router

Składnik `Router` w połączeniu ze `AuthorizeRouteView` składnikiem umożliwia aplikacji określenie zawartości niestandardowej, jeśli:

* Nie znaleziono zawartości.
* Użytkownik ulegnie `[Authorize]` awarii warunek stosowany do składnika. Atrybut `[Authorize]` jest omówiony w sekcji [ `[Authorize]` atrybutu.](#authorize-attribute)
* Uwierzytelnianie asynchroniczne jest w toku.

W domyślnym Blazor szablonie `App` projektu serwera składnik *(App.brzytwa)* pokazuje, jak ustawić zawartość niestandardową:

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

Zawartość `<NotFound>`programu `<NotAuthorized>`, `<Authorizing>` i tagów może zawierać dowolne elementy, takie jak inne składniki interaktywne.

Jeśli `<NotAuthorized>` element nie jest określony, `AuthorizeRouteView` używa następującego komunikatu rezerwowego:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Powiadomienie o zmianach stanu uwierzytelniania

Jeśli aplikacja określa, że podstawowe dane o stanie uwierzytelniania uległy zmianie (na przykład, ponieważ użytkownik wylogował się lub `NotifyAuthenticationStateChanged` inny `AuthenticationStateProvider` użytkownik zmienił swoje role), [niestandardowy AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) może opcjonalnie wywołać metodę w klasie podstawowej. To powiadamia konsumentów o danych stanu uwierzytelniania `AuthorizeView`(na przykład ) do rerender przy użyciu nowych danych.

## <a name="procedural-logic"></a>Logika proceduralna

Jeśli aplikacja jest wymagana do sprawdzania reguł autoryzacji w ramach logiki `Task<AuthenticationState>` proceduralnej, użyj <xref:System.Security.Claims.ClaimsPrincipal>kaskadowego parametru typu, aby uzyskać parametr użytkownika . `Task<AuthenticationState>`można łączyć z innymi `IAuthorizationService`usługami, takimi jak , w celu oceny zasad.

```razor
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> W Blazor składniku aplikacji WebAssembly `Microsoft.AspNetCore.Authorization` `Microsoft.AspNetCore.Components.Authorization` dodaj obszary nazw i:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Te obszary nazw mogą być dostarczane globalnie, dodając je do pliku *_Imports.razor* aplikacji.

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a>Autoryzacja Blazor w aplikacjach WebAssembly

W Blazor aplikacjach WebAssembly można pominąć sprawdzanie autoryzacji, ponieważ cały kod po stronie klienta może być modyfikowany przez użytkowników. To samo dotyczy wszystkich technologii aplikacji po stronie klienta, w tym struktur JavaScript SPA lub aplikacji natywnych dla dowolnego systemu operacyjnego.

**Zawsze wykonuj kontrole autoryzacji na serwerze w obrębie dowolnego punktu końcowego interfejsu API, do którego ma dostęp aplikacja po stronie klienta.**

Aby uzyskać więcej informacji, <xref:security/blazor/webassembly/index>zobacz artykuły w obszarze .

## <a name="troubleshoot-errors"></a>Rozwiązywanie problemów

Typowe błędy:

* **Autoryzacja wymaga parametru kaskadowego typu Task\<AuthenticationState>. Należy rozważyć użycie CascadingAuthenticationState do dostarczania tego.**

* **`null`wartość jest odbierana dla`authenticationStateTask`**

Jest prawdopodobne, że projekt nie został Blazor utworzony przy użyciu szablonu serwera z włączonym uwierzytelnianiem. Owiń `<CascadingAuthenticationState>` wokół jakiejś części drzewa interfejsu `App` użytkownika, na przykład w składniku *(App.brzytwa)* w następujący sposób:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

Dostarcza `CascadingAuthenticationState` parametr `Task<AuthenticationState>` kaskadowy, który z kolei otrzymuje `AuthenticationStateProvider` od podstawowej usługi DI.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* [Niesamowite Blazor: Uwierzytelniania](https://github.com/AdrienTorris/awesome-blazor#authentication) społeczności przykładowe linki
