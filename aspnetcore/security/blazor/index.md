---
title: ASP.NET Core Blazor uwierzytelnianie i autoryzacja
author: guardrex
description: Dowiedz Blazor się więcej na temat scenariuszy uwierzytelniania i autoryzacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: d55880265ed1ceedf8f115412e5ac47309521239
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772898"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>ASP.NET Core Blazor uwierzytelnianie i autoryzacja

[Steve Sanderson](https://github.com/SteveSandersonMS) i [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> Aby uzyskać wskazówki w tym artykule Blazor dotyczące zestawu webassembly, wymagany jest szablon Blazor ASP.NET Core webassembly w wersji 3,2 lub nowszej. Jeśli nie używasz programu Visual Studio w wersji 16,6 Preview 2 lub nowszej, Uzyskaj najnowszy Blazor szablon webassembly, postępując zgodnie ze wskazówkami w <xref:blazor/get-started>temacie.

ASP.NET Core obsługuje konfigurację i zarządzanie zabezpieczeniami w Blazor aplikacjach.

Scenariusze zabezpieczeń różnią Blazor się między Blazor aplikacjami serwera i webassembly. Ponieważ Blazor aplikacje serwera są uruchomione na serwerze, sprawdzenia autoryzacji mogą określić:

* Opcje interfejsu użytkownika wyświetlane użytkownikowi (na przykład, które pozycje menu są dostępne dla użytkownika).
* Reguły dostępu do obszarów aplikacji i składników.

BlazorAplikacje webassembly są uruchamiane na kliencie. Autoryzacja jest używana *tylko* do określenia opcji interfejsu użytkownika, które mają być wyświetlane. Ponieważ testy po stronie klienta mogą być modyfikowane lub pomijane przez użytkownika, Blazor aplikacja webassembly nie może wymusić reguł dostępu autoryzacji.

Konwencje autoryzacji stron nie dotyczą składników Razor rutowanych. [ Razor ](xref:security/authorization/razor-pages-authorization) Jeśli na [stronie osadzony](xref:blazor/integrate-components#render-components-from-a-page-or-view)jest składnik nieobsługujący routingu Razor Razor , konwencje autoryzacji stron pośrednio wpływają na składnik wraz z resztą zawartości strony.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601>i <xref:Microsoft.AspNetCore.Identity.UserManager%601> nie są obsługiwane Razor w składnikach.

## <a name="authentication"></a>Uwierzytelnianie

Blazorprogram używa istniejących mechanizmów uwierzytelniania ASP.NET Core do ustanowienia tożsamości użytkownika. Dokładny mechanizm zależy od tego Blazor , jak aplikacja jest hostowana, Blazor webassembly lub Blazor serwer.

### <a name="blazor-webassembly-authentication"></a>BlazorUwierzytelnianie webassembly

W Blazor aplikacjach webassembly sprawdzanie uwierzytelniania można obejść, ponieważ każdy kod po stronie klienta może być modyfikowany przez użytkowników. Jest to samo prawdziwe dla wszystkich technologii aplikacji po stronie klienta, w tym dla struktur SPA skryptów JavaScript lub natywnych aplikacji dla dowolnego systemu operacyjnego.

Dodaj następujące elementy:

* Odwołanie do pakietu dla elementu [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) do pliku projektu aplikacji.
* `Microsoft.AspNetCore.Components.Authorization` Przestrzeń nazw do pliku *_Imports. Razor* aplikacji.

Aby obsłużyć uwierzytelnianie, implementacja wbudowanej lub niestandardowej `AuthenticationStateProvider` usługi została omówiona w poniższych sekcjach.

Aby uzyskać więcej informacji na temat tworzenia aplikacji i konfiguracji <xref:security/blazor/webassembly/index>, zobacz.

### <a name="blazor-server-authentication"></a>BlazorUwierzytelnianie serwera

BlazorAplikacje serwera działają za pośrednictwem połączenia w czasie rzeczywistym, które zostało SignalRutworzone za pomocą programu. [Uwierzytelnianie w SignalRaplikacjach opartych na protokole](xref:signalr/authn-and-authz) jest obsługiwane, gdy połączenie zostanie nawiązane. Uwierzytelnianie może opierać się na pliku cookie lub innym tokenie okaziciela.

Aby uzyskać więcej informacji na temat tworzenia aplikacji i konfiguracji <xref:security/blazor/server/index>, zobacz.

## <a name="authenticationstateprovider-service"></a>Usługa AuthenticationStateProvider

Wbudowana `AuthenticationStateProvider` usługa uzyskuje dane stanu uwierzytelniania z ASP.NET Core `HttpContext.User`. Jest to sposób integracji stanu uwierzytelniania z istniejącymi mechanizmami uwierzytelniania ASP.NET Core.

`AuthenticationStateProvider`to podstawowa usługa używana przez `AuthorizeView` składnik i `CascadingAuthenticationState` składnik do uzyskiwania stanu uwierzytelniania.

Zwykle nie są używane `AuthenticationStateProvider` bezpośrednio. Użyj [AuthorizeView składnika](#authorizeview-component) lub [zadania>\<AuthenticationState zadań](#expose-the-authentication-state-as-a-cascading-parameter) , które opisano w dalszej części tego artykułu. Główną wadą zwrotu z używania `AuthenticationStateProvider` bezpośrednio jest to, że składnik nie jest automatycznie powiadamiany o zmianach danych stanu uwierzytelniania.

`AuthenticationStateProvider` Usługa może udostępniać <xref:System.Security.Claims.ClaimsPrincipal> dane bieżącego użytkownika, jak pokazano w następującym przykładzie:

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

Jeśli `user.Identity.IsAuthenticated` jest `true` i ponieważ użytkownik jest <xref:System.Security.Claims.ClaimsPrincipal>, można wyliczyć oświadczenia i członkostwo w rolach.

Aby uzyskać więcej informacji na temat iniekcji zależności (DI) i <xref:blazor/dependency-injection> usług <xref:fundamentals/dependency-injection>, zobacz i.

## <a name="implement-a-custom-authenticationstateprovider"></a>Implementowanie niestandardowego AuthenticationStateProvider

Jeśli aplikacja wymaga dostawcy niestandardowego, zaimplementuj `AuthenticationStateProvider` i Przesłoń `GetAuthenticationStateAsync`:

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

Blazor W aplikacji webassembly `CustomAuthStateProvider` usługa jest zarejestrowana w `Main` *program.cs*:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

W aplikacji Blazor serwera `CustomAuthStateProvider` usługa jest zarejestrowana w `Startup.ConfigureServices`:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

`CustomAuthStateProvider` W powyższym przykładzie wszyscy użytkownicy są uwierzytelniani przy użyciu nazwy użytkownika `mrfibuli`.

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Uwidacznianie stanu uwierzytelniania jako parametru kaskadowego

Jeśli dane stanu uwierzytelniania są wymagane dla logiki proceduralnej, na przykład podczas wykonywania akcji wyzwalanej przez użytkownika, uzyskaj dane stanu uwierzytelniania przez zdefiniowanie parametru kaskadowego typu `Task<AuthenticationState>`:

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

Jeśli `user.Identity.IsAuthenticated` jest `true`, oświadczenia mogą być wyliczane i członkostwo w rolach oceniane.

`Task<AuthenticationState>` Skonfiguruj parametr kaskadowy za pomocą składników `AuthorizeRouteView` i `CascadingAuthenticationState` w `App` składniku (*App. Razor*):

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

Blazor W aplikacji webassembly Dodaj usługi dla opcji i autoryzacji do `Program.Main`:

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

W aplikacji Blazor serwera usługi dla opcji i autoryzacji są już obecne, więc nie są wymagane żadne dalsze działania.

## <a name="authorization"></a>Autoryzacja

Po uwierzytelnieniu użytkownika są stosowane reguły *autoryzacji* umożliwiające kontrolę działania użytkownika.

Dostęp jest zazwyczaj udzielany lub odrzucany w zależności od tego, czy:

* Użytkownik jest uwierzytelniany (zalogowany).
* Użytkownik należy do *roli*.
* Użytkownik ma *wierzytelność*.
* *Zasady* są spełnione.

Każda z tych koncepcji jest taka sama jak w aplikacji ASP.NET Core MVC lub Razor Pages. Więcej informacji o zabezpieczeniach ASP.NET Core można znaleźć w artykułach w obszarze [ASP.NET Core IdentitySecurity i ](xref:security/index).

## <a name="authorizeview-component"></a>Składnik AuthorizeView

`AuthorizeView` Składnik selektywnie wyświetla interfejs użytkownika w zależności od tego, czy użytkownik jest uprawniony do jego wyświetlania. Takie podejście jest przydatne, gdy wystarczy *wyświetlić* dane dla użytkownika i nie trzeba używać tożsamości użytkownika w logice proceduralnej.

Składnik uwidacznia `context` zmienną typu `AuthenticationState`, za pomocą której można uzyskać dostęp do informacji o zalogowanym użytkowniku:

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Jeśli użytkownik nie jest uwierzytelniony, można również podać inną zawartość do wyświetlenia:

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

`AuthorizeView` `NavMenu` Składnik może być używany w składniku (*Shared/NavMenu. Razor*) do wyświetlania elementu listy (`<li>...</li>`) dla elementu `NavLink`, ale należy zauważyć, że to podejście powoduje jedynie usunięcie elementu listy z renderowanych danych wyjściowych. Nie uniemożliwia użytkownikowi przechodzenia do składnika.

Zawartość `<Authorized>` i `<NotAuthorized>` Tagi mogą zawierać dowolne elementy, takie jak inne składniki interaktywne.

Warunki autoryzacji, takie jak role lub zasady kontrolujące opcje interfejsu użytkownika lub dostęp, są omówione w sekcji [autoryzacja](#authorization) .

Jeśli warunki autoryzacji nie są określone `AuthorizeView` , program używa domyślnych zasad i traktuje je:

* Uwierzytelniony (zalogowany) Użytkownicy jako autoryzowany.
* Nieuwierzytelnionych (wylogowanych) użytkowników jako nieautoryzowanych.

### <a name="role-based-and-policy-based-authorization"></a>Autoryzacja oparta na rolach i zasadach

`AuthorizeView` Składnik obsługuje autoryzację opartą *na rolach* lub *zasadach* .

W przypadku autoryzacji opartej na rolach Użyj `Roles` parametru:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Aby uzyskać więcej informacji, zobacz <xref:security/authorization/roles>.

W przypadku autoryzacji opartej na zasadach należy `Policy` użyć parametru:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

Autoryzacja oparta na oświadczeniach jest specjalnym przypadkiem autoryzacji opartej na zasadach. Na przykład można zdefiniować zasady, które wymagają, aby użytkownicy mieli pewne wnioski. Aby uzyskać więcej informacji, zobacz <xref:security/authorization/policies>.

Te interfejsy API mogą być używane w Blazor aplikacjach serwera Blazor lub webassembly.

`Roles` Jeśli ani nie `Policy` zostanie określony, `AuthorizeView` program używa domyślnych zasad.

### <a name="content-displayed-during-asynchronous-authentication"></a>Zawartość wyświetlana podczas uwierzytelniania asynchronicznego

Blazorumożliwia określenie stanu uwierzytelniania w sposób *asynchroniczny*. Głównym scenariuszem tego podejścia jest Blazor aplikacje webassembly, które składają żądanie do zewnętrznego punktu końcowego w celu uwierzytelnienia.

Gdy trwa uwierzytelnianie, `AuthorizeView` domyślnie nie jest wyświetlana żadna zawartość. Aby wyświetlić zawartość podczas uwierzytelniania, należy użyć `<Authorizing>` elementu:

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

Takie podejście nie dotyczy zazwyczaj aplikacji Blazor serwerowych. BlazorAplikacje serwera znają stan uwierzytelniania zaraz po ustanowieniu stanu. `Authorizing`zawartość można podać w Blazor `AuthorizeView` składniku aplikacji serwera, ale zawartość nigdy nie jest wyświetlana.

## <a name="authorize-attribute"></a>[Autoryzuj] — atrybut

Ten `[Authorize]` atrybut może być używany w Razor składnikach:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Używać `[Authorize]` tylko dla `@page` składników uzyskanych za Blazor pośrednictwem routera. Autoryzacja jest wykonywana tylko jako aspekt routingu, a *nie* dla składników podrzędnych renderowanych na stronie. Aby autoryzować wyświetlanie określonych części na stronie, użyj `AuthorizeView` zamiast tego.

Ten `[Authorize]` atrybut obsługuje również autoryzację opartą na rolach lub zasadach. W przypadku autoryzacji opartej na rolach Użyj `Roles` parametru:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

W przypadku autoryzacji opartej na zasadach należy `Policy` użyć parametru:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

`Roles` Jeśli ani nie `Policy` zostanie określony, `[Authorize]` program używa domyślnych zasad, które domyślnie są traktowane:

* Uwierzytelniony (zalogowany) Użytkownicy jako autoryzowany.
* Nieuwierzytelnionych (wylogowanych) użytkowników jako nieautoryzowanych.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Dostosowywanie nieautoryzowanej zawartości za pomocą składnika routera

`Router` Składnik, w połączeniu z `AuthorizeRouteView` składnikiem, umożliwia aplikacji określenie zawartości niestandardowej, jeśli:

* Nie znaleziono zawartości.
* Użytkownik nie może wykonać `[Authorize]` warunku zastosowanego do składnika. Ten `[Authorize]` atrybut jest pokryty w sekcji [ `[Authorize]` atrybutu](#authorize-attribute) .
* Uwierzytelnianie asynchroniczne jest w toku.

W domyślnym Blazor szablonie projektu serwera `App` składnik (*App. Razor*) demonstruje sposób ustawiania zawartości niestandardowej:

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

Zawartość `<NotFound>`, `<NotAuthorized>`, i `<Authorizing>` Tagi mogą zawierać dowolne elementy, takie jak inne składniki interaktywne.

Jeśli `<NotAuthorized>` element nie jest określony, zostanie `AuthorizeRouteView` użyty następujący komunikat rezerwowy:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Powiadomienie o zmianach stanu uwierzytelniania

Jeśli aplikacja określi, że dane stanu uwierzytelniania zostały zmienione (na przykład, ponieważ użytkownik wylogowany lub inny użytkownik zmienił swoje role), [niestandardowe AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) może opcjonalnie wywołać metodę `NotifyAuthenticationStateChanged` w klasie `AuthenticationStateProvider` bazowej. Spowoduje to powiadomienie klientów o danych stanu uwierzytelniania (na przykład `AuthorizeView`) w celu ponownego renderowania przy użyciu nowych danych.

## <a name="procedural-logic"></a>Logika proceduralna

Jeśli aplikacja jest wymagana do sprawdzenia reguł autoryzacji jako części logiki proceduralnej, należy użyć kaskadowego parametru typu `Task<AuthenticationState>` , aby uzyskać użytkownika. <xref:System.Security.Claims.ClaimsPrincipal> `Task<AuthenticationState>`można łączyć z innymi usługami, takimi jak `IAuthorizationService`, do analizowania zasad.

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
> Blazor W składniku aplikacji webassembly Dodaj przestrzenie `Microsoft.AspNetCore.Authorization` nazw `Microsoft.AspNetCore.Components.Authorization` i:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Te przestrzenie nazw mogą być udostępniane globalnie przez dodanie ich do pliku *_Imports. Razor* aplikacji.

## <a name="authorization-in-blazor-webassembly-apps"></a>Autoryzacja Blazor w aplikacjach webassembly

W Blazor aplikacjach webassembly sprawdzanie autoryzacji można obejść, ponieważ każdy kod po stronie klienta może być modyfikowany przez użytkowników. Jest to samo prawdziwe dla wszystkich technologii aplikacji po stronie klienta, w tym dla struktur SPA skryptów JavaScript lub natywnych aplikacji dla dowolnego systemu operacyjnego.

**Zawsze sprawdzaj autoryzację na serwerze w ramach dowolnych punktów końcowych interfejsu API, do których uzyskuje dostęp aplikacja po stronie klienta.**

Aby uzyskać więcej informacji, zobacz artykuły poniżej <xref:security/blazor/webassembly/index>.

## <a name="troubleshoot-errors"></a>Rozwiązywanie problemów

Typowe błędy:

* **Autoryzacja wymaga parametru kaskadowego typu Task\<AuthenticationState>. Rozważ użycie CascadingAuthenticationState, aby to zrobić.**

* **`null`Odebrano wartość dla`authenticationStateTask`**

Prawdopodobnie projekt nie został utworzony przy użyciu szablonu Blazor serwera z włączonym uwierzytelnianiem. Zawiń `<CascadingAuthenticationState>` wokół pewnej części drzewa interfejsu użytkownika, na przykład w `App` składniku (*App. Razor*) w następujący sposób:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

`CascadingAuthenticationState` Dostarcza parametr `Task<AuthenticationState>` kaskadowy, który z kolei otrzymuje od podstawowej `AuthenticationStateProvider` usługi di.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [Awesome Blazor:](https://github.com/AdrienTorris/awesome-blazor#authentication) przykładowe linki społeczności uwierzytelniania
