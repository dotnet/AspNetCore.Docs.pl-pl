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
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a><span data-ttu-id="7c176-103">uwierzytelnianie i Blazor autoryzacja ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c176-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="7c176-104">Przez [Steve Sanderson](https://github.com/SteveSandersonMS) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7c176-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> <span data-ttu-id="7c176-105">Aby uzyskać wskazówki zawarte w Blazor tym artykule, który ma zastosowanie Blazor do WebAssembly, ASP.NET Core WebAssembly szablon w wersji 3.2 lub nowszej jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="7c176-105">For the guidance in this article that applies to Blazor WebAssembly, the ASP.NET Core Blazor WebAssembly template version 3.2 or later is required.</span></span> <span data-ttu-id="7c176-106">Jeśli nie używasz programu Visual Studio w wersji 16.6 Blazor Preview 2 lub nowszej, <xref:blazor/get-started>uzyskaj najnowszy szablon WebAssembly, postępając zgodnie ze wskazówkami w programie .</span><span class="sxs-lookup"><span data-stu-id="7c176-106">If you aren't using Visual Studio version 16.6 Preview 2 or later, obtain the latest Blazor WebAssembly template by following the guidance in <xref:blazor/get-started>.</span></span>

<span data-ttu-id="7c176-107">ASP.NET Core obsługuje konfigurację i zarządzanie Blazor zabezpieczeniami w aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="7c176-107">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="7c176-108">Scenariusze zabezpieczeń Blazor różnią Blazor się między aplikacjami Server i WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="7c176-108">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="7c176-109">Ponieważ Blazor aplikacje serwera działają na serwerze, kontrole autoryzacji są w stanie określić:</span><span class="sxs-lookup"><span data-stu-id="7c176-109">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="7c176-110">Opcje interfejsu użytkownika prezentowane użytkownikowi (na przykład, które pozycje menu są dostępne dla użytkownika).</span><span class="sxs-lookup"><span data-stu-id="7c176-110">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="7c176-111">Reguły dostępu dla obszarów aplikacji i składników.</span><span class="sxs-lookup"><span data-stu-id="7c176-111">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="7c176-112">Aplikacje WebAssembly są uruchamiane na kliencie.</span><span class="sxs-lookup"><span data-stu-id="7c176-112"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="7c176-113">Autoryzacja jest używana *tylko* do określenia, które opcje interfejsu użytkownika mają być wyświetlane.</span><span class="sxs-lookup"><span data-stu-id="7c176-113">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="7c176-114">Ponieważ kontrole po stronie klienta mogą być modyfikowane Blazor lub pomijane przez użytkownika, aplikacja WebAssembly nie może wymuszać reguł dostępu do autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="7c176-114">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="7c176-115">[Konwencje autoryzacji stron maszynki do golenia](xref:security/authorization/razor-pages-authorization) nie mają zastosowania do składników razor rutable.</span><span class="sxs-lookup"><span data-stu-id="7c176-115">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="7c176-116">Jeśli składnik Razor niesydywalny jest [osadzony na stronie,](xref:blazor/integrate-components#render-components-from-a-page-or-view)konwencje autoryzacji strony pośrednio wpływają na składnik Razor wraz z resztą zawartości strony.</span><span class="sxs-lookup"><span data-stu-id="7c176-116">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="7c176-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601>i <xref:Microsoft.AspNetCore.Identity.UserManager%601> nie są obsługiwane w komponentach Razor.</span><span class="sxs-lookup"><span data-stu-id="7c176-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="7c176-118">Authentication</span><span class="sxs-lookup"><span data-stu-id="7c176-118">Authentication</span></span>

Blazor<span data-ttu-id="7c176-119">używa istniejących mechanizmów uwierzytelniania ASP.NET Core w celu ustalenia tożsamości użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7c176-119"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="7c176-120">Dokładny mechanizm zależy od Blazor sposobu hostowanie Blazor aplikacji, WebAssembly lub Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="7c176-120">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="opno-locblazor-webassembly-authentication"></a>Blazor<span data-ttu-id="7c176-121">Uwierzytelnianie WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7c176-121"> WebAssembly authentication</span></span>

<span data-ttu-id="7c176-122">W Blazor aplikacjach WebAssembly można pominąć sprawdzanie uwierzytelniania, ponieważ cały kod po stronie klienta może być modyfikowany przez użytkowników.</span><span class="sxs-lookup"><span data-stu-id="7c176-122">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="7c176-123">To samo dotyczy wszystkich technologii aplikacji po stronie klienta, w tym struktur JavaScript SPA lub aplikacji natywnych dla dowolnego systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="7c176-123">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="7c176-124">Dodaj następujące elementy:</span><span class="sxs-lookup"><span data-stu-id="7c176-124">Add the following:</span></span>

* <span data-ttu-id="7c176-125">Odwołanie do pakietu dla [pliku projektu Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) do pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7c176-125">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="7c176-126">Obszar `Microsoft.AspNetCore.Components.Authorization` nazw do pliku *_Imports.razor* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7c176-126">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="7c176-127">Aby obsłużyć uwierzytelnianie, `AuthenticationStateProvider` implementacja wbudowanej lub niestandardowej usługi jest omówiona w poniższych sekcjach.</span><span class="sxs-lookup"><span data-stu-id="7c176-127">To handle authentication, implementation of a built-in or custom `AuthenticationStateProvider` service is covered in the following sections.</span></span>

<span data-ttu-id="7c176-128">Aby uzyskać więcej informacji na temat <xref:security/blazor/webassembly/index>tworzenia aplikacji i konfiguracji, zobacz .</span><span class="sxs-lookup"><span data-stu-id="7c176-128">For more information on creating apps and configuration, see <xref:security/blazor/webassembly/index>.</span></span>

### <a name="opno-locblazor-server-authentication"></a>Blazor<span data-ttu-id="7c176-129">Uwierzytelnianie serwera</span><span class="sxs-lookup"><span data-stu-id="7c176-129"> Server authentication</span></span>

Blazor<span data-ttu-id="7c176-130">Aplikacje serwerowe działają za pośrednictwem połączenia SignalRw czasie rzeczywistym, które jest tworzone przy użyciu .</span><span class="sxs-lookup"><span data-stu-id="7c176-130"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="7c176-131">[Uwierzytelnianie SignalRw aplikacjach opartych](xref:signalr/authn-and-authz) na aplikacjach jest obsługiwane po nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="7c176-131">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="7c176-132">Uwierzytelnianie może opierać się na pliku cookie lub innym tokenie nośnika.</span><span class="sxs-lookup"><span data-stu-id="7c176-132">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="7c176-133">Aby uzyskać więcej informacji na temat <xref:security/blazor/server>tworzenia aplikacji i konfiguracji, zobacz .</span><span class="sxs-lookup"><span data-stu-id="7c176-133">For more information on creating apps and configuration, see <xref:security/blazor/server>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="7c176-134">Usługa AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="7c176-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="7c176-135">Wbudowana `AuthenticationStateProvider` usługa uzyskuje dane o stanie uwierzytelniania z `HttpContext.User`ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c176-135">The built-in `AuthenticationStateProvider` service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="7c176-136">W ten sposób stan uwierzytelniania integruje się z istniejącymi mechanizmami uwierzytelniania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c176-136">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="7c176-137">`AuthenticationStateProvider`jest podstawową usługą `AuthorizeView` używaną przez składnik i `CascadingAuthenticationState` składnik do uzyskania stanu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="7c176-137">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="7c176-138">Zazwyczaj nie używasz `AuthenticationStateProvider` bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="7c176-138">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="7c176-139">Użyj [authorizeView składnika](#authorizeview-component) lub [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) podejścia opisane w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="7c176-139">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="7c176-140">Główną wadą `AuthenticationStateProvider` przy użyciu bezpośrednio jest to, że składnik nie jest powiadamiany automatycznie, jeśli podstawowe dane stanu uwierzytelniania zmiany.</span><span class="sxs-lookup"><span data-stu-id="7c176-140">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="7c176-141">Usługa `AuthenticationStateProvider` może dostarczyć dane bieżącego <xref:System.Security.Claims.ClaimsPrincipal> użytkownika, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="7c176-141">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

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

<span data-ttu-id="7c176-142">Jeśli `user.Identity.IsAuthenticated` `true` jest i ponieważ <xref:System.Security.Claims.ClaimsPrincipal>użytkownik jest , oświadczenia mogą być wyliczone i członkostwa w rolach oceniane.</span><span class="sxs-lookup"><span data-stu-id="7c176-142">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="7c176-143">Aby uzyskać więcej informacji na temat iniekcji zależności (DI) i usług, zobacz <xref:blazor/dependency-injection> i <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="7c176-143">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="7c176-144">Implementowanie niestandardowego narzędzia authenticationstateprovider</span><span class="sxs-lookup"><span data-stu-id="7c176-144">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="7c176-145">Jeśli aplikacja wymaga niestandardowego `AuthenticationStateProvider` dostawcy, zaimplementuj `GetAuthenticationStateAsync`i zastąp:</span><span class="sxs-lookup"><span data-stu-id="7c176-145">If the app requires a custom provider, implement `AuthenticationStateProvider` and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="7c176-146">W Blazor aplikacji WebAssembly `CustomAuthStateProvider` usługa jest `Main` zarejestrowana w *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="7c176-146">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="7c176-147">W Blazor aplikacji Server `CustomAuthStateProvider` usługa jest `Startup.ConfigureServices`zarejestrowana w :</span><span class="sxs-lookup"><span data-stu-id="7c176-147">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="7c176-148">`CustomAuthStateProvider` W poprzednim przykładzie wszyscy użytkownicy są uwierzytelnieni `mrfibuli`przy użyciu nazwy użytkownika .</span><span class="sxs-lookup"><span data-stu-id="7c176-148">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="7c176-149">Uwidacznianie stanu uwierzytelniania jako parametru kaskadowego</span><span class="sxs-lookup"><span data-stu-id="7c176-149">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="7c176-150">Jeśli dane o stanie uwierzytelniania są wymagane dla logiki proceduralnej, na przykład podczas wykonywania akcji wyzwalanej `Task<AuthenticationState>`przez użytkownika, uzyskaj dane stanu uwierzytelniania, definiując parametr kaskadowy typu:</span><span class="sxs-lookup"><span data-stu-id="7c176-150">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

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

<span data-ttu-id="7c176-151">Jeśli `user.Identity.IsAuthenticated` `true`tak , oświadczenia mogą być wyliczone i członkostwa w rolach oceniane.</span><span class="sxs-lookup"><span data-stu-id="7c176-151">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="7c176-152">Konfigurowanie `Task<AuthenticationState>` parametru kaskadowego `AuthorizeRouteView` `CascadingAuthenticationState` przy użyciu `App` i komponentów w składniku (*App.brzytwa):*</span><span class="sxs-lookup"><span data-stu-id="7c176-152">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the `App` component (*App.razor*):</span></span>

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

<span data-ttu-id="7c176-153">W Blazor aplikacji WebAssembly dodaj usługi dla `Program.Main`opcji i autoryzacji do:</span><span class="sxs-lookup"><span data-stu-id="7c176-153">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="7c176-154">W Blazor aplikacji Server usługi dla opcji i autoryzacji są już obecne, więc nie są wymagane żadne dalsze działania.</span><span class="sxs-lookup"><span data-stu-id="7c176-154">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="7c176-155">Autoryzacja</span><span class="sxs-lookup"><span data-stu-id="7c176-155">Authorization</span></span>

<span data-ttu-id="7c176-156">Po uwierzytelnieniu użytkownika *reguły autoryzacji* są stosowane do kontrolowania, co użytkownik może zrobić.</span><span class="sxs-lookup"><span data-stu-id="7c176-156">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="7c176-157">Dostęp jest zazwyczaj przyznawany lub odmawiany na podstawie tego, czy:</span><span class="sxs-lookup"><span data-stu-id="7c176-157">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="7c176-158">Użytkownik jest uwierzytelniony (zalogowany).</span><span class="sxs-lookup"><span data-stu-id="7c176-158">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="7c176-159">Użytkownik jest w *roli*.</span><span class="sxs-lookup"><span data-stu-id="7c176-159">A user is in a *role*.</span></span>
* <span data-ttu-id="7c176-160">Użytkownik ma *oświadczenie*.</span><span class="sxs-lookup"><span data-stu-id="7c176-160">A user has a *claim*.</span></span>
* <span data-ttu-id="7c176-161">*Zasada* jest spełniona.</span><span class="sxs-lookup"><span data-stu-id="7c176-161">A *policy* is satisfied.</span></span>

<span data-ttu-id="7c176-162">Każda z tych koncepcji jest taka sama jak w aplikacji ASP.NET Core MVC lub Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="7c176-162">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="7c176-163">Aby uzyskać więcej informacji na temat ASP.NET podstawowych zabezpieczeń, zobacz artykuły w obszarze [ASP.NET Bezpieczeństwo podstawowe i tożsamość](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="7c176-163">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="7c176-164">Autoryzując składnikView</span><span class="sxs-lookup"><span data-stu-id="7c176-164">AuthorizeView component</span></span>

<span data-ttu-id="7c176-165">Składnik `AuthorizeView` wybiórczo wyświetla interfejs użytkownika w zależności od tego, czy użytkownik jest upoważniony do jego wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="7c176-165">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="7c176-166">Takie podejście jest przydatne, gdy wystarczy *wyświetlić* dane dla użytkownika i nie trzeba używać tożsamości użytkownika w logice proceduralnej.</span><span class="sxs-lookup"><span data-stu-id="7c176-166">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="7c176-167">Składnik udostępnia zmienną `context` typu `AuthenticationState`, której można użyć, aby uzyskać dostęp do informacji o zalogowanym użytkowniku:</span><span class="sxs-lookup"><span data-stu-id="7c176-167">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="7c176-168">Można również podać inną zawartość do wyświetlania, jeśli użytkownik nie jest uwierzytelniony:</span><span class="sxs-lookup"><span data-stu-id="7c176-168">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="7c176-169">Składnik `AuthorizeView` może być używany `NavMenu` w składniku (*Shared/NavMenu.brzytwa*) do wyświetlania elementu listy (`<li>...</li>`) dla `NavLink`, ale należy pamiętać, że takie podejście usuwa tylko element listy z renderowanego wyjścia.</span><span class="sxs-lookup"><span data-stu-id="7c176-169">The `AuthorizeView` component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a `NavLink`, but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="7c176-170">Nie uniemożliwia użytkownikowi przechodzenia do składnika.</span><span class="sxs-lookup"><span data-stu-id="7c176-170">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="7c176-171">Zawartość `<Authorized>` i `<NotAuthorized>` tagi mogą zawierać dowolne elementy, takie jak inne składniki interaktywne.</span><span class="sxs-lookup"><span data-stu-id="7c176-171">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="7c176-172">Warunki autoryzacji, takie jak role lub zasady sterujące opcjami interfejsu użytkownika lub dostępem, są opisane w sekcji [Autoryzacja.](#authorization)</span><span class="sxs-lookup"><span data-stu-id="7c176-172">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="7c176-173">Jeśli warunki autoryzacji nie `AuthorizeView` są określone, używa zasad domyślnych i traktuje:</span><span class="sxs-lookup"><span data-stu-id="7c176-173">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="7c176-174">Uwierzytelnieni (zalogowani) użytkownicy jako autoryzowani.</span><span class="sxs-lookup"><span data-stu-id="7c176-174">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="7c176-175">Nieuwierzyleni (wylogowani) użytkownicy jako nieautoryzowani.</span><span class="sxs-lookup"><span data-stu-id="7c176-175">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="7c176-176">Autoryzacja oparta na rolach i zasadach</span><span class="sxs-lookup"><span data-stu-id="7c176-176">Role-based and policy-based authorization</span></span>

<span data-ttu-id="7c176-177">Składnik `AuthorizeView` obsługuje *autoryzację opartą na rolach* lub *zasadach.*</span><span class="sxs-lookup"><span data-stu-id="7c176-177">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="7c176-178">W przypadku autoryzacji opartej na rolach należy użyć parametru: `Roles`</span><span class="sxs-lookup"><span data-stu-id="7c176-178">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="7c176-179">Aby uzyskać więcej informacji, zobacz <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="7c176-179">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="7c176-180">W przypadku autoryzacji opartej na zasadach należy użyć parametru: `Policy`</span><span class="sxs-lookup"><span data-stu-id="7c176-180">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="7c176-181">Autoryzacja oparta na oświadczeniach jest szczególnym przypadkiem autoryzacji opartej na zasadach.</span><span class="sxs-lookup"><span data-stu-id="7c176-181">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="7c176-182">Na przykład można zdefiniować zasady, które wymagają od użytkowników, aby mieć pewne oświadczenie.</span><span class="sxs-lookup"><span data-stu-id="7c176-182">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="7c176-183">Aby uzyskać więcej informacji, zobacz <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="7c176-183">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="7c176-184">Te interfejsy API mogą być Blazor używane Blazor w aplikacjach Server lub WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="7c176-184">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="7c176-185">Jeśli nie `Roles` `Policy` jest ani `AuthorizeView` nie jest określony, używa zasad domyślnych.</span><span class="sxs-lookup"><span data-stu-id="7c176-185">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="7c176-186">Zawartość wyświetlana podczas uwierzytelniania asynchroniowego</span><span class="sxs-lookup"><span data-stu-id="7c176-186">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="7c176-187">pozwala na *asynchroniczne określanie*stanu uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="7c176-187"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="7c176-188">Podstawowym scenariuszem tego podejścia Blazor jest w webassembly aplikacji, które sprawiają, że żądanie do zewnętrznego punktu końcowego do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="7c176-188">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="7c176-189">Gdy uwierzytelnianie jest `AuthorizeView` w toku, domyślnie nie wyświetla zawartości.</span><span class="sxs-lookup"><span data-stu-id="7c176-189">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="7c176-190">Aby wyświetlić zawartość podczas `<Authorizing>` uwierzytelniania, należy użyć elementu:</span><span class="sxs-lookup"><span data-stu-id="7c176-190">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="7c176-191">Takie podejście zwykle nie ma Blazor zastosowania do aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="7c176-191">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="7c176-192">Aplikacje serwera znają stan uwierzytelniania zaraz po ustanowieniu stanu.</span><span class="sxs-lookup"><span data-stu-id="7c176-192"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="7c176-193">`Authorizing`zawartość może być Blazor dostarczana w `AuthorizeView` składniku aplikacji Server, ale nigdy nie jest wyświetlana.</span><span class="sxs-lookup"><span data-stu-id="7c176-193">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="7c176-194">Atrybut [Autoryzuj]</span><span class="sxs-lookup"><span data-stu-id="7c176-194">[Authorize] attribute</span></span>

<span data-ttu-id="7c176-195">Atrybut `[Authorize]` może być używany w składnikach Razor:</span><span class="sxs-lookup"><span data-stu-id="7c176-195">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="7c176-196">Używać `[Authorize]` tylko `@page` na komponentach, do których Blazor dotarł router.</span><span class="sxs-lookup"><span data-stu-id="7c176-196">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="7c176-197">Autoryzacja jest wykonywana tylko jako aspekt routingu, a *nie* dla składników podrzędnych renderowanych na stronie.</span><span class="sxs-lookup"><span data-stu-id="7c176-197">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="7c176-198">Aby autoryzować wyświetlanie określonych części na `AuthorizeView` stronie, należy użyć.</span><span class="sxs-lookup"><span data-stu-id="7c176-198">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="7c176-199">Atrybut `[Authorize]` obsługuje również autoryzację opartą na rolach lub zasadach.</span><span class="sxs-lookup"><span data-stu-id="7c176-199">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="7c176-200">W przypadku autoryzacji opartej na rolach należy użyć parametru: `Roles`</span><span class="sxs-lookup"><span data-stu-id="7c176-200">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="7c176-201">W przypadku autoryzacji opartej na zasadach należy użyć parametru: `Policy`</span><span class="sxs-lookup"><span data-stu-id="7c176-201">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="7c176-202">Jeśli nie `Roles` `Policy` jest ani, ani nie jest określony, `[Authorize]` używa domyślnej zasady, która domyślnie jest do leczenia:</span><span class="sxs-lookup"><span data-stu-id="7c176-202">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="7c176-203">Uwierzytelnieni (zalogowani) użytkownicy jako autoryzowani.</span><span class="sxs-lookup"><span data-stu-id="7c176-203">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="7c176-204">Nieuwierzyleni (wylogowani) użytkownicy jako nieautoryzowani.</span><span class="sxs-lookup"><span data-stu-id="7c176-204">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="7c176-205">Dostosowywanie nieautoryzowanej zawartości za pomocą składnika Router</span><span class="sxs-lookup"><span data-stu-id="7c176-205">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="7c176-206">Składnik `Router` w połączeniu ze `AuthorizeRouteView` składnikiem umożliwia aplikacji określenie zawartości niestandardowej, jeśli:</span><span class="sxs-lookup"><span data-stu-id="7c176-206">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="7c176-207">Nie znaleziono zawartości.</span><span class="sxs-lookup"><span data-stu-id="7c176-207">Content isn't found.</span></span>
* <span data-ttu-id="7c176-208">Użytkownik ulegnie `[Authorize]` awarii warunek stosowany do składnika.</span><span class="sxs-lookup"><span data-stu-id="7c176-208">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="7c176-209">Atrybut `[Authorize]` jest omówiony w sekcji [ `[Authorize]` atrybutu.](#authorize-attribute)</span><span class="sxs-lookup"><span data-stu-id="7c176-209">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="7c176-210">Uwierzytelnianie asynchroniczne jest w toku.</span><span class="sxs-lookup"><span data-stu-id="7c176-210">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="7c176-211">W domyślnym Blazor szablonie `App` projektu serwera składnik *(App.brzytwa)* pokazuje, jak ustawić zawartość niestandardową:</span><span class="sxs-lookup"><span data-stu-id="7c176-211">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

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

<span data-ttu-id="7c176-212">Zawartość `<NotFound>`programu `<NotAuthorized>`, `<Authorizing>` i tagów może zawierać dowolne elementy, takie jak inne składniki interaktywne.</span><span class="sxs-lookup"><span data-stu-id="7c176-212">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="7c176-213">Jeśli `<NotAuthorized>` element nie jest określony, `AuthorizeRouteView` używa następującego komunikatu rezerwowego:</span><span class="sxs-lookup"><span data-stu-id="7c176-213">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="7c176-214">Powiadomienie o zmianach stanu uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="7c176-214">Notification about authentication state changes</span></span>

<span data-ttu-id="7c176-215">Jeśli aplikacja określa, że podstawowe dane o stanie uwierzytelniania uległy zmianie (na przykład, ponieważ użytkownik wylogował się lub `NotifyAuthenticationStateChanged` inny `AuthenticationStateProvider` użytkownik zmienił swoje role), [niestandardowy AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) może opcjonalnie wywołać metodę w klasie podstawowej.</span><span class="sxs-lookup"><span data-stu-id="7c176-215">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="7c176-216">To powiadamia konsumentów o danych stanu uwierzytelniania `AuthorizeView`(na przykład ) do rerender przy użyciu nowych danych.</span><span class="sxs-lookup"><span data-stu-id="7c176-216">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="7c176-217">Logika proceduralna</span><span class="sxs-lookup"><span data-stu-id="7c176-217">Procedural logic</span></span>

<span data-ttu-id="7c176-218">Jeśli aplikacja jest wymagana do sprawdzania reguł autoryzacji w ramach logiki `Task<AuthenticationState>` proceduralnej, użyj <xref:System.Security.Claims.ClaimsPrincipal>kaskadowego parametru typu, aby uzyskać parametr użytkownika .</span><span class="sxs-lookup"><span data-stu-id="7c176-218">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="7c176-219">`Task<AuthenticationState>`można łączyć z innymi `IAuthorizationService`usługami, takimi jak , w celu oceny zasad.</span><span class="sxs-lookup"><span data-stu-id="7c176-219">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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
> <span data-ttu-id="7c176-220">W Blazor składniku aplikacji WebAssembly `Microsoft.AspNetCore.Authorization` `Microsoft.AspNetCore.Components.Authorization` dodaj obszary nazw i:</span><span class="sxs-lookup"><span data-stu-id="7c176-220">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="7c176-221">Te obszary nazw mogą być dostarczane globalnie, dodając je do pliku *_Imports.razor* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7c176-221">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a><span data-ttu-id="7c176-222">Autoryzacja Blazor w aplikacjach WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7c176-222">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="7c176-223">W Blazor aplikacjach WebAssembly można pominąć sprawdzanie autoryzacji, ponieważ cały kod po stronie klienta może być modyfikowany przez użytkowników.</span><span class="sxs-lookup"><span data-stu-id="7c176-223">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="7c176-224">To samo dotyczy wszystkich technologii aplikacji po stronie klienta, w tym struktur JavaScript SPA lub aplikacji natywnych dla dowolnego systemu operacyjnego.</span><span class="sxs-lookup"><span data-stu-id="7c176-224">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="7c176-225">**Zawsze wykonuj kontrole autoryzacji na serwerze w obrębie dowolnego punktu końcowego interfejsu API, do którego ma dostęp aplikacja po stronie klienta.**</span><span class="sxs-lookup"><span data-stu-id="7c176-225">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="7c176-226">Aby uzyskać więcej informacji, <xref:security/blazor/webassembly/index>zobacz artykuły w obszarze .</span><span class="sxs-lookup"><span data-stu-id="7c176-226">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="7c176-227">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="7c176-227">Troubleshoot errors</span></span>

<span data-ttu-id="7c176-228">Typowe błędy:</span><span class="sxs-lookup"><span data-stu-id="7c176-228">Common errors:</span></span>

* <span data-ttu-id="7c176-229">**Autoryzacja wymaga parametru kaskadowego typu Task\<AuthenticationState>. Należy rozważyć użycie CascadingAuthenticationState do dostarczania tego.**</span><span class="sxs-lookup"><span data-stu-id="7c176-229">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="7c176-230">**`null`wartość jest odbierana dla`authenticationStateTask`**</span><span class="sxs-lookup"><span data-stu-id="7c176-230">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="7c176-231">Jest prawdopodobne, że projekt nie został Blazor utworzony przy użyciu szablonu serwera z włączonym uwierzytelnianiem.</span><span class="sxs-lookup"><span data-stu-id="7c176-231">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="7c176-232">Owiń `<CascadingAuthenticationState>` wokół jakiejś części drzewa interfejsu `App` użytkownika, na przykład w składniku *(App.brzytwa)* w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="7c176-232">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="7c176-233">Dostarcza `CascadingAuthenticationState` parametr `Task<AuthenticationState>` kaskadowy, który z kolei otrzymuje `AuthenticationStateProvider` od podstawowej usługi DI.</span><span class="sxs-lookup"><span data-stu-id="7c176-233">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c176-234">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7c176-234">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="7c176-235">[Niesamowite Blazor: Uwierzytelniania](https://github.com/AdrienTorris/awesome-blazor#authentication) społeczności przykładowe linki</span><span class="sxs-lookup"><span data-stu-id="7c176-235">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
