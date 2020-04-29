---
title: ASP.NET Core Blazor uwierzytelnianie i autoryzacja
author: guardrex
description: Dowiedz Blazor się więcej na temat scenariuszy uwierzytelniania i autoryzacji.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: ced8e90147b08bc75aec4534fdd8d8552506f88c
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206102"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="52f7a-103">ASP.NET Core Blazor uwierzytelnianie i autoryzacja</span><span class="sxs-lookup"><span data-stu-id="52f7a-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="52f7a-104">[Steve Sanderson](https://github.com/SteveSandersonMS) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="52f7a-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> <span data-ttu-id="52f7a-105">Aby uzyskać wskazówki w tym artykule Blazor dotyczące zestawu webassembly, wymagany jest szablon Blazor ASP.NET Core webassembly w wersji 3,2 lub nowszej.</span><span class="sxs-lookup"><span data-stu-id="52f7a-105">For the guidance in this article that applies to Blazor WebAssembly, the ASP.NET Core Blazor WebAssembly template version 3.2 or later is required.</span></span> <span data-ttu-id="52f7a-106">Jeśli nie używasz programu Visual Studio w wersji 16,6 Preview 2 lub nowszej, Uzyskaj najnowszy Blazor szablon webassembly, postępując zgodnie ze wskazówkami w <xref:blazor/get-started>temacie.</span><span class="sxs-lookup"><span data-stu-id="52f7a-106">If you aren't using Visual Studio version 16.6 Preview 2 or later, obtain the latest Blazor WebAssembly template by following the guidance in <xref:blazor/get-started>.</span></span>

<span data-ttu-id="52f7a-107">ASP.NET Core obsługuje konfigurację i zarządzanie zabezpieczeniami w Blazor aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="52f7a-107">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="52f7a-108">Scenariusze zabezpieczeń różnią Blazor się między Blazor aplikacjami serwera i webassembly.</span><span class="sxs-lookup"><span data-stu-id="52f7a-108">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="52f7a-109">Ponieważ Blazor aplikacje serwera są uruchomione na serwerze, sprawdzenia autoryzacji mogą określić:</span><span class="sxs-lookup"><span data-stu-id="52f7a-109">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="52f7a-110">Opcje interfejsu użytkownika wyświetlane użytkownikowi (na przykład, które pozycje menu są dostępne dla użytkownika).</span><span class="sxs-lookup"><span data-stu-id="52f7a-110">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="52f7a-111">Reguły dostępu do obszarów aplikacji i składników.</span><span class="sxs-lookup"><span data-stu-id="52f7a-111">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="52f7a-112">Aplikacje webassembly są uruchamiane na kliencie.</span><span class="sxs-lookup"><span data-stu-id="52f7a-112"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="52f7a-113">Autoryzacja jest używana *tylko* do określenia opcji interfejsu użytkownika, które mają być wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="52f7a-113">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="52f7a-114">Ponieważ testy po stronie klienta mogą być modyfikowane lub pomijane przez użytkownika, Blazor aplikacja webassembly nie może wymusić reguł dostępu autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="52f7a-114">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="52f7a-115">[Razor Pages Konwencji autoryzacji](xref:security/authorization/razor-pages-authorization) nie mają zastosowania do routingu składników Razor.</span><span class="sxs-lookup"><span data-stu-id="52f7a-115">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="52f7a-116">Jeśli składnik Razor nieobsługujący routingu jest [osadzony na stronie](xref:blazor/integrate-components#render-components-from-a-page-or-view), konwencje autoryzacji strony mają pośredni wpływ na składnik Razor wraz z resztą zawartości strony.</span><span class="sxs-lookup"><span data-stu-id="52f7a-116">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="52f7a-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601>i <xref:Microsoft.AspNetCore.Identity.UserManager%601> nie są obsługiwane w składnikach Razor.</span><span class="sxs-lookup"><span data-stu-id="52f7a-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="52f7a-118">Authentication</span><span class="sxs-lookup"><span data-stu-id="52f7a-118">Authentication</span></span>

Blazor<span data-ttu-id="52f7a-119">program używa istniejących mechanizmów uwierzytelniania ASP.NET Core do ustanowienia tożsamości użytkownika.</span><span class="sxs-lookup"><span data-stu-id="52f7a-119"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="52f7a-120">Dokładny mechanizm zależy od tego Blazor , jak aplikacja jest hostowana, Blazor webassembly lub Blazor serwer.</span><span class="sxs-lookup"><span data-stu-id="52f7a-120">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a>Blazor<span data-ttu-id="52f7a-121">Uwierzytelnianie webassembly</span><span class="sxs-lookup"><span data-stu-id="52f7a-121"> WebAssembly authentication</span></span>

<span data-ttu-id="52f7a-122">W Blazor aplikacjach webassembly sprawdzanie uwierzytelniania można obejść, ponieważ każdy kod po stronie klienta może być modyfikowany przez użytkowników.</span><span class="sxs-lookup"><span data-stu-id="52f7a-122">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="52f7a-123">Jest to samo prawdziwe dla wszystkich technologii aplikacji po stronie klienta, w tym dla struktur SPA skryptów JavaScript lub natywnych aplikacji dla dowolnego systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="52f7a-123">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="52f7a-124">Dodaj następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="52f7a-124">Add the following:</span></span>

* <span data-ttu-id="52f7a-125">Odwołanie do pakietu dla elementu [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) do pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="52f7a-125">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="52f7a-126">`Microsoft.AspNetCore.Components.Authorization` Przestrzeń nazw do pliku *_Imports. Razor* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="52f7a-126">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="52f7a-127">Aby obsłużyć uwierzytelnianie, implementacja wbudowanej lub niestandardowej `AuthenticationStateProvider` usługi została omówiona w poniższych sekcjach.</span><span class="sxs-lookup"><span data-stu-id="52f7a-127">To handle authentication, implementation of a built-in or custom `AuthenticationStateProvider` service is covered in the following sections.</span></span>

<span data-ttu-id="52f7a-128">Aby uzyskać więcej informacji na temat tworzenia aplikacji i konfiguracji <xref:security/blazor/webassembly/index>, zobacz.</span><span class="sxs-lookup"><span data-stu-id="52f7a-128">For more information on creating apps and configuration, see <xref:security/blazor/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a>Blazor<span data-ttu-id="52f7a-129">Uwierzytelnianie serwera</span><span class="sxs-lookup"><span data-stu-id="52f7a-129"> Server authentication</span></span>

Blazor<span data-ttu-id="52f7a-130">Aplikacje serwera działają za pośrednictwem połączenia w czasie rzeczywistym, które zostało SignalRutworzone za pomocą programu.</span><span class="sxs-lookup"><span data-stu-id="52f7a-130"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="52f7a-131">[Uwierzytelnianie w SignalRaplikacjach opartych na protokole](xref:signalr/authn-and-authz) jest obsługiwane, gdy połączenie zostanie nawiązane.</span><span class="sxs-lookup"><span data-stu-id="52f7a-131">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="52f7a-132">Uwierzytelnianie może opierać się na pliku cookie lub innym tokenie okaziciela.</span><span class="sxs-lookup"><span data-stu-id="52f7a-132">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="52f7a-133">Aby uzyskać więcej informacji na temat tworzenia aplikacji i konfiguracji <xref:security/blazor/server/index>, zobacz.</span><span class="sxs-lookup"><span data-stu-id="52f7a-133">For more information on creating apps and configuration, see <xref:security/blazor/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="52f7a-134">Usługa AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="52f7a-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="52f7a-135">Wbudowana `AuthenticationStateProvider` usługa uzyskuje dane stanu uwierzytelniania z ASP.NET Core `HttpContext.User`.</span><span class="sxs-lookup"><span data-stu-id="52f7a-135">The built-in `AuthenticationStateProvider` service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="52f7a-136">Jest to sposób integracji stanu uwierzytelniania z istniejącymi mechanizmami uwierzytelniania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="52f7a-136">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="52f7a-137">`AuthenticationStateProvider`to podstawowa usługa używana przez `AuthorizeView` składnik i `CascadingAuthenticationState` składnik do uzyskiwania stanu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="52f7a-137">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="52f7a-138">Zwykle nie są używane `AuthenticationStateProvider` bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="52f7a-138">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="52f7a-139">Użyj [AuthorizeView składnika](#authorizeview-component) lub [zadania>\<AuthenticationState zadań](#expose-the-authentication-state-as-a-cascading-parameter) , które opisano w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="52f7a-139">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="52f7a-140">Główną wadą zwrotu z używania `AuthenticationStateProvider` bezpośrednio jest to, że składnik nie jest automatycznie powiadamiany o zmianach danych stanu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="52f7a-140">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="52f7a-141">`AuthenticationStateProvider` Usługa może udostępniać <xref:System.Security.Claims.ClaimsPrincipal> dane bieżącego użytkownika, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="52f7a-141">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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

<span data-ttu-id="52f7a-142">Jeśli `user.Identity.IsAuthenticated` jest `true` i ponieważ użytkownik jest <xref:System.Security.Claims.ClaimsPrincipal>, można wyliczyć oświadczenia i członkostwo w rolach.</span><span class="sxs-lookup"><span data-stu-id="52f7a-142">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="52f7a-143">Aby uzyskać więcej informacji na temat iniekcji zależności (DI) i <xref:blazor/dependency-injection> usług <xref:fundamentals/dependency-injection>, zobacz i.</span><span class="sxs-lookup"><span data-stu-id="52f7a-143">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="52f7a-144">Implementowanie niestandardowego AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="52f7a-144">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="52f7a-145">Jeśli aplikacja wymaga dostawcy niestandardowego, zaimplementuj `AuthenticationStateProvider` i Przesłoń `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="52f7a-145">If the app requires a custom provider, implement `AuthenticationStateProvider` and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="52f7a-146">Blazor W aplikacji webassembly `CustomAuthStateProvider` usługa jest zarejestrowana w `Main` *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="52f7a-146">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="52f7a-147">W aplikacji Blazor serwera `CustomAuthStateProvider` usługa jest zarejestrowana w `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="52f7a-147">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="52f7a-148">`CustomAuthStateProvider` W powyższym przykładzie wszyscy użytkownicy są uwierzytelniani przy użyciu nazwy użytkownika `mrfibuli`.</span><span class="sxs-lookup"><span data-stu-id="52f7a-148">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="52f7a-149">Uwidacznianie stanu uwierzytelniania jako parametru kaskadowego</span><span class="sxs-lookup"><span data-stu-id="52f7a-149">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="52f7a-150">Jeśli dane stanu uwierzytelniania są wymagane dla logiki proceduralnej, na przykład podczas wykonywania akcji wyzwalanej przez użytkownika, uzyskaj dane stanu uwierzytelniania przez zdefiniowanie parametru kaskadowego typu `Task<AuthenticationState>`:</span><span class="sxs-lookup"><span data-stu-id="52f7a-150">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

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

<span data-ttu-id="52f7a-151">Jeśli `user.Identity.IsAuthenticated` jest `true`, oświadczenia mogą być wyliczane i członkostwo w rolach oceniane.</span><span class="sxs-lookup"><span data-stu-id="52f7a-151">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="52f7a-152">`Task<AuthenticationState>` Skonfiguruj parametr kaskadowy za pomocą składników `AuthorizeRouteView` i `CascadingAuthenticationState` w `App` składniku (*App. Razor*):</span><span class="sxs-lookup"><span data-stu-id="52f7a-152">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the `App` component (*App.razor*):</span></span>

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

<span data-ttu-id="52f7a-153">Blazor W aplikacji webassembly Dodaj usługi dla opcji i autoryzacji do `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="52f7a-153">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="52f7a-154">W aplikacji Blazor serwera usługi dla opcji i autoryzacji są już obecne, więc nie są wymagane żadne dalsze działania.</span><span class="sxs-lookup"><span data-stu-id="52f7a-154">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="52f7a-155">Autoryzacja</span><span class="sxs-lookup"><span data-stu-id="52f7a-155">Authorization</span></span>

<span data-ttu-id="52f7a-156">Po uwierzytelnieniu użytkownika są stosowane reguły *autoryzacji* umożliwiające kontrolę działania użytkownika.</span><span class="sxs-lookup"><span data-stu-id="52f7a-156">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="52f7a-157">Dostęp jest zazwyczaj udzielany lub odrzucany w zależności od tego, czy:</span><span class="sxs-lookup"><span data-stu-id="52f7a-157">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="52f7a-158">Użytkownik jest uwierzytelniany (zalogowany).</span><span class="sxs-lookup"><span data-stu-id="52f7a-158">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="52f7a-159">Użytkownik należy do *roli*.</span><span class="sxs-lookup"><span data-stu-id="52f7a-159">A user is in a *role*.</span></span>
* <span data-ttu-id="52f7a-160">Użytkownik ma *wierzytelność*.</span><span class="sxs-lookup"><span data-stu-id="52f7a-160">A user has a *claim*.</span></span>
* <span data-ttu-id="52f7a-161">*Zasady* są spełnione.</span><span class="sxs-lookup"><span data-stu-id="52f7a-161">A *policy* is satisfied.</span></span>

<span data-ttu-id="52f7a-162">Każda z tych koncepcji jest taka sama jak w aplikacji ASP.NET Core MVC lub Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="52f7a-162">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="52f7a-163">Aby uzyskać więcej informacji na temat zabezpieczeń ASP.NET Core, zapoznaj się z artykułami w obszarze [ASP.NET Core zabezpieczenia i tożsamość](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="52f7a-163">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="52f7a-164">Składnik AuthorizeView</span><span class="sxs-lookup"><span data-stu-id="52f7a-164">AuthorizeView component</span></span>

<span data-ttu-id="52f7a-165">`AuthorizeView` Składnik selektywnie wyświetla interfejs użytkownika w zależności od tego, czy użytkownik jest uprawniony do jego wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="52f7a-165">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="52f7a-166">Takie podejście jest przydatne, gdy wystarczy *wyświetlić* dane dla użytkownika i nie trzeba używać tożsamości użytkownika w logice proceduralnej.</span><span class="sxs-lookup"><span data-stu-id="52f7a-166">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="52f7a-167">Składnik uwidacznia `context` zmienną typu `AuthenticationState`, za pomocą której można uzyskać dostęp do informacji o zalogowanym użytkowniku:</span><span class="sxs-lookup"><span data-stu-id="52f7a-167">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="52f7a-168">Jeśli użytkownik nie jest uwierzytelniony, można również podać inną zawartość do wyświetlenia:</span><span class="sxs-lookup"><span data-stu-id="52f7a-168">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="52f7a-169">`AuthorizeView` `NavMenu` Składnik może być używany w składniku (*Shared/NavMenu. Razor*) do wyświetlania elementu listy (`<li>...</li>`) dla elementu `NavLink`, ale należy zauważyć, że to podejście powoduje jedynie usunięcie elementu listy z renderowanych danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="52f7a-169">The `AuthorizeView` component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a `NavLink`, but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="52f7a-170">Nie uniemożliwia użytkownikowi przechodzenia do składnika.</span><span class="sxs-lookup"><span data-stu-id="52f7a-170">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="52f7a-171">Zawartość `<Authorized>` i `<NotAuthorized>` Tagi mogą zawierać dowolne elementy, takie jak inne składniki interaktywne.</span><span class="sxs-lookup"><span data-stu-id="52f7a-171">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="52f7a-172">Warunki autoryzacji, takie jak role lub zasady kontrolujące opcje interfejsu użytkownika lub dostęp, są omówione w sekcji [autoryzacja](#authorization) .</span><span class="sxs-lookup"><span data-stu-id="52f7a-172">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="52f7a-173">Jeśli warunki autoryzacji nie są określone `AuthorizeView` , program używa domyślnych zasad i traktuje je:</span><span class="sxs-lookup"><span data-stu-id="52f7a-173">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="52f7a-174">Uwierzytelniony (zalogowany) Użytkownicy jako autoryzowany.</span><span class="sxs-lookup"><span data-stu-id="52f7a-174">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="52f7a-175">Nieuwierzytelnionych (wylogowanych) użytkowników jako nieautoryzowanych.</span><span class="sxs-lookup"><span data-stu-id="52f7a-175">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="52f7a-176">Autoryzacja oparta na rolach i zasadach</span><span class="sxs-lookup"><span data-stu-id="52f7a-176">Role-based and policy-based authorization</span></span>

<span data-ttu-id="52f7a-177">`AuthorizeView` Składnik obsługuje autoryzację opartą *na rolach* lub *zasadach* .</span><span class="sxs-lookup"><span data-stu-id="52f7a-177">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="52f7a-178">W przypadku autoryzacji opartej na rolach Użyj `Roles` parametru:</span><span class="sxs-lookup"><span data-stu-id="52f7a-178">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="52f7a-179">Aby uzyskać więcej informacji, zobacz <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="52f7a-179">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="52f7a-180">W przypadku autoryzacji opartej na zasadach należy `Policy` użyć parametru:</span><span class="sxs-lookup"><span data-stu-id="52f7a-180">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="52f7a-181">Autoryzacja oparta na oświadczeniach jest specjalnym przypadkiem autoryzacji opartej na zasadach.</span><span class="sxs-lookup"><span data-stu-id="52f7a-181">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="52f7a-182">Na przykład można zdefiniować zasady, które wymagają, aby użytkownicy mieli pewne wnioski.</span><span class="sxs-lookup"><span data-stu-id="52f7a-182">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="52f7a-183">Aby uzyskać więcej informacji, zobacz <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="52f7a-183">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="52f7a-184">Te interfejsy API mogą być używane w Blazor aplikacjach serwera Blazor lub webassembly.</span><span class="sxs-lookup"><span data-stu-id="52f7a-184">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="52f7a-185">`Roles` Jeśli ani nie `Policy` zostanie określony, `AuthorizeView` program używa domyślnych zasad.</span><span class="sxs-lookup"><span data-stu-id="52f7a-185">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="52f7a-186">Zawartość wyświetlana podczas uwierzytelniania asynchronicznego</span><span class="sxs-lookup"><span data-stu-id="52f7a-186">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="52f7a-187">umożliwia określenie stanu uwierzytelniania w sposób *asynchroniczny*.</span><span class="sxs-lookup"><span data-stu-id="52f7a-187"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="52f7a-188">Głównym scenariuszem tego podejścia jest Blazor aplikacje webassembly, które składają żądanie do zewnętrznego punktu końcowego w celu uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="52f7a-188">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="52f7a-189">Gdy trwa uwierzytelnianie, `AuthorizeView` domyślnie nie jest wyświetlana żadna zawartość.</span><span class="sxs-lookup"><span data-stu-id="52f7a-189">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="52f7a-190">Aby wyświetlić zawartość podczas uwierzytelniania, należy użyć `<Authorizing>` elementu:</span><span class="sxs-lookup"><span data-stu-id="52f7a-190">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="52f7a-191">Takie podejście nie dotyczy zazwyczaj aplikacji Blazor serwerowych.</span><span class="sxs-lookup"><span data-stu-id="52f7a-191">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="52f7a-192">Aplikacje serwera znają stan uwierzytelniania zaraz po ustanowieniu stanu.</span><span class="sxs-lookup"><span data-stu-id="52f7a-192"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="52f7a-193">`Authorizing`zawartość można podać w Blazor `AuthorizeView` składniku aplikacji serwera, ale zawartość nigdy nie jest wyświetlana.</span><span class="sxs-lookup"><span data-stu-id="52f7a-193">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="52f7a-194">[Autoryzuj] — atrybut</span><span class="sxs-lookup"><span data-stu-id="52f7a-194">[Authorize] attribute</span></span>

<span data-ttu-id="52f7a-195">Ten `[Authorize]` atrybut może być używany w składnikach Razor:</span><span class="sxs-lookup"><span data-stu-id="52f7a-195">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="52f7a-196">Używać `[Authorize]` tylko dla `@page` składników uzyskanych za Blazor pośrednictwem routera.</span><span class="sxs-lookup"><span data-stu-id="52f7a-196">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="52f7a-197">Autoryzacja jest wykonywana tylko jako aspekt routingu, a *nie* dla składników podrzędnych renderowanych na stronie.</span><span class="sxs-lookup"><span data-stu-id="52f7a-197">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="52f7a-198">Aby autoryzować wyświetlanie określonych części na stronie, użyj `AuthorizeView` zamiast tego.</span><span class="sxs-lookup"><span data-stu-id="52f7a-198">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="52f7a-199">Ten `[Authorize]` atrybut obsługuje również autoryzację opartą na rolach lub zasadach.</span><span class="sxs-lookup"><span data-stu-id="52f7a-199">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="52f7a-200">W przypadku autoryzacji opartej na rolach Użyj `Roles` parametru:</span><span class="sxs-lookup"><span data-stu-id="52f7a-200">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="52f7a-201">W przypadku autoryzacji opartej na zasadach należy `Policy` użyć parametru:</span><span class="sxs-lookup"><span data-stu-id="52f7a-201">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="52f7a-202">`Roles` Jeśli ani nie `Policy` zostanie określony, `[Authorize]` program używa domyślnych zasad, które domyślnie są traktowane:</span><span class="sxs-lookup"><span data-stu-id="52f7a-202">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="52f7a-203">Uwierzytelniony (zalogowany) Użytkownicy jako autoryzowany.</span><span class="sxs-lookup"><span data-stu-id="52f7a-203">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="52f7a-204">Nieuwierzytelnionych (wylogowanych) użytkowników jako nieautoryzowanych.</span><span class="sxs-lookup"><span data-stu-id="52f7a-204">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="52f7a-205">Dostosowywanie nieautoryzowanej zawartości za pomocą składnika routera</span><span class="sxs-lookup"><span data-stu-id="52f7a-205">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="52f7a-206">`Router` Składnik, w połączeniu z `AuthorizeRouteView` składnikiem, umożliwia aplikacji określenie zawartości niestandardowej, jeśli:</span><span class="sxs-lookup"><span data-stu-id="52f7a-206">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="52f7a-207">Nie znaleziono zawartości.</span><span class="sxs-lookup"><span data-stu-id="52f7a-207">Content isn't found.</span></span>
* <span data-ttu-id="52f7a-208">Użytkownik nie może wykonać `[Authorize]` warunku zastosowanego do składnika.</span><span class="sxs-lookup"><span data-stu-id="52f7a-208">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="52f7a-209">Ten `[Authorize]` atrybut jest pokryty w sekcji [ `[Authorize]` atrybutu](#authorize-attribute) .</span><span class="sxs-lookup"><span data-stu-id="52f7a-209">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="52f7a-210">Uwierzytelnianie asynchroniczne jest w toku.</span><span class="sxs-lookup"><span data-stu-id="52f7a-210">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="52f7a-211">W domyślnym Blazor szablonie projektu serwera `App` składnik (*App. Razor*) demonstruje sposób ustawiania zawartości niestandardowej:</span><span class="sxs-lookup"><span data-stu-id="52f7a-211">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

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

<span data-ttu-id="52f7a-212">Zawartość `<NotFound>`, `<NotAuthorized>`, i `<Authorizing>` Tagi mogą zawierać dowolne elementy, takie jak inne składniki interaktywne.</span><span class="sxs-lookup"><span data-stu-id="52f7a-212">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="52f7a-213">Jeśli `<NotAuthorized>` element nie jest określony, zostanie `AuthorizeRouteView` użyty następujący komunikat rezerwowy:</span><span class="sxs-lookup"><span data-stu-id="52f7a-213">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="52f7a-214">Powiadomienie o zmianach stanu uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="52f7a-214">Notification about authentication state changes</span></span>

<span data-ttu-id="52f7a-215">Jeśli aplikacja określi, że dane stanu uwierzytelniania zostały zmienione (na przykład, ponieważ użytkownik wylogowany lub inny użytkownik zmienił swoje role), [niestandardowe AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) może opcjonalnie wywołać metodę `NotifyAuthenticationStateChanged` w klasie `AuthenticationStateProvider` bazowej.</span><span class="sxs-lookup"><span data-stu-id="52f7a-215">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="52f7a-216">Spowoduje to powiadomienie klientów o danych stanu uwierzytelniania (na przykład `AuthorizeView`) w celu ponownego renderowania przy użyciu nowych danych.</span><span class="sxs-lookup"><span data-stu-id="52f7a-216">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="52f7a-217">Logika proceduralna</span><span class="sxs-lookup"><span data-stu-id="52f7a-217">Procedural logic</span></span>

<span data-ttu-id="52f7a-218">Jeśli aplikacja jest wymagana do sprawdzenia reguł autoryzacji jako części logiki proceduralnej, należy użyć kaskadowego parametru typu `Task<AuthenticationState>` , aby uzyskać użytkownika. <xref:System.Security.Claims.ClaimsPrincipal></span><span class="sxs-lookup"><span data-stu-id="52f7a-218">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="52f7a-219">`Task<AuthenticationState>`można łączyć z innymi usługami, takimi jak `IAuthorizationService`, do analizowania zasad.</span><span class="sxs-lookup"><span data-stu-id="52f7a-219">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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
> <span data-ttu-id="52f7a-220">Blazor W składniku aplikacji webassembly Dodaj przestrzenie `Microsoft.AspNetCore.Authorization` nazw `Microsoft.AspNetCore.Components.Authorization` i:</span><span class="sxs-lookup"><span data-stu-id="52f7a-220">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="52f7a-221">Te przestrzenie nazw mogą być udostępniane globalnie przez dodanie ich do pliku *_Imports. Razor* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="52f7a-221">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="authorization-in-blazor-webassembly-apps"></a><span data-ttu-id="52f7a-222">Autoryzacja Blazor w aplikacjach webassembly</span><span class="sxs-lookup"><span data-stu-id="52f7a-222">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="52f7a-223">W Blazor aplikacjach webassembly sprawdzanie autoryzacji można obejść, ponieważ każdy kod po stronie klienta może być modyfikowany przez użytkowników.</span><span class="sxs-lookup"><span data-stu-id="52f7a-223">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="52f7a-224">Jest to samo prawdziwe dla wszystkich technologii aplikacji po stronie klienta, w tym dla struktur SPA skryptów JavaScript lub natywnych aplikacji dla dowolnego systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="52f7a-224">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="52f7a-225">**Zawsze sprawdzaj autoryzację na serwerze w ramach dowolnych punktów końcowych interfejsu API, do których uzyskuje dostęp aplikacja po stronie klienta.**</span><span class="sxs-lookup"><span data-stu-id="52f7a-225">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="52f7a-226">Aby uzyskać więcej informacji, zobacz artykuły poniżej <xref:security/blazor/webassembly/index>.</span><span class="sxs-lookup"><span data-stu-id="52f7a-226">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="52f7a-227">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="52f7a-227">Troubleshoot errors</span></span>

<span data-ttu-id="52f7a-228">Typowe błędy:</span><span class="sxs-lookup"><span data-stu-id="52f7a-228">Common errors:</span></span>

* <span data-ttu-id="52f7a-229">**Autoryzacja wymaga parametru kaskadowego typu Task\<AuthenticationState>. Rozważ użycie CascadingAuthenticationState, aby to zrobić.**</span><span class="sxs-lookup"><span data-stu-id="52f7a-229">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="52f7a-230">**`null`Odebrano wartość dla`authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="52f7a-230">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="52f7a-231">Prawdopodobnie projekt nie został utworzony przy użyciu szablonu Blazor serwera z włączonym uwierzytelnianiem.</span><span class="sxs-lookup"><span data-stu-id="52f7a-231">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="52f7a-232">Zawiń `<CascadingAuthenticationState>` wokół pewnej części drzewa interfejsu użytkownika, na przykład w `App` składniku (*App. Razor*) w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="52f7a-232">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="52f7a-233">`CascadingAuthenticationState` Dostarcza parametr `Task<AuthenticationState>` kaskadowy, który z kolei otrzymuje od podstawowej `AuthenticationStateProvider` usługi di.</span><span class="sxs-lookup"><span data-stu-id="52f7a-233">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52f7a-234">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="52f7a-234">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="52f7a-235">[Awesome Blazor:](https://github.com/AdrienTorris/awesome-blazor#authentication) przykładowe linki społeczności uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="52f7a-235">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
