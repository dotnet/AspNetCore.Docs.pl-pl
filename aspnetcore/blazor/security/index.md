---
title: 'ASP.NET Core Blazor uwierzytelnianie i autoryzacja'
author: guardrex
description: 'Dowiedz się więcej na temat Blazor scenariuszy uwierzytelniania i autoryzacji.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/security/index
ms.openlocfilehash: 6435a7c9ce2a30873f0d3475a38270d3dea1b300
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595470"
---
# <a name="aspnet-core-no-locblazor-authentication-and-authorization"></a><span data-ttu-id="133a4-103">ASP.NET Core Blazor uwierzytelnianie i autoryzacja</span><span class="sxs-lookup"><span data-stu-id="133a4-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="133a4-104">[Steve Sanderson](https://github.com/SteveSandersonMS) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="133a4-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="133a4-105">ASP.NET Core obsługuje konfigurację i zarządzanie zabezpieczeniami w Blazor aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="133a4-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="133a4-106">Scenariusze zabezpieczeń różnią się w zależności od Blazor Server Blazor WebAssembly aplikacji.</span><span class="sxs-lookup"><span data-stu-id="133a4-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="133a4-107">Ponieważ Blazor Server aplikacje są uruchamiane na serwerze, kontrole autoryzacji mogą określić:</span><span class="sxs-lookup"><span data-stu-id="133a4-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="133a4-108">Opcje interfejsu użytkownika wyświetlane użytkownikowi (na przykład, które pozycje menu są dostępne dla użytkownika).</span><span class="sxs-lookup"><span data-stu-id="133a4-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="133a4-109">Reguły dostępu do obszarów aplikacji i składników.</span><span class="sxs-lookup"><span data-stu-id="133a4-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="133a4-110">Blazor WebAssembly aplikacje są uruchamiane na kliencie.</span><span class="sxs-lookup"><span data-stu-id="133a4-110">Blazor WebAssembly apps run on the client.</span></span> <span data-ttu-id="133a4-111">Autoryzacja jest używana *tylko* do określenia opcji interfejsu użytkownika, które mają być wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="133a4-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="133a4-112">Ponieważ testy po stronie klienta mogą być modyfikowane lub pomijane przez użytkownika, Blazor WebAssembly aplikacja nie może wymusić reguł dostępu autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="133a4-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="133a4-113">[ Razor Konwencje autoryzacji stron](xref:security/authorization/razor-pages-authorization) nie dotyczą składników rutowanych Razor .</span><span class="sxs-lookup"><span data-stu-id="133a4-113">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="133a4-114">Jeśli na Razor [stronie osadzony](xref:blazor/components/prerendering-and-integration)jest składnik nieobsługujący routingu, konwencje autoryzacji stron pośrednio wpływają na Razor składnik wraz z resztą zawartości strony.</span><span class="sxs-lookup"><span data-stu-id="133a4-114">If a non-routable Razor component is [embedded in a page](xref:blazor/components/prerendering-and-integration), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="133a4-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> i <xref:Microsoft.AspNetCore.Identity.UserManager%601> nie są obsługiwane w Razor składnikach.</span><span class="sxs-lookup"><span data-stu-id="133a4-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="133a4-116">Authentication</span><span class="sxs-lookup"><span data-stu-id="133a4-116">Authentication</span></span>

<span data-ttu-id="133a4-117">Blazor program używa istniejących mechanizmów uwierzytelniania ASP.NET Core do ustanowienia tożsamości użytkownika.</span><span class="sxs-lookup"><span data-stu-id="133a4-117">Blazor uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="133a4-118">Dokładny mechanizm zależy od tego, w jaki sposób Blazor aplikacja jest hostowana Blazor WebAssembly Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="133a4-118">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="no-locblazor-webassembly-authentication"></a><span data-ttu-id="133a4-119">Blazor WebAssembly ponowne</span><span class="sxs-lookup"><span data-stu-id="133a4-119">Blazor WebAssembly authentication</span></span>

<span data-ttu-id="133a4-120">W Blazor WebAssembly aplikacjach sprawdzanie uwierzytelniania może być pomijane, ponieważ każdy kod po stronie klienta może być modyfikowany przez użytkowników.</span><span class="sxs-lookup"><span data-stu-id="133a4-120">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="133a4-121">Jest to samo prawdziwe dla wszystkich technologii aplikacji po stronie klienta, w tym dla struktur SPA skryptów JavaScript lub natywnych aplikacji dla dowolnego systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="133a4-121">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="133a4-122">Dodaj następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="133a4-122">Add the following:</span></span>

* <span data-ttu-id="133a4-123">Odwołanie do pakietu dla [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="133a4-123">A package reference for [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) to the app's project file.</span></span>
* <span data-ttu-id="133a4-124">`Microsoft.AspNetCore.Components.Authorization`Przestrzeń nazw do `_Imports.razor` pliku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="133a4-124">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's `_Imports.razor` file.</span></span>

<span data-ttu-id="133a4-125">Aby obsłużyć uwierzytelnianie, użyj wbudowanej lub niestandardowej <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> usługi zawartej w poniższych sekcjach.</span><span class="sxs-lookup"><span data-stu-id="133a4-125">To handle authentication, use of a built-in or custom <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service is covered in the following sections.</span></span>

<span data-ttu-id="133a4-126">Aby uzyskać więcej informacji na temat tworzenia aplikacji i konfiguracji, zobacz <xref:blazor/security/webassembly/index> .</span><span class="sxs-lookup"><span data-stu-id="133a4-126">For more information on creating apps and configuration, see <xref:blazor/security/webassembly/index>.</span></span>

### <a name="no-locblazor-server-authentication"></a><span data-ttu-id="133a4-127">Blazor Server ponowne</span><span class="sxs-lookup"><span data-stu-id="133a4-127">Blazor Server authentication</span></span>

<span data-ttu-id="133a4-128">Blazor Server aplikacje działają za pośrednictwem połączenia w czasie rzeczywistym, które zostało utworzone za pomocą programu SignalR .</span><span class="sxs-lookup"><span data-stu-id="133a4-128">Blazor Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="133a4-129">[Uwierzytelnianie w SignalR aplikacjach opartych na protokole](xref:signalr/authn-and-authz) jest obsługiwane, gdy połączenie zostanie nawiązane.</span><span class="sxs-lookup"><span data-stu-id="133a4-129">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="133a4-130">Uwierzytelnianie może być oparte na cookie lub innym tokenie okaziciela.</span><span class="sxs-lookup"><span data-stu-id="133a4-130">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="133a4-131">Wbudowana <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> Usługa dla Blazor Server aplikacji uzyskuje dane stanu uwierzytelniania z ASP.NET Core `HttpContext.User` .</span><span class="sxs-lookup"><span data-stu-id="133a4-131">The built-in <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service for Blazor Server apps obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="133a4-132">Jest to sposób integracji stanu uwierzytelniania z istniejącymi mechanizmami uwierzytelniania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="133a4-132">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="133a4-133">Aby uzyskać więcej informacji na temat tworzenia aplikacji i konfiguracji, zobacz <xref:blazor/security/server/index> .</span><span class="sxs-lookup"><span data-stu-id="133a4-133">For more information on creating apps and configuration, see <xref:blazor/security/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="133a4-134">Usługa AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="133a4-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="133a4-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> to podstawowa usługa używana przez <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> składnik i <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> składnik do uzyskiwania stanu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="133a4-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> is the underlying service used by the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component to get the authentication state.</span></span>

<span data-ttu-id="133a4-136">Zwykle nie są używane <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="133a4-136">You don't typically use <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly.</span></span> <span data-ttu-id="133a4-137">Użyj [ `AuthorizeView` składnika](#authorizeview-component) lub [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) podejścia opisanego w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="133a4-137">Use the [`AuthorizeView` component](#authorizeview-component) or [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="133a4-138">Główną wadą zwrotu z używania <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> bezpośrednio jest to, że składnik nie jest automatycznie powiadamiany o zmianach danych stanu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="133a4-138">The main drawback to using <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="133a4-139"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>Usługa może udostępniać dane bieżącego użytkownika <xref:System.Security.Claims.ClaimsPrincipal> , jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="133a4-139">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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

<span data-ttu-id="133a4-140">Jeśli `user.Identity.IsAuthenticated` jest `true` i ponieważ użytkownik jest <xref:System.Security.Claims.ClaimsPrincipal> , można wyliczyć oświadczenia i członkostwo w rolach.</span><span class="sxs-lookup"><span data-stu-id="133a4-140">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="133a4-141">Aby uzyskać więcej informacji na temat iniekcji zależności (DI) i usług, zobacz <xref:blazor/fundamentals/dependency-injection> i <xref:fundamentals/dependency-injection> .</span><span class="sxs-lookup"><span data-stu-id="133a4-141">For more information on dependency injection (DI) and services, see <xref:blazor/fundamentals/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="133a4-142">Implementowanie niestandardowego AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="133a4-142">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="133a4-143">Jeśli aplikacja wymaga dostawcy niestandardowego, zaimplementuj <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> i Przesłoń `GetAuthenticationStateAsync` :</span><span class="sxs-lookup"><span data-stu-id="133a4-143">If the app requires a custom provider, implement <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="133a4-144">W Blazor WebAssembly aplikacji `CustomAuthStateProvider` Usługa jest zarejestrowana w `Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="133a4-144">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="133a4-145">W Blazor Server aplikacji `CustomAuthStateProvider` Usługa jest zarejestrowana w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="133a4-145">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="133a4-146">`CustomAuthStateProvider`W powyższym przykładzie wszyscy użytkownicy są uwierzytelniani przy użyciu nazwy użytkownika `mrfibuli` .</span><span class="sxs-lookup"><span data-stu-id="133a4-146">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="133a4-147">Uwidacznianie stanu uwierzytelniania jako parametru kaskadowego</span><span class="sxs-lookup"><span data-stu-id="133a4-147">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="133a4-148">Jeśli dane stanu uwierzytelniania są wymagane dla logiki proceduralnej, na przykład podczas wykonywania akcji wyzwalanej przez użytkownika, uzyskaj dane stanu uwierzytelniania przez zdefiniowanie parametru kaskadowego typu `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` :</span><span class="sxs-lookup"><span data-stu-id="133a4-148">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:</span></span>

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

<span data-ttu-id="133a4-149">Jeśli `user.Identity.IsAuthenticated` jest `true` , oświadczenia mogą być wyliczane i członkostwo w rolach oceniane.</span><span class="sxs-lookup"><span data-stu-id="133a4-149">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="133a4-150">Skonfiguruj `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` parametr kaskadowy przy użyciu <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> składników i w `App` składniku ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="133a4-150">Set up the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter using the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> components in the `App` component (`App.razor`):</span></span>

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

<span data-ttu-id="133a4-151">W Blazor WebAssembly aplikacji Dodaj usługi dla opcji i autoryzacji do `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="133a4-151">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="133a4-152">W Blazor Server aplikacji usługi dla opcji i autoryzacji są już obecne, więc nie są wymagane żadne dalsze działania.</span><span class="sxs-lookup"><span data-stu-id="133a4-152">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="133a4-153">Autoryzacja</span><span class="sxs-lookup"><span data-stu-id="133a4-153">Authorization</span></span>

<span data-ttu-id="133a4-154">Po uwierzytelnieniu użytkownika są stosowane reguły *autoryzacji* umożliwiające kontrolę działania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="133a4-154">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="133a4-155">Dostęp jest zazwyczaj udzielany lub odrzucany w zależności od tego, czy:</span><span class="sxs-lookup"><span data-stu-id="133a4-155">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="133a4-156">Użytkownik jest uwierzytelniany (zalogowany).</span><span class="sxs-lookup"><span data-stu-id="133a4-156">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="133a4-157">Użytkownik należy do *roli*.</span><span class="sxs-lookup"><span data-stu-id="133a4-157">A user is in a *role*.</span></span>
* <span data-ttu-id="133a4-158">Użytkownik ma *wierzytelność*.</span><span class="sxs-lookup"><span data-stu-id="133a4-158">A user has a *claim*.</span></span>
* <span data-ttu-id="133a4-159">*Zasady* są spełnione.</span><span class="sxs-lookup"><span data-stu-id="133a4-159">A *policy* is satisfied.</span></span>

<span data-ttu-id="133a4-160">Każda z tych koncepcji jest taka sama jak w aplikacji ASP.NET Core MVC lub Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="133a4-160">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="133a4-161">Więcej informacji o zabezpieczeniach ASP.NET Core można znaleźć w artykułach w obszarze [ASP.NET Core Identity Security i ](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="133a4-161">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="133a4-162">Składnik AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="133a4-162">AuthorizeView component</span></span>

<span data-ttu-id="133a4-163"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Składnik selektywnie wyświetla zawartość interfejsu użytkownika w zależności od tego, czy użytkownik jest autoryzowany.</span><span class="sxs-lookup"><span data-stu-id="133a4-163">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component selectively displays UI content depending on whether the user is authorized.</span></span> <span data-ttu-id="133a4-164">Takie podejście jest przydatne, gdy wystarczy *wyświetlić* dane dla użytkownika i nie trzeba używać tożsamości użytkownika w logice proceduralnej.</span><span class="sxs-lookup"><span data-stu-id="133a4-164">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="133a4-165">Składnik uwidacznia `context` zmienną typu <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> , za pomocą której można uzyskać dostęp do informacji o zalogowanym użytkowniku:</span><span class="sxs-lookup"><span data-stu-id="133a4-165">The component exposes a `context` variable of type <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="133a4-166">Możesz również podać inną zawartość do wyświetlenia, jeśli użytkownik nie ma uprawnień:</span><span class="sxs-lookup"><span data-stu-id="133a4-166">You can also supply different content for display if the user isn't authorized:</span></span>

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

<span data-ttu-id="133a4-167">Zawartość `<Authorized>` i `<NotAuthorized>` tagi mogą zawierać dowolne elementy, takie jak inne składniki interaktywne.</span><span class="sxs-lookup"><span data-stu-id="133a4-167">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="133a4-168">Domyślna procedura obsługi zdarzeń dla autoryzowanego elementu, taka jak `SecureMethod` Metoda dla `<button>` elementu w poprzednim przykładzie, może być wywoływana tylko przez autoryzowanego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="133a4-168">A default event handler for an authorized element, such as the `SecureMethod` method for the `<button>` element in the preceding example, can only be invoked by an authorized user.</span></span>

<span data-ttu-id="133a4-169">Warunki autoryzacji, takie jak role lub zasady kontrolujące opcje interfejsu użytkownika lub dostęp, są omówione w sekcji [autoryzacja](#authorization) .</span><span class="sxs-lookup"><span data-stu-id="133a4-169">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="133a4-170">Jeśli warunki autoryzacji nie są określone, program <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> używa domyślnych zasad i traktuje je:</span><span class="sxs-lookup"><span data-stu-id="133a4-170">If authorization conditions aren't specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses a default policy and treats:</span></span>

* <span data-ttu-id="133a4-171">Uwierzytelniony (zalogowany) Użytkownicy jako autoryzowany.</span><span class="sxs-lookup"><span data-stu-id="133a4-171">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="133a4-172">Nieuwierzytelnionych (wylogowanych) użytkowników jako nieautoryzowanych.</span><span class="sxs-lookup"><span data-stu-id="133a4-172">Unauthenticated (signed-out) users as unauthorized.</span></span>

<span data-ttu-id="133a4-173"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Składnik może być używany w `NavMenu` składniku ( `Shared/NavMenu.razor` ) do wyświetlania elementu listy ( `<li>...</li>` ) dla [ `NavLink` składnika](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), ale należy zauważyć, że to podejście powoduje jedynie usunięcie elementu listy z renderowanych danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="133a4-173">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component can be used in the `NavMenu` component (`Shared/NavMenu.razor`) to display a list item (`<li>...</li>`) for a [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="133a4-174">Nie uniemożliwia użytkownikowi przechodzenia do składnika.</span><span class="sxs-lookup"><span data-stu-id="133a4-174">It doesn't prevent the user from navigating to the component.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="133a4-175">Autoryzacja oparta na rolach i zasadach</span><span class="sxs-lookup"><span data-stu-id="133a4-175">Role-based and policy-based authorization</span></span>

<span data-ttu-id="133a4-176"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Składnik obsługuje autoryzację opartą *na rolach* lub *zasadach* .</span><span class="sxs-lookup"><span data-stu-id="133a4-176">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="133a4-177">W przypadku autoryzacji opartej na rolach Użyj <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parametru:</span><span class="sxs-lookup"><span data-stu-id="133a4-177">For role-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="133a4-178">Aby uzyskać więcej informacji, zobacz <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="133a4-178">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="133a4-179">W przypadku autoryzacji opartej na zasadach należy użyć <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parametru:</span><span class="sxs-lookup"><span data-stu-id="133a4-179">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="133a4-180">Autoryzacja oparta na oświadczeniach jest specjalnym przypadkiem autoryzacji opartej na zasadach.</span><span class="sxs-lookup"><span data-stu-id="133a4-180">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="133a4-181">Na przykład można zdefiniować zasady, które wymagają, aby użytkownicy mieli pewne wnioski.</span><span class="sxs-lookup"><span data-stu-id="133a4-181">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="133a4-182">Aby uzyskać więcej informacji, zobacz <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="133a4-182">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="133a4-183">Te interfejsy API mogą być używane w Blazor Server Blazor WebAssembly aplikacji lub aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="133a4-183">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="133a4-184">Jeśli ani <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> nie <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> zostanie określony, program <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> używa domyślnych zasad.</span><span class="sxs-lookup"><span data-stu-id="133a4-184">If neither <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> nor <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> is specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="133a4-185">Zawartość wyświetlana podczas uwierzytelniania asynchronicznego</span><span class="sxs-lookup"><span data-stu-id="133a4-185">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="133a4-186">Blazor umożliwia określenie stanu uwierzytelniania w sposób *asynchroniczny*.</span><span class="sxs-lookup"><span data-stu-id="133a4-186">Blazor allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="133a4-187">Głównym scenariuszem tego podejścia jest w Blazor WebAssembly aplikacjach, które składają żądanie do zewnętrznego punktu końcowego w celu uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="133a4-187">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="133a4-188">Gdy trwa uwierzytelnianie, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> domyślnie nie jest wyświetlana żadna zawartość.</span><span class="sxs-lookup"><span data-stu-id="133a4-188">While authentication is in progress, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> displays no content by default.</span></span> <span data-ttu-id="133a4-189">Aby wyświetlić zawartość podczas uwierzytelniania, należy użyć `<Authorizing>` tagu:</span><span class="sxs-lookup"><span data-stu-id="133a4-189">To display content while authentication occurs, use the `<Authorizing>` tag:</span></span>

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

<span data-ttu-id="133a4-190">Takie podejście nie ma zwykle zastosowania do Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="133a4-190">This approach isn't normally applicable to Blazor Server apps.</span></span> <span data-ttu-id="133a4-191">Blazor Server aplikacje znają stan uwierzytelniania zaraz po ustanowieniu stanu.</span><span class="sxs-lookup"><span data-stu-id="133a4-191">Blazor Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="133a4-192"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> zawartość można podać w Blazor Server <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> składniku aplikacji, ale zawartość nigdy nie jest wyświetlana.</span><span class="sxs-lookup"><span data-stu-id="133a4-192"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> content can be provided in a Blazor Server app's <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="133a4-193">[Autoryzuj] — atrybut</span><span class="sxs-lookup"><span data-stu-id="133a4-193">[Authorize] attribute</span></span>

<span data-ttu-id="133a4-194">Ten [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut może być używany w Razor składnikach:</span><span class="sxs-lookup"><span data-stu-id="133a4-194">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="133a4-195">Używać tylko [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) dla `@page` składników uzyskanych za pośrednictwem Blazor routera.</span><span class="sxs-lookup"><span data-stu-id="133a4-195">Only use [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="133a4-196">Autoryzacja jest wykonywana tylko jako aspekt routingu, a *nie* dla składników podrzędnych renderowanych na stronie.</span><span class="sxs-lookup"><span data-stu-id="133a4-196">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="133a4-197">Aby autoryzować wyświetlanie określonych części na stronie, użyj <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> zamiast tego.</span><span class="sxs-lookup"><span data-stu-id="133a4-197">To authorize the display of specific parts within a page, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> instead.</span></span>

<span data-ttu-id="133a4-198">Ten [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut obsługuje również autoryzację opartą na rolach lub zasadach.</span><span class="sxs-lookup"><span data-stu-id="133a4-198">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="133a4-199">W przypadku autoryzacji opartej na rolach Użyj <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parametru:</span><span class="sxs-lookup"><span data-stu-id="133a4-199">For role-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="133a4-200">W przypadku autoryzacji opartej na zasadach należy użyć <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parametru:</span><span class="sxs-lookup"><span data-stu-id="133a4-200">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="133a4-201">Jeśli ani <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nie <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> zostanie określony, program [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) używa domyślnych zasad, które domyślnie są traktowane:</span><span class="sxs-lookup"><span data-stu-id="133a4-201">If neither <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nor <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> is specified, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="133a4-202">Uwierzytelniony (zalogowany) Użytkownicy jako autoryzowany.</span><span class="sxs-lookup"><span data-stu-id="133a4-202">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="133a4-203">Nieuwierzytelnionych (wylogowanych) użytkowników jako nieautoryzowanych.</span><span class="sxs-lookup"><span data-stu-id="133a4-203">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="133a4-204">Dostosowywanie nieautoryzowanej zawartości za pomocą składnika routera</span><span class="sxs-lookup"><span data-stu-id="133a4-204">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="133a4-205"><xref:Microsoft.AspNetCore.Components.Routing.Router>Składnik, w połączeniu z <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> składnikiem, umożliwia aplikacji określenie zawartości niestandardowej, jeśli:</span><span class="sxs-lookup"><span data-stu-id="133a4-205">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component, in conjunction with the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="133a4-206">Nie znaleziono zawartości.</span><span class="sxs-lookup"><span data-stu-id="133a4-206">Content isn't found.</span></span>
* <span data-ttu-id="133a4-207">Użytkownik nie może [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) wykonać warunku zastosowanego do składnika.</span><span class="sxs-lookup"><span data-stu-id="133a4-207">The user fails an [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) condition applied to the component.</span></span> <span data-ttu-id="133a4-208">Ten [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut jest pokryty w sekcji [ `[Authorize]` atrybutu](#authorize-attribute) .</span><span class="sxs-lookup"><span data-stu-id="133a4-208">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="133a4-209">Uwierzytelnianie asynchroniczne jest w toku.</span><span class="sxs-lookup"><span data-stu-id="133a4-209">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="133a4-210">W domyślnym Blazor Server szablonie projektu `App` składnik ( `App.razor` ) demonstruje sposób ustawiania zawartości niestandardowej:</span><span class="sxs-lookup"><span data-stu-id="133a4-210">In the default Blazor Server project template, the `App` component (`App.razor`) demonstrates how to set custom content:</span></span>

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
                    <h1>Authentication in progress</h1>
                    <p>Only visible while authentication is in progress.</p>
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

<span data-ttu-id="133a4-211">Zawartość `<NotFound>` , `<NotAuthorized>` , i `<Authorizing>` tagi mogą zawierać dowolne elementy, takie jak inne składniki interaktywne.</span><span class="sxs-lookup"><span data-stu-id="133a4-211">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="133a4-212">Jeśli `<NotAuthorized>` tag nie jest określony, zostanie <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> użyty następujący komunikat rezerwowy:</span><span class="sxs-lookup"><span data-stu-id="133a4-212">If the `<NotAuthorized>` tag isn't specified, the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="133a4-213">Powiadomienie o zmianach stanu uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="133a4-213">Notification about authentication state changes</span></span>

<span data-ttu-id="133a4-214">Jeśli aplikacja określi, że dane stanu uwierzytelniania zostały zmienione (na przykład, ponieważ użytkownik wylogowany lub inny użytkownik zmienił swoje role), [niestandardowe `AuthenticationStateProvider` ](#implement-a-custom-authenticationstateprovider) może opcjonalnie wywołać metodę <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> w <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> klasie bazowej.</span><span class="sxs-lookup"><span data-stu-id="133a4-214">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) can optionally invoke the method <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> on the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> base class.</span></span> <span data-ttu-id="133a4-215">Spowoduje to powiadomienie klientów o danych stanu uwierzytelniania (na przykład <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> ) w celu ponownego renderowania przy użyciu nowych danych.</span><span class="sxs-lookup"><span data-stu-id="133a4-215">This notifies consumers of the authentication state data (for example, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="133a4-216">Logika proceduralna</span><span class="sxs-lookup"><span data-stu-id="133a4-216">Procedural logic</span></span>

<span data-ttu-id="133a4-217">Jeśli aplikacja jest wymagana do sprawdzenia reguł autoryzacji jako części logiki proceduralnej, należy użyć kaskadowego parametru typu, `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` Aby uzyskać użytkownika <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="133a4-217">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="133a4-218">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` można łączyć z innymi usługami, takimi jak `IAuthorizationService` , do analizowania zasad.</span><span class="sxs-lookup"><span data-stu-id="133a4-218">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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
> <span data-ttu-id="133a4-219">W Blazor WebAssembly składniku aplikacji Dodaj <xref:Microsoft.AspNetCore.Authorization> <xref:Microsoft.AspNetCore.Components.Authorization> przestrzenie nazw i:</span><span class="sxs-lookup"><span data-stu-id="133a4-219">In a Blazor WebAssembly app component, add the <xref:Microsoft.AspNetCore.Authorization> and <xref:Microsoft.AspNetCore.Components.Authorization> namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="133a4-220">Te przestrzenie nazw mogą być udostępniane globalnie przez dodanie ich do `_Imports.razor` pliku aplikacji.</span><span class="sxs-lookup"><span data-stu-id="133a4-220">These namespaces can be provided globally by adding them to the app's `_Imports.razor` file.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="133a4-221">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="133a4-221">Troubleshoot errors</span></span>

<span data-ttu-id="133a4-222">Typowe błędy:</span><span class="sxs-lookup"><span data-stu-id="133a4-222">Common errors:</span></span>

* <span data-ttu-id="133a4-223">**Autoryzacja wymaga kaskadowego parametru typu `Task\<AuthenticationState>` . Rozważ użycie `CascadingAuthenticationState` , aby to zrobić.**</span><span class="sxs-lookup"><span data-stu-id="133a4-223">**Authorization requires a cascading parameter of type `Task\<AuthenticationState>`. Consider using `CascadingAuthenticationState` to supply this.**</span></span>

* <span data-ttu-id="133a4-224">**`null` Odebrano wartość dla `authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="133a4-224">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="133a4-225">Prawdopodobnie projekt nie został utworzony przy użyciu Blazor Server szablonu z włączonym uwierzytelnianiem.</span><span class="sxs-lookup"><span data-stu-id="133a4-225">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="133a4-226">Zawiń `<CascadingAuthenticationState>` wokół pewnej części drzewa interfejsu użytkownika, na przykład w `App` składniku ( `App.razor` ) w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="133a4-226">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (`App.razor`) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="133a4-227"><xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState>Dostarcza `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` parametr kaskadowy, który z kolei otrzymuje od podstawowej <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> usługi di.</span><span class="sxs-lookup"><span data-stu-id="133a4-227">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> supplies the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter, which in turn it receives from the underlying <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="133a4-228">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="133a4-228">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="133a4-229">[Awesome Blazor :](https://github.com/AdrienTorris/awesome-blazor#authentication) przykładowe linki społeczności uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="133a4-229">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
