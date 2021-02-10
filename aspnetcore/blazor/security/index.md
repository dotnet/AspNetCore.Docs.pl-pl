---
title: ASP.NET Core Blazor uwierzytelnianie i autoryzacja
author: guardrex
description: Dowiedz się więcej na temat Blazor scenariuszy uwierzytelniania i autoryzacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/index
ms.openlocfilehash: c786c00892772f9f0ce80c903bde495d4f2523f2
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106742"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>ASP.NET Core Blazor uwierzytelnianie i autoryzacja

[Steve Sanderson](https://github.com/SteveSandersonMS) i [Luke Latham](https://github.com/guardrex)

ASP.NET Core obsługuje konfigurację i zarządzanie zabezpieczeniami w Blazor aplikacjach.

Scenariusze zabezpieczeń różnią się w zależności od Blazor Server Blazor WebAssembly aplikacji. Ponieważ Blazor Server aplikacje są uruchamiane na serwerze, kontrole autoryzacji mogą określić:

* Opcje interfejsu użytkownika wyświetlane użytkownikowi (na przykład, które pozycje menu są dostępne dla użytkownika).
* Reguły dostępu do obszarów aplikacji i składników.

Blazor WebAssembly aplikacje są uruchamiane na kliencie. Autoryzacja jest używana *tylko* do określenia opcji interfejsu użytkownika, które mają być wyświetlane. Ponieważ testy po stronie klienta mogą być modyfikowane lub pomijane przez użytkownika, Blazor WebAssembly aplikacja nie może wymusić reguł dostępu autoryzacji.

[ Razor Konwencje autoryzacji stron](xref:security/authorization/razor-pages-authorization) nie dotyczą składników rutowanych Razor . Jeśli na Razor [stronie osadzony](xref:blazor/components/prerendering-and-integration)jest składnik nieobsługujący routingu, konwencje autoryzacji stron pośrednio wpływają na Razor składnik wraz z resztą zawartości strony.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601> i <xref:Microsoft.AspNetCore.Identity.UserManager%601> nie są obsługiwane w Razor składnikach.

## <a name="authentication"></a>Authentication

Blazor program używa istniejących mechanizmów uwierzytelniania ASP.NET Core do ustanowienia tożsamości użytkownika. Dokładny mechanizm zależy od tego, w jaki sposób Blazor aplikacja jest hostowana Blazor WebAssembly Blazor Server .

### <a name="blazor-webassembly-authentication"></a>Blazor WebAssembly ponowne

W Blazor WebAssembly aplikacjach sprawdzanie uwierzytelniania może być pomijane, ponieważ każdy kod po stronie klienta może być modyfikowany przez użytkowników. Jest to samo prawdziwe dla wszystkich technologii aplikacji po stronie klienta, w tym dla struktur SPA skryptów JavaScript lub natywnych aplikacji dla dowolnego systemu operacyjnego.

Dodaj następujące elementy:

* Odwołanie do pakietu dla [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) pliku projektu aplikacji.
* `Microsoft.AspNetCore.Components.Authorization`Przestrzeń nazw do `_Imports.razor` pliku aplikacji.

Aby obsłużyć uwierzytelnianie, użyj wbudowanej lub niestandardowej <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> usługi zawartej w poniższych sekcjach.

Aby uzyskać więcej informacji na temat tworzenia aplikacji i konfiguracji, zobacz <xref:blazor/security/webassembly/index> .

### <a name="blazor-server-authentication"></a>Blazor Server ponowne

Blazor Server aplikacje działają za pośrednictwem połączenia w czasie rzeczywistym, które zostało utworzone za pomocą programu SignalR . [Uwierzytelnianie w SignalR aplikacjach opartych na protokole](xref:signalr/authn-and-authz) jest obsługiwane, gdy połączenie zostanie nawiązane. Uwierzytelnianie może być oparte na cookie lub innym tokenie okaziciela.

Wbudowana <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> Usługa dla Blazor Server aplikacji uzyskuje dane stanu uwierzytelniania z ASP.NET Core `HttpContext.User` . Jest to sposób integracji stanu uwierzytelniania z istniejącymi mechanizmami uwierzytelniania ASP.NET Core.

Aby uzyskać więcej informacji na temat tworzenia aplikacji i konfiguracji, zobacz <xref:blazor/security/server/index> .

## <a name="authenticationstateprovider-service"></a>Usługa AuthenticationStateProvider

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> to podstawowa usługa używana przez <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> składnik i <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> składnik do uzyskiwania stanu uwierzytelniania.

Zwykle nie są używane <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> bezpośrednio. Użyj [ `AuthorizeView` składnika](#authorizeview-component) lub [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) podejścia opisanego w dalszej części tego artykułu. Główną wadą zwrotu z używania <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> bezpośrednio jest to, że składnik nie jest automatycznie powiadamiany o zmianach danych stanu uwierzytelniania.

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>Usługa może udostępniać dane bieżącego użytkownika <xref:System.Security.Claims.ClaimsPrincipal> , jak pokazano w następującym przykładzie:

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
            <li>@claim.Type: @claim.Value</li>
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

Jeśli `user.Identity.IsAuthenticated` jest `true` i ponieważ użytkownik jest <xref:System.Security.Claims.ClaimsPrincipal> , można wyliczyć oświadczenia i członkostwo w rolach.

Aby uzyskać więcej informacji na temat iniekcji zależności (DI) i usług, zobacz <xref:blazor/fundamentals/dependency-injection> i <xref:fundamentals/dependency-injection> .

## <a name="implement-a-custom-authenticationstateprovider"></a>Implementowanie niestandardowego AuthenticationStateProvider

Jeśli aplikacja wymaga dostawcy niestandardowego, zaimplementuj <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> i Przesłoń `GetAuthenticationStateAsync` :

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

W Blazor WebAssembly aplikacji `CustomAuthStateProvider` Usługa jest zarejestrowana w `Main` `Program.cs` :

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

W Blazor Server aplikacji `CustomAuthStateProvider` Usługa jest zarejestrowana w `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

`CustomAuthStateProvider`W powyższym przykładzie wszyscy użytkownicy są uwierzytelniani przy użyciu nazwy użytkownika `mrfibuli` .

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Uwidacznianie stanu uwierzytelniania jako parametru kaskadowego

Jeśli dane stanu uwierzytelniania są wymagane dla logiki proceduralnej, na przykład podczas wykonywania akcji wyzwalanej przez użytkownika, uzyskaj dane stanu uwierzytelniania przez zdefiniowanie parametru kaskadowego typu `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` :

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

Jeśli `user.Identity.IsAuthenticated` jest `true` , oświadczenia mogą być wyliczane i członkostwo w rolach oceniane.

Skonfiguruj `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` parametr kaskadowy przy użyciu <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> składników i w `App` składniku ( `App.razor` ):

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

W Blazor WebAssembly aplikacji Dodaj usługi dla opcji i autoryzacji do `Program.Main` :

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

W Blazor Server aplikacji usługi dla opcji i autoryzacji są już obecne, więc nie są wymagane żadne dalsze działania.

## <a name="authorization"></a>Autoryzacja

Po uwierzytelnieniu użytkownika są stosowane reguły *autoryzacji* umożliwiające kontrolę działania użytkownika.

Dostęp jest zazwyczaj udzielany lub odrzucany w zależności od tego, czy:

* Użytkownik jest uwierzytelniany (zalogowany).
* Użytkownik należy do *roli*.
* Użytkownik ma *wierzytelność*.
* *Zasady* są spełnione.

Każda z tych koncepcji jest taka sama jak w aplikacji ASP.NET Core MVC lub Razor Pages. Więcej informacji o zabezpieczeniach ASP.NET Core można znaleźć w artykułach w obszarze [ASP.NET Core Identity Security i ](xref:security/index).

## <a name="authorizeview-component"></a>Składnik AuthorizeView

<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Składnik selektywnie wyświetla zawartość interfejsu użytkownika w zależności od tego, czy użytkownik jest autoryzowany. Takie podejście jest przydatne, gdy wystarczy *wyświetlić* dane dla użytkownika i nie trzeba używać tożsamości użytkownika w logice proceduralnej.

Składnik uwidacznia `context` zmienną typu <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> , za pomocą której można uzyskać dostęp do informacji o zalogowanym użytkowniku:

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Możesz również podać inną zawartość do wyświetlenia, jeśli użytkownik nie ma uprawnień:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authorized.</p>
        <button @onclick="SecureMethod">Authorized Only Button</button>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>

@code {
    private void SecureMethod() { ... }
}
```

Zawartość `<Authorized>` i `<NotAuthorized>` tagi mogą zawierać dowolne elementy, takie jak inne składniki interaktywne.

Domyślna procedura obsługi zdarzeń dla autoryzowanego elementu, taka jak `SecureMethod` Metoda dla `<button>` elementu w poprzednim przykładzie, może być wywoływana tylko przez autoryzowanego użytkownika.

Warunki autoryzacji, takie jak role lub zasady kontrolujące opcje interfejsu użytkownika lub dostęp, są omówione w sekcji [autoryzacja](#authorization) .

Jeśli warunki autoryzacji nie są określone, program <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> używa domyślnych zasad i traktuje je:

* Uwierzytelniony (zalogowany) Użytkownicy jako autoryzowany.
* Nieuwierzytelnionych (wylogowanych) użytkowników jako nieautoryzowanych.

<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Składnik może być używany w `NavMenu` składniku ( `Shared/NavMenu.razor` ) do wyświetlania elementu listy ( `<li>...</li>` ) dla [ `NavLink` składnika](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), ale należy zauważyć, że to podejście powoduje jedynie usunięcie elementu listy z renderowanych danych wyjściowych. Nie uniemożliwia użytkownikowi przechodzenia do składnika.

### <a name="role-based-and-policy-based-authorization"></a>Autoryzacja oparta na rolach i zasadach

<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Składnik obsługuje autoryzację opartą *na rolach* lub *zasadach* .

W przypadku autoryzacji opartej na rolach Użyj <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parametru:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Aby uzyskać więcej informacji, zobacz <xref:security/authorization/roles>.

W przypadku autoryzacji opartej na zasadach należy użyć <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parametru:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

Autoryzacja oparta na oświadczeniach jest specjalnym przypadkiem autoryzacji opartej na zasadach. Na przykład można zdefiniować zasady, które wymagają, aby użytkownicy mieli pewne wnioski. Aby uzyskać więcej informacji, zobacz <xref:security/authorization/policies>.

Te interfejsy API mogą być używane w Blazor Server Blazor WebAssembly aplikacji lub aplikacjach.

Jeśli ani <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> nie <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> zostanie określony, program <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> używa domyślnych zasad.

### <a name="content-displayed-during-asynchronous-authentication"></a>Zawartość wyświetlana podczas uwierzytelniania asynchronicznego

Blazor umożliwia określenie stanu uwierzytelniania w sposób *asynchroniczny*. Głównym scenariuszem tego podejścia jest w Blazor WebAssembly aplikacjach, które składają żądanie do zewnętrznego punktu końcowego w celu uwierzytelnienia.

Gdy trwa uwierzytelnianie, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> domyślnie nie jest wyświetlana żadna zawartość. Aby wyświetlić zawartość podczas uwierzytelniania, należy użyć `<Authorizing>` tagu:

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

Takie podejście nie ma zwykle zastosowania do Blazor Server aplikacji. Blazor Server aplikacje znają stan uwierzytelniania zaraz po ustanowieniu stanu. <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> zawartość można podać w Blazor Server <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> składniku aplikacji, ale zawartość nigdy nie jest wyświetlana.

## <a name="authorize-attribute"></a>[Autoryzuj] — atrybut

Ten [ `[Authorize]` atrybut](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) może być używany w Razor składnikach:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Używać tylko [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) dla `@page` składników uzyskanych za pośrednictwem Blazor routera. Autoryzacja jest wykonywana tylko jako aspekt routingu, a *nie* dla składników podrzędnych renderowanych na stronie. Aby autoryzować wyświetlanie określonych części na stronie, użyj <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> zamiast tego.

Ten [ `[Authorize]` atrybut](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) obsługuje również autoryzację opartą na rolach lub zasadach. W przypadku autoryzacji opartej na rolach Użyj <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parametru:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

W przypadku autoryzacji opartej na zasadach należy użyć <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parametru:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Jeśli ani <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nie <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> zostanie określony, program [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) używa domyślnych zasad, które domyślnie są traktowane:

* Uwierzytelniony (zalogowany) Użytkownicy jako autoryzowany.
* Nieuwierzytelnionych (wylogowanych) użytkowników jako nieautoryzowanych.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Dostosowywanie nieautoryzowanej zawartości za pomocą składnika routera

<xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik, w połączeniu z <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> składnikiem, umożliwia aplikacji określenie zawartości niestandardowej, jeśli:

* Użytkownik nie może [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) wykonać warunku zastosowanego do składnika. [`<NotAuthorized>`](xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView.NotAuthorized?displayProperty=nameWithType)Zostanie wyświetlona Adiustacja elementu. Ten [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut jest pokryty w sekcji [ `[Authorize]` atrybutu](#authorize-attribute) .
* Autoryzacja asynchroniczna jest w toku, co zazwyczaj oznacza, że proces uwierzytelniania użytkownika jest w toku. [`<Authorizing>`](xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView.Authorizing?displayProperty=nameWithType)Zostanie wyświetlona Adiustacja elementu.
* Nie znaleziono zawartości. [`<NotFound>`](xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound?displayProperty=nameWithType)Zostanie wyświetlona Adiustacja elementu.

W domyślnym Blazor Server szablonie projektu `App` składnik ( `App.razor` ) demonstruje sposób ustawiania zawartości niestandardowej:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <h1>Sorry</h1>
                    <p>You're not authorized to reach this page.</p>
                    <p>You may need to log in as a different user.</p>
                </NotAuthorized>
                <Authorizing>
                    <h1>Authorization in progress</h1>
                    <p>Only visible while authorization is in progress.</p>
                </Authorizing>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Zawartość `<NotFound>` , `<NotAuthorized>` , i `<Authorizing>` tagi mogą zawierać dowolne elementy, takie jak inne składniki interaktywne.

Jeśli `<NotAuthorized>` tag nie jest określony, zostanie <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> użyty następujący komunikat rezerwowy:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Powiadomienie o zmianach stanu uwierzytelniania

Jeśli aplikacja określi, że dane stanu uwierzytelniania zostały zmienione (na przykład, ponieważ użytkownik wylogowany lub inny użytkownik zmienił swoje role), [niestandardowe `AuthenticationStateProvider` ](#implement-a-custom-authenticationstateprovider) może opcjonalnie wywołać metodę <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> w <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> klasie bazowej. Spowoduje to powiadomienie klientów o danych stanu uwierzytelniania (na przykład <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> ) w celu ponownego renderowania przy użyciu nowych danych.

## <a name="procedural-logic"></a>Logika proceduralna

Jeśli aplikacja jest wymagana do sprawdzenia reguł autoryzacji jako części logiki proceduralnej, należy użyć kaskadowego parametru typu, `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` Aby uzyskać użytkownika <xref:System.Security.Claims.ClaimsPrincipal> . `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` można łączyć z innymi usługami, takimi jak `IAuthorizationService` , do analizowania zasad.

```razor
@using Microsoft.AspNetCore.Authorization
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
> W Blazor WebAssembly składniku aplikacji Dodaj <xref:Microsoft.AspNetCore.Authorization> <xref:Microsoft.AspNetCore.Components.Authorization> przestrzenie nazw i:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Te przestrzenie nazw mogą być udostępniane globalnie przez dodanie ich do `_Imports.razor` pliku aplikacji.

## <a name="troubleshoot-errors"></a>Rozwiązywanie problemów

Typowe błędy:

* **Autoryzacja wymaga kaskadowego parametru typu `Task\<AuthenticationState>` . Rozważ użycie `CascadingAuthenticationState` , aby to zrobić.**

* **`null` Odebrano wartość dla `authenticationStateTask`**

Prawdopodobnie projekt nie został utworzony przy użyciu Blazor Server szablonu z włączonym uwierzytelnianiem. Zawiń `<CascadingAuthenticationState>` wokół pewnej części drzewa interfejsu użytkownika, na przykład w `App` składniku ( `App.razor` ) w następujący sposób:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState>Dostarcza `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` parametr kaskadowy, który z kolei otrzymuje od podstawowej <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> usługi di.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [Utwórz niestandardową wersję biblioteki MSAL JavaScript.](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [Awesome Blazor :](https://github.com/AdrienTorris/awesome-blazor#authentication) przykładowe linki społeczności uwierzytelniania
