---
title: Bezpieczne aplikacje ASP.NET Blazor Core Server
author: guardrex
description: Dowiedz się, jak ograniczyć Blazor zagrożenia bezpieczeństwa dla aplikacji serwera.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626021"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="9b6f5-103">Bezpieczne ASP.NET aplikacje Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="9b6f5-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="9b6f5-104">Przez [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="9b6f5-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="9b6f5-105">Aplikacje Blazor Server przyjmują *stanowy* model przetwarzania danych, w którym serwer i klient utrzymują długotrwałą relację.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-105">Blazor Server apps adopt a *stateful* data processing model, where the server and client maintain a long-lived relationship.</span></span> <span data-ttu-id="9b6f5-106">Stan trwały jest utrzymywany przez [obwód,](xref:blazor/state-management)który może obejmować połączenia, które są również potencjalnie długotrwałe.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-106">The persistent state is maintained by a [circuit](xref:blazor/state-management), which can span connections that are also potentially long-lived.</span></span>

<span data-ttu-id="9b6f5-107">Gdy użytkownik odwiedza witrynę serwera Blazor, serwer tworzy obwód w pamięci serwera.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-107">When a user visits a Blazor Server site, the server creates a circuit in the server's memory.</span></span> <span data-ttu-id="9b6f5-108">Obwód wskazuje przeglądarce, jaką zawartość ma być renderowana i reaguje na zdarzenia, na przykład gdy użytkownik wybierze przycisk w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-108">The circuit indicates to the browser what content to render and responds to events, such as when the user selects a button in the UI.</span></span> <span data-ttu-id="9b6f5-109">Aby wykonać te akcje, obwód wywołuje funkcje JavaScript w przeglądarce użytkownika i metody .NET na serwerze.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-109">To perform these actions, a circuit invokes JavaScript functions in the user's browser and .NET methods on the server.</span></span> <span data-ttu-id="9b6f5-110">Ta dwukierunkowa interakcja oparta na JavaScript jest określana jako [javascript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).</span><span class="sxs-lookup"><span data-stu-id="9b6f5-110">This two-way JavaScript-based interaction is referred to as [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).</span></span>

<span data-ttu-id="9b6f5-111">Ponieważ JS interop występuje przez Internet, a klient używa przeglądarki zdalnej, aplikacje Blazor Server udostępniają większość problemów związanych z bezpieczeństwem aplikacji internetowych.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-111">Because JS interop occurs over the Internet and the client uses a remote browser, Blazor Server apps share most web app security concerns.</span></span> <span data-ttu-id="9b6f5-112">W tym temacie opisano typowe zagrożenia dla aplikacji Blazor Server i zawiera wskazówki dotyczące ograniczania zagrożeń koncentruje się na aplikacjach internetowych.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-112">This topic describes common threats to Blazor Server apps and provides threat mitigation guidance focused on Internet-facing apps.</span></span>

<span data-ttu-id="9b6f5-113">W ograniczonych środowiskach, takich jak wewnątrz sieci firmowych lub intranetów, niektóre wskazówki dotyczące łagodzenia albo:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-113">In constrained environments, such as inside corporate networks or intranets, some of the mitigation guidance either:</span></span>

* <span data-ttu-id="9b6f5-114">Nie ma zastosowania w środowisku ograniczonym.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-114">Doesn't apply in the constrained environment.</span></span>
* <span data-ttu-id="9b6f5-115">Nie jest wart kosztów do zaimplementowania, ponieważ ryzyko zabezpieczeń jest niska w środowisku ograniczonym.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-115">Isn't worth the cost to implement because the security risk is low in a constrained environment.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="9b6f5-116">Szablon projektu serwera Blazor</span><span class="sxs-lookup"><span data-stu-id="9b6f5-116">Blazor Server project template</span></span>

<span data-ttu-id="9b6f5-117">Szablon projektu Blazor Server można skonfigurować do uwierzytelniania podczas tworzenia projektu.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-117">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9b6f5-118">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b6f5-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9b6f5-119">Postępuj zgodnie ze <xref:blazor/get-started> wskazówkami programu Visual Studio w tym artykule, aby utworzyć nowy projekt blazor server z mechanizmem uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-119">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="9b6f5-120">Po wybraniu szablonu **aplikacji Blazor Server** w oknie **dialogowym Tworzenie nowej ASP.NET Core Web Application** wybierz pozycję **Zmień** w obszarze **Uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-120">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="9b6f5-121">Zostanie otwarte okno dialogowe oferujące ten sam zestaw mechanizmów uwierzytelniania dostępnych dla innych projektów ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-121">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="9b6f5-122">**Bez uwierzytelniania**</span><span class="sxs-lookup"><span data-stu-id="9b6f5-122">**No Authentication**</span></span>
* <span data-ttu-id="9b6f5-123">**Konta użytkowników indywidualnych** &ndash; mogą być przechowywane:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-123">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="9b6f5-124">W aplikacji przy użyciu systemu [tożsamości](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-124">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="9b6f5-125">Z [usługą Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="9b6f5-125">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="9b6f5-126">**Konta służbowe lub szkolne**</span><span class="sxs-lookup"><span data-stu-id="9b6f5-126">**Work or School Accounts**</span></span>
* <span data-ttu-id="9b6f5-127">**Uwierzytelnianie systemu Windows**</span><span class="sxs-lookup"><span data-stu-id="9b6f5-127">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9b6f5-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9b6f5-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9b6f5-129">Postępuj zgodnie ze wskazówkami dotyczącymi kodu programu Visual Studio w tym artykule, <xref:blazor/get-started> aby utworzyć nowy projekt serwera Blazor z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-129">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="9b6f5-130">Dopuszczalne wartości uwierzytelniania`{AUTHENTICATION}`( ) są pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-130">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="9b6f5-131">Mechanizm uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="9b6f5-131">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="9b6f5-132">`{AUTHENTICATION}`Wartość</span><span class="sxs-lookup"><span data-stu-id="9b6f5-132">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="9b6f5-133">Bez uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="9b6f5-133">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="9b6f5-134">Indywidualne</span><span class="sxs-lookup"><span data-stu-id="9b6f5-134">Individual</span></span><br><span data-ttu-id="9b6f5-135">Użytkownicy zapisani w aplikacji z ASP.NET podstawową tożsamością.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-135">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="9b6f5-136">Indywidualne</span><span class="sxs-lookup"><span data-stu-id="9b6f5-136">Individual</span></span><br><span data-ttu-id="9b6f5-137">Użytkownicy zapisani w [usłudze Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="9b6f5-137">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="9b6f5-138">Konta służbowe lub szkolne</span><span class="sxs-lookup"><span data-stu-id="9b6f5-138">Work or School Accounts</span></span><br><span data-ttu-id="9b6f5-139">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-139">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="9b6f5-140">Konta służbowe lub szkolne</span><span class="sxs-lookup"><span data-stu-id="9b6f5-140">Work or School Accounts</span></span><br><span data-ttu-id="9b6f5-141">Uwierzytelnianie organizacyjne dla wielu dzierżawców.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-141">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="9b6f5-142">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="9b6f5-142">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="9b6f5-143">Polecenie tworzy folder o nazwie o `{APP NAME}` wartości podanej dla symbolu zastępczego i używa nazwy folderu jako nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-143">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="9b6f5-144">Aby uzyskać więcej informacji, zobacz nowe polecenie [dotnet](/dotnet/core/tools/dotnet-new) w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-144">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9b6f5-145">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="9b6f5-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9b6f5-146">Postępuj zgodnie ze wskazówkami <xref:blazor/get-started> dotyczącymi programu Visual Studio dla komputerów Mac w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-146">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="9b6f5-147">W kroku **Konfigurowanie nowej aplikacji Serwera Blazor** wybierz pozycję **Uwierzytelnianie indywidualne (w aplikacji)** z listy rozwijanej **Uwierzytelnianie.**</span><span class="sxs-lookup"><span data-stu-id="9b6f5-147">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="9b6f5-148">Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji z ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-148">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9b6f5-149">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="9b6f5-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="9b6f5-150">Postępuj zgodnie ze wskazówkami <xref:blazor/get-started> .NET Core CLI w artykule, aby utworzyć nowy projekt serwera Blazor z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-150">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="9b6f5-151">Dopuszczalne wartości uwierzytelniania`{AUTHENTICATION}`( ) są pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-151">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="9b6f5-152">Mechanizm uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="9b6f5-152">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="9b6f5-153">`{AUTHENTICATION}`Wartość</span><span class="sxs-lookup"><span data-stu-id="9b6f5-153">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="9b6f5-154">Bez uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="9b6f5-154">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="9b6f5-155">Indywidualne</span><span class="sxs-lookup"><span data-stu-id="9b6f5-155">Individual</span></span><br><span data-ttu-id="9b6f5-156">Użytkownicy zapisani w aplikacji z ASP.NET podstawową tożsamością.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-156">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="9b6f5-157">Indywidualne</span><span class="sxs-lookup"><span data-stu-id="9b6f5-157">Individual</span></span><br><span data-ttu-id="9b6f5-158">Użytkownicy zapisani w [usłudze Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="9b6f5-158">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="9b6f5-159">Konta służbowe lub szkolne</span><span class="sxs-lookup"><span data-stu-id="9b6f5-159">Work or School Accounts</span></span><br><span data-ttu-id="9b6f5-160">Uwierzytelnianie organizacyjne dla pojedynczej dzierżawy.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-160">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="9b6f5-161">Konta służbowe lub szkolne</span><span class="sxs-lookup"><span data-stu-id="9b6f5-161">Work or School Accounts</span></span><br><span data-ttu-id="9b6f5-162">Uwierzytelnianie organizacyjne dla wielu dzierżawców.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-162">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="9b6f5-163">Uwierzytelnianie systemu Windows</span><span class="sxs-lookup"><span data-stu-id="9b6f5-163">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="9b6f5-164">Polecenie tworzy folder o nazwie o `{APP NAME}` wartości podanej dla symbolu zastępczego i używa nazwy folderu jako nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-164">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="9b6f5-165">Aby uzyskać więcej informacji, zobacz nowe polecenie [dotnet](/dotnet/core/tools/dotnet-new) w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-165">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="9b6f5-166">Przekazywanie tokenów do aplikacji Blazor Server</span><span class="sxs-lookup"><span data-stu-id="9b6f5-166">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="9b6f5-167">Uwierzytelnij aplikację Blazor Server tak, jak w przypadku zwykłych stron Razor lub aplikacji MVC.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-167">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="9b6f5-168">Aprowizuj i zapisz tokeny w pliku cookie uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-168">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="9b6f5-169">Przykład:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-169">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="9b6f5-170">Przykładowy kod, w `Startup.ConfigureServices` tym pełny przykład, zobacz [Przekazywanie tokenów do aplikacji Blazor po stronie serwera](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="9b6f5-170">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="9b6f5-171">Zdefiniuj klasę do przekazania w początkowym stanie aplikacji za pomocą tokenów dostępu i odświeżania:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-171">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="9b6f5-172">Zdefiniuj usługę dostawcy tokenu **o określonym zakresie,** która może być używana w aplikacji Blazor do rozpoznawania tokenów z DI:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-172">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from DI:</span></span>

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="9b6f5-173">W `Startup.ConfigureServices`, dodaj usługi dla:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-173">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="9b6f5-174">W pliku *_Host.cshtml* utwórz i wystąpienie `InitialApplicationState` i przekaż go jako parametr do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-174">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="9b6f5-175">W `App` składniku (*App.brzytwa*), rozwiąż usługę i zainiólj ją danymi z parametru:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-175">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="9b6f5-176">W usłudze, która sprawia, że żądanie bezpiecznego interfejsu API, wstrzyknąć dostawcę tokenu i pobrać token do wywołania interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-176">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a><span data-ttu-id="9b6f5-177">Wyczerpanie zasobów</span><span class="sxs-lookup"><span data-stu-id="9b6f5-177">Resource exhaustion</span></span>

<span data-ttu-id="9b6f5-178">Wyczerpanie zasobów może wystąpić, gdy klient wchodzi w interakcję z serwerem i powoduje, że serwer zużywa nadmierne zasoby.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-178">Resource exhaustion can occur when a client interacts with the server and causes the server to consume excessive resources.</span></span> <span data-ttu-id="9b6f5-179">Nadmierne zużycie zasobów ma przede wszystkim wpływ:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-179">Excessive resource consumption primarily affects:</span></span>

* [<span data-ttu-id="9b6f5-180">Procesora</span><span class="sxs-lookup"><span data-stu-id="9b6f5-180">CPU</span></span>](#cpu)
* [<span data-ttu-id="9b6f5-181">Pamięci</span><span class="sxs-lookup"><span data-stu-id="9b6f5-181">Memory</span></span>](#memory)
* [<span data-ttu-id="9b6f5-182">Połączenia klienta</span><span class="sxs-lookup"><span data-stu-id="9b6f5-182">Client connections</span></span>](#client-connections)

<span data-ttu-id="9b6f5-183">Ataki typu "odmowa usługi" (DoS) zwykle mają na celu wyczerpanie zasobów aplikacji lub serwera.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-183">Denial of service (DoS) attacks usually seek to exhaust an app or server's resources.</span></span> <span data-ttu-id="9b6f5-184">Jednak wyczerpanie zasobów nie musi być wynikiem ataku na system.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-184">However, resource exhaustion isn't necessarily the result of an attack on the system.</span></span> <span data-ttu-id="9b6f5-185">Na przykład ograniczone zasoby mogą zostać wyczerpane ze względu na duże zapotrzebowanie użytkowników.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-185">For example, finite resources can be exhausted due to high user demand.</span></span> <span data-ttu-id="9b6f5-186">DoS jest dalej objęty w sekcji [Ataki typu "odmowa usługi" (DoS).](#denial-of-service-dos-attacks)</span><span class="sxs-lookup"><span data-stu-id="9b6f5-186">DoS is covered further in the [Denial of service (DoS) attacks](#denial-of-service-dos-attacks) section.</span></span>

<span data-ttu-id="9b6f5-187">Zasoby zewnętrzne do platformy Blazor, takie jak bazy danych i dojścia plików (używane do odczytu i zapisu plików), mogą również wystąpić wyczerpanie zasobów.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-187">Resources external to the Blazor framework, such as databases and file handles (used to read and write files), may also experience resource exhaustion.</span></span> <span data-ttu-id="9b6f5-188">Aby uzyskać więcej informacji, zobacz <xref:performance/performance-best-practices>.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-188">For more information, see <xref:performance/performance-best-practices>.</span></span>

### <a name="cpu"></a><span data-ttu-id="9b6f5-189">Procesor CPU</span><span class="sxs-lookup"><span data-stu-id="9b6f5-189">CPU</span></span>

<span data-ttu-id="9b6f5-190">Wyczerpanie procesora CPU może wystąpić, gdy jeden lub więcej klientów wymusić serwer do wykonywania intensywnej pracy procesora CPU.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-190">CPU exhaustion can occur when one or more clients force the server to perform intensive CPU work.</span></span>

<span data-ttu-id="9b6f5-191">Rozważmy na przykład aplikację Blazor Server, która oblicza *liczbę Fibonnacci*.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-191">For example, consider a Blazor Server app that calculates a *Fibonnacci number*.</span></span> <span data-ttu-id="9b6f5-192">Numer Fibonnacci jest produkowany z sekwencji Fibonnacci, gdzie każda liczba w sekwencji jest sumą dwóch poprzednich liczb.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-192">A Fibonnacci number is produced from a Fibonnacci sequence, where each number in the sequence is the sum of the two preceding numbers.</span></span> <span data-ttu-id="9b6f5-193">Ilość pracy wymaganej do uzyskania odpowiedzi zależy od długości sekwencji i rozmiaru wartości początkowej.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-193">The amount of work required to reach the answer depends on the length of the sequence and the size of the initial value.</span></span> <span data-ttu-id="9b6f5-194">Jeśli aplikacja nie nakłada limitów na żądanie klienta, obliczenia intensywnie korzystające z procesora CPU mogą zdominować czas procesora CPU i zmniejszyć wydajność innych zadań.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-194">If the app doesn't place limits on a client's request, the CPU-intensive calculations may dominate the CPU's time and diminish the performance of other tasks.</span></span> <span data-ttu-id="9b6f5-195">Nadmierne zużycie zasobów jest problemem bezpieczeństwa wpływającym na dostępność.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-195">Excessive resource consumption is a security concern impacting availability.</span></span>

<span data-ttu-id="9b6f5-196">Wyczerpanie procesora jest problemem dla wszystkich aplikacji publicznych.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-196">CPU exhaustion is a concern for all public-facing apps.</span></span> <span data-ttu-id="9b6f5-197">W zwykłych aplikacjach internetowych, żądania i połączenia limit czasu jako zabezpieczenie, ale aplikacje Blazor Server nie zapewniają tych samych zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-197">In regular web apps, requests and connections time out as a safeguard, but Blazor Server apps don't provide the same safeguards.</span></span> <span data-ttu-id="9b6f5-198">Aplikacje Blazor Server muszą zawierać odpowiednie kontrole i limity przed wykonaniem potencjalnie intensywnej pracy procesora.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-198">Blazor Server apps must include appropriate checks and limits before performing potentially CPU-intensive work.</span></span>

### <a name="memory"></a><span data-ttu-id="9b6f5-199">Memory (Pamięć)</span><span class="sxs-lookup"><span data-stu-id="9b6f5-199">Memory</span></span>

<span data-ttu-id="9b6f5-200">Wyczerpanie pamięci może wystąpić, gdy jeden lub więcej klientów wymusić serwer do korzystania z dużej ilości pamięci.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-200">Memory exhaustion can occur when one or more clients force the server to consume a large amount of memory.</span></span>

<span data-ttu-id="9b6f5-201">Rozważmy na przykład aplikację po stronie serwera Blazor ze składnikiem, który akceptuje i wyświetla listę elementów.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-201">For example, consider a Blazor-server side app with a component that accepts and displays a list of items.</span></span> <span data-ttu-id="9b6f5-202">Jeśli aplikacja Blazor nie nakłada limitów na liczbę dozwolonych elementów lub liczbę elementów renderowanych z powrotem do klienta, przetwarzanie i renderowanie intensywnie korzystające z pamięci może zdominować pamięć serwera do tego stopnia, że wydajność serwera cierpi.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-202">If the Blazor app doesn't place limits on the number of items allowed or the number of items rendered back to the client, the memory-intensive processing and rendering may dominate the server's memory to the point where performance of the server suffers.</span></span> <span data-ttu-id="9b6f5-203">Serwer może ulec awarii lub spowolnić do tego stopnia, że wydaje się, że uległ awarii.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-203">The server may crash or slow to the point that it appears to have crashed.</span></span>

<span data-ttu-id="9b6f5-204">Należy wziąć pod uwagę następujący scenariusz do utrzymania i wyświetlania listy elementów, które odnoszą się do potencjalnego scenariusza wyczerpania pamięci na serwerze:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-204">Consider the following scenario for maintaining and displaying a list of items that pertain to a potential memory exhaustion scenario on the server:</span></span>

* <span data-ttu-id="9b6f5-205">Elementy we `List<MyItem>` właściwości lub polu używają pamięci serwera.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-205">The items in a `List<MyItem>` property or field use the server's memory.</span></span> <span data-ttu-id="9b6f5-206">Jeśli aplikacja zezwala na listę elementów, aby rozwijać bez ograniczeń, istnieje ryzyko serwera wyczerpania pamięci.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-206">If the app allows the list of items to grow unbounded, there's a risk of the server running out of memory.</span></span> <span data-ttu-id="9b6f5-207">Wyczerpanie pamięci powoduje, że bieżąca sesja kończy się (awaria), a wszystkie równoczesne sesje w tym wystąpieniu serwera otrzymują wyjątek braku pamięci.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-207">Running out of memory causes the current session to end (crash) and all of the concurrent sessions in that server instance receive an out-of-memory exception.</span></span> <span data-ttu-id="9b6f5-208">Aby zapobiec występowaniu tego scenariusza, aplikacja musi używać struktury danych, która nakłada limit elementów na równoczesnych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-208">To prevent this scenario from occurring, the app must use a data structure that imposes an item limit on concurrent users.</span></span>
* <span data-ttu-id="9b6f5-209">Jeśli schemat stronicowania nie jest używany do renderowania, serwer używa dodatkowej pamięci dla obiektów, które nie są widoczne w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-209">If a paging scheme isn't used for rendering, the server uses additional memory for objects that aren't visible in the UI.</span></span> <span data-ttu-id="9b6f5-210">Bez ograniczenia liczby elementów zapotrzebowanie na pamięć może wyczerpać dostępną pamięć serwera.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-210">Without a limit on the number of items, memory demands may exhaust the available server memory.</span></span> <span data-ttu-id="9b6f5-211">Aby zapobiec temu scenariuszowi, należy użyć jednego z następujących podejść:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-211">To prevent this scenario, use one of the following approaches:</span></span>
  * <span data-ttu-id="9b6f5-212">Podczas renderowania należy używać list na strony.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-212">Use paginated lists when rendering.</span></span>
  * <span data-ttu-id="9b6f5-213">Wyświetlaj tylko pierwsze od 100 do 1000 elementów i wymagaj od użytkownika wprowadzania kryteriów wyszukiwania w celu znalezienia elementów poza wyświetlanymi elementami.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-213">Only display the first 100 to 1,000 items and require the user to enter search criteria to find items beyond the items displayed.</span></span>
  * <span data-ttu-id="9b6f5-214">W przypadku bardziej zaawansowanego scenariusza renderowania zaimplementuj listy lub siatki obsługujące *wirtualizację*.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-214">For a more advanced rendering scenario, implement lists or grids that support *virtualization*.</span></span> <span data-ttu-id="9b6f5-215">Za pomocą wirtualizacji listy renderuje tylko podzbiór elementów aktualnie widocznych dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-215">Using virtualization, lists only render a subset of items currently visible to the user.</span></span> <span data-ttu-id="9b6f5-216">Gdy użytkownik wchodzi w interakcję z paskiem przewijania w interfejsie użytkownika, składnik renderuje tylko te elementy wymagane do wyświetlenia.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-216">When the user interacts with the scrollbar in the UI, the component renders only those items required for display.</span></span> <span data-ttu-id="9b6f5-217">Elementy, które nie są obecnie wymagane do wyświetlania mogą być przechowywane w magazynie pomocniczym, co jest idealnym rozwiązaniem.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-217">The items that aren't currently required for display can be held in secondary storage, which is the ideal approach.</span></span> <span data-ttu-id="9b6f5-218">Nieuwydajone przedmioty mogą być również przechowywane w pamięci, co jest mniej idealne.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-218">Undisplayed items can also be held in memory, which is less ideal.</span></span>

<span data-ttu-id="9b6f5-219">Aplikacje Blazor Server oferują podobny model programowania do innych struktur interfejsu użytkownika dla aplikacji stanowych, takich jak WPF, Windows Forms lub Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-219">Blazor Server apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor WebAssembly.</span></span> <span data-ttu-id="9b6f5-220">Główną różnicą jest to, że w kilku ramach interfejsu użytkownika pamięci używanej przez aplikację należy do klienta i dotyczy tylko tego klienta.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-220">The main difference is that in several of the UI frameworks the memory consumed by the app belongs to the client and only affects that individual client.</span></span> <span data-ttu-id="9b6f5-221">Na przykład aplikacja Blazor WebAssembly działa całkowicie na kliencie i używa tylko zasobów pamięci klienta.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-221">For example, a Blazor WebAssembly app runs entirely on the client and only uses client memory resources.</span></span> <span data-ttu-id="9b6f5-222">W scenariuszu serwera Blazor pamięć zużywana przez aplikację należy do serwera i jest współużytkowana przez klientów w wystąpieniu serwera.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-222">In the Blazor Server scenario, the memory consumed by the app belongs to the server and is shared among clients on the server instance.</span></span>

<span data-ttu-id="9b6f5-223">Wymagania dotyczące pamięci po stronie serwera są brane pod uwagę dla wszystkich aplikacji Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-223">Server-side memory demands are a consideration for all Blazor Server apps.</span></span> <span data-ttu-id="9b6f5-224">Jednak większość aplikacji sieci web są bezstanowe, a pamięć używana podczas przetwarzania żądania jest zwalniana po zwróceniu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-224">However, most web apps are stateless, and the memory used while processing a request is released when the response is returned.</span></span> <span data-ttu-id="9b6f5-225">Zgodnie z ogólnym zaleceniem nie zezwalaj klientom na przydzielanie niezwiązanej ilości pamięci, tak jak w każdej innej aplikacji po stronie serwera, która utrzymuje połączenia klienta.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-225">As a general recommendation, don't permit clients to allocate an unbound amount of memory as in any other server-side app that persists client connections.</span></span> <span data-ttu-id="9b6f5-226">Pamięć zużywana przez aplikację Blazor Server jest utrzymywana przez dłuższy czas niż pojedyncze żądanie.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-226">The memory consumed by a Blazor Server app persists for a longer time than a single request.</span></span>

> [!NOTE]
> <span data-ttu-id="9b6f5-227">Podczas tworzenia profiler może służyć lub śledzenia przechwycone do oceny zapotrzebowania na pamięć klientów.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-227">During development, a profiler can be used or a trace captured to assess memory demands of clients.</span></span> <span data-ttu-id="9b6f5-228">Profiler lub śledzenia nie przechwytuje pamięci przydzielonej do określonego klienta.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-228">A profiler or trace won't capture the memory allocated to a specific client.</span></span> <span data-ttu-id="9b6f5-229">Aby przechwycić użycie pamięci określonego klienta podczas tworzenia, przechwyć zrzut i sprawdź zapotrzebowanie na pamięć wszystkich obiektów zakorzenionych w obwodzie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-229">To capture the memory use of a specific client during development, capture a dump and examine the memory demand of all the objects rooted at a user's circuit.</span></span>

### <a name="client-connections"></a><span data-ttu-id="9b6f5-230">Połączenia klienta</span><span class="sxs-lookup"><span data-stu-id="9b6f5-230">Client connections</span></span>

<span data-ttu-id="9b6f5-231">Wyczerpanie połączenia może wystąpić, gdy jeden lub więcej klientów otworzy zbyt wiele równoczesnych połączeń z serwerem, uniemożliwiając innym klientom ustanawianie nowych połączeń.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-231">Connection exhaustion can occur when one or more clients open too many concurrent connections to the server, preventing other clients from establishing new connections.</span></span>

<span data-ttu-id="9b6f5-232">Klienci Blazor nawiązają jedno połączenie na sesję i utrzymują połączenie otwarte tak długo, jak długo jest otwarte okno przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-232">Blazor clients establish a single connection per session and keep the connection open for as long as the browser window is open.</span></span> <span data-ttu-id="9b6f5-233">Wymagania dotyczące utrzymywania wszystkich połączeń przez serwer nie są specyficzne dla aplikacji Blazor.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-233">The demands on the server of maintaining all of the connections isn't specific to Blazor apps.</span></span> <span data-ttu-id="9b6f5-234">Biorąc pod uwagę trwały charakter połączeń i stanowy charakter aplikacji Blazor Server, wyczerpanie połączeń jest większym ryzykiem dla dostępności aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-234">Given the persistent nature of the connections and the stateful nature of Blazor Server apps, connection exhaustion is a greater risk to availability of the app.</span></span>

<span data-ttu-id="9b6f5-235">Domyślnie nie ma limitu liczby połączeń na użytkownika dla aplikacji Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-235">By default, there's no limit on the number of connections per user for a Blazor Server app.</span></span> <span data-ttu-id="9b6f5-236">Jeśli aplikacja wymaga limitu połączenia, należy podjąć co najmniej jedną z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-236">If the app requires a connection limit, take one or more of the following approaches:</span></span>

* <span data-ttu-id="9b6f5-237">Wymagaj uwierzytelniania, co oczywiście ogranicza możliwość łączenia się z aplikacją przez nieautoryzowanych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-237">Require authentication, which naturally limits the ability of unauthorized users to connect to the app.</span></span> <span data-ttu-id="9b6f5-238">Aby ten scenariusz był skuteczny, użytkownicy muszą być uniemożliwione inicjowania obsługi administracyjnej nowych użytkowników do woli.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-238">For this scenario to be effective, users must be prevented from provisioning new users at will.</span></span>
* <span data-ttu-id="9b6f5-239">Ogranicz liczbę połączeń na użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-239">Limit the number of connections per user.</span></span> <span data-ttu-id="9b6f5-240">Ograniczenie połączeń można wykonać za pomocą następujących metod.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-240">Limiting connections can be accomplished via the following approaches.</span></span> <span data-ttu-id="9b6f5-241">Należy zachować ostrożność, aby umożliwić legalnym użytkownikom dostęp do aplikacji (na przykład, gdy limit połączenia jest ustanawiany na podstawie adresu IP klienta).</span><span class="sxs-lookup"><span data-stu-id="9b6f5-241">Exercise care to allow legitimate users to access the app (for example, when a connection limit is established based on the client's IP address).</span></span>
  * <span data-ttu-id="9b6f5-242">Na poziomie aplikacji:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-242">At the app level:</span></span>
    * <span data-ttu-id="9b6f5-243">Rozszerzalność routingu punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-243">Endpoint routing extensibility.</span></span>
    * <span data-ttu-id="9b6f5-244">Wymagaj uwierzytelniania, aby połączyć się z aplikacją i śledzić aktywne sesje na użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-244">Require authentication to connect to the app and keep track of the active sessions per user.</span></span>
    * <span data-ttu-id="9b6f5-245">Odrzuć nowe sesje po osiągnięciu limitu.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-245">Reject new sessions upon reaching a limit.</span></span>
    * <span data-ttu-id="9b6f5-246">Proxy WebSocket połączenia z aplikacją za pomocą serwera proxy, takich jak [usługa Azure SignalR,](/azure/azure-signalr/signalr-overview) która multipleksuje połączenia z klientów do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-246">Proxy WebSocket connections to an app through the use of a proxy, such as the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) that multiplexes connections from clients to an app.</span></span> <span data-ttu-id="9b6f5-247">Zapewnia to aplikacji o większej pojemności połączenia niż jeden klient może ustanowić, uniemożliwiając klientowi wyczerpanie połączeń z serwerem.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-247">This provides an app with greater connection capacity than a single client can establish, preventing a client from exhausting the connections to the server.</span></span>
  * <span data-ttu-id="9b6f5-248">Na poziomie serwera: użyj serwera proxy/bramy przed aplikacją.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-248">At the server level: Use a proxy/gateway in front of the app.</span></span> <span data-ttu-id="9b6f5-249">Na przykład [usługi Azure Front Door](/azure/frontdoor/front-door-overview) umożliwia definiowanie, zarządzanie i monitorowanie globalnego routingu ruchu internetowego do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-249">For example, [Azure Front Door](/azure/frontdoor/front-door-overview) enables you to define, manage, and monitor the global routing of web traffic to an app.</span></span>

## <a name="denial-of-service-dos-attacks"></a><span data-ttu-id="9b6f5-250">Ataki typu "odmowa usługi" (DoS)</span><span class="sxs-lookup"><span data-stu-id="9b6f5-250">Denial of service (DoS) attacks</span></span>

<span data-ttu-id="9b6f5-251">Ataki typu "odmowa usługi" (DoS) obejmują klienta, co powoduje, że serwer wyczerpuje jeden lub więcej zasobów, dzięki czemu aplikacja jest niedostępna.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-251">Denial of service (DoS) attacks involve a client causing the server to exhaust one or more of its resources making the app unavailable.</span></span> <span data-ttu-id="9b6f5-252">Aplikacje Blazor Server zawierają pewne domyślne limity i opierają się na innych limitach ASP.NET Core i SignalR, aby chronić przed atakami DoS:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-252">Blazor Server apps include some default limits and rely on other ASP.NET Core and SignalR limits to protect against DoS attacks:</span></span>

| <span data-ttu-id="9b6f5-253">Limit aplikacji Blazor Server</span><span class="sxs-lookup"><span data-stu-id="9b6f5-253">Blazor Server app limit</span></span>                            | <span data-ttu-id="9b6f5-254">Opis</span><span class="sxs-lookup"><span data-stu-id="9b6f5-254">Description</span></span> | <span data-ttu-id="9b6f5-255">Domyślne</span><span class="sxs-lookup"><span data-stu-id="9b6f5-255">Default</span></span> |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | <span data-ttu-id="9b6f5-256">Maksymalna liczba rozłączonych obwodów, które dany serwer przechowuje w pamięci naraz.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-256">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> | <span data-ttu-id="9b6f5-257">100</span><span class="sxs-lookup"><span data-stu-id="9b6f5-257">100</span></span> |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | <span data-ttu-id="9b6f5-258">Maksymalny czas, przez który odłączony obwód jest przechowywany w pamięci przed rozerwaniem.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-258">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> | <span data-ttu-id="9b6f5-259">3 minuty</span><span class="sxs-lookup"><span data-stu-id="9b6f5-259">3 minutes</span></span> |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | <span data-ttu-id="9b6f5-260">Maksymalny czas oczekiwania serwera przed wytyczeniem czasu wywołania funkcji asynchronicznego języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-260">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> | <span data-ttu-id="9b6f5-261">1 min</span><span class="sxs-lookup"><span data-stu-id="9b6f5-261">1 minute</span></span> |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | <span data-ttu-id="9b6f5-262">Maksymalna liczba nieuznanych partii renderowania, które serwer przechowuje w pamięci na obwód w danym czasie, aby obsługiwać niezawodne ponowne połączenie.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-262">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="9b6f5-263">Po osiągnięciu limitu serwer przestaje produkować nowe partie renderowania, dopóki klient nie potwierdzi co najmniej jednej partii.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-263">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> | <span data-ttu-id="9b6f5-264">10</span><span class="sxs-lookup"><span data-stu-id="9b6f5-264">10</span></span> |


| <span data-ttu-id="9b6f5-265">Limit signalr i ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b6f5-265">SignalR and ASP.NET Core limit</span></span>             | <span data-ttu-id="9b6f5-266">Opis</span><span class="sxs-lookup"><span data-stu-id="9b6f5-266">Description</span></span> | <span data-ttu-id="9b6f5-267">Domyślne</span><span class="sxs-lookup"><span data-stu-id="9b6f5-267">Default</span></span> |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | <span data-ttu-id="9b6f5-268">Rozmiar wiadomości dla pojedynczej wiadomości.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-268">Message size for an individual message.</span></span> | <span data-ttu-id="9b6f5-269">32 KB</span><span class="sxs-lookup"><span data-stu-id="9b6f5-269">32 KB</span></span> |

## <a name="interactions-with-the-browser-client"></a><span data-ttu-id="9b6f5-270">Interakcje z przeglądarką (klientem)</span><span class="sxs-lookup"><span data-stu-id="9b6f5-270">Interactions with the browser (client)</span></span>

<span data-ttu-id="9b6f5-271">Klient wchodzi w interakcję z serwerem za pośrednictwem wywoływania zdarzeń interop JS i ukończenia renderowania.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-271">A client interacts with the server through JS interop event dispatching and render completion.</span></span> <span data-ttu-id="9b6f5-272">Komunikacja interop JS idzie w obie strony między JavaScript i .NET:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-272">JS interop communication goes both ways between JavaScript and .NET:</span></span>

* <span data-ttu-id="9b6f5-273">Zdarzenia przeglądarki są wywoływane z klienta do serwera w sposób asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-273">Browser events are dispatched from the client to the server in an asynchronous fashion.</span></span>
* <span data-ttu-id="9b6f5-274">Serwer odpowiada asynchronicznie rerendering interfejsu użytkownika w razie potrzeby.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-274">The server responds asynchronously rerendering the UI as necessary.</span></span>

### <a name="javascript-functions-invoked-from-net"></a><span data-ttu-id="9b6f5-275">Funkcje JavaScript wywoływane z platformy .NET</span><span class="sxs-lookup"><span data-stu-id="9b6f5-275">JavaScript functions invoked from .NET</span></span>

<span data-ttu-id="9b6f5-276">W przypadku połączeń z metod platformy .NET do języka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-276">For calls from .NET methods to JavaScript:</span></span>

* <span data-ttu-id="9b6f5-277">Wszystkie wywołania mają konfigurowalny limit czasu, po którym <xref:System.OperationCanceledException> nie powiedzie się, zwracając do wywołującego.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-277">All invocations have a configurable timeout after which they fail, returning a <xref:System.OperationCanceledException> to the caller.</span></span>
  * <span data-ttu-id="9b6f5-278">Istnieje domyślny limit czasu dla`CircuitOptions.JSInteropDefaultCallTimeout`połączeń ( ) jednej minuty.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-278">There's a default timeout for the calls (`CircuitOptions.JSInteropDefaultCallTimeout`) of one minute.</span></span> <span data-ttu-id="9b6f5-279">Aby skonfigurować ten <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>limit, zobacz .</span><span class="sxs-lookup"><span data-stu-id="9b6f5-279">To configure this limit, see <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.</span></span>
  * <span data-ttu-id="9b6f5-280">Token anulowania można podać, aby kontrolować anulowanie na podstawie połączenia.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-280">A cancellation token can be provided to control the cancellation on a per-call basis.</span></span> <span data-ttu-id="9b6f5-281">Polegaj na domyślnym limitie czasu wywołania, jeśli jest to możliwe, i czasochłomnie dowolne wywołanie klienta, jeśli zostanie podany token anulowania.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-281">Rely on the default call timeout where possible and time-bound any call to the client if a cancellation token is provided.</span></span>
* <span data-ttu-id="9b6f5-282">Nie można ufać wynikowi połączenia JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-282">The result of a JavaScript call can't be trusted.</span></span> <span data-ttu-id="9b6f5-283">Klient Blazor aplikacji uruchomiony w przeglądarce wyszukuje funkcję JavaScript do wywołania.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-283">The Blazor app client running in the browser searches for the JavaScript function to invoke.</span></span> <span data-ttu-id="9b6f5-284">Funkcja jest wywoływana i wynik lub błąd jest produkowany.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-284">The function is invoked, and either the result or an error is produced.</span></span> <span data-ttu-id="9b6f5-285">Złośliwy klient może próbować:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-285">A malicious client can attempt to:</span></span>
  * <span data-ttu-id="9b6f5-286">Przyczyną problemu w aplikacji jest zwracanie błędu z funkcji JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-286">Cause an issue in the app by returning an error from the JavaScript function.</span></span>
  * <span data-ttu-id="9b6f5-287">Wywołaj niezamierzone zachowanie na serwerze, zwracając nieoczekiwany wynik z funkcji JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-287">Induce an unintended behavior on the server by returning an unexpected result from the JavaScript function.</span></span>

<span data-ttu-id="9b6f5-288">Podjąć następujące środki ostrożności, aby chronić się przed poprzednimi scenariuszami:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-288">Take the following precautions to guard against the preceding scenarios:</span></span>

* <span data-ttu-id="9b6f5-289">Zawijanie wywołań interop JS w instrukcjach [try-catch,](/dotnet/csharp/language-reference/keywords/try-catch) aby uwzględnić błędy, które mogą wystąpić podczas wywołań.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-289">Wrap JS interop calls within [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements to account for errors that might occur during the invocations.</span></span> <span data-ttu-id="9b6f5-290">Aby uzyskać więcej informacji, zobacz <xref:blazor/handle-errors#javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-290">For more information, see <xref:blazor/handle-errors#javascript-interop>.</span></span>
* <span data-ttu-id="9b6f5-291">Sprawdź poprawność danych zwróconych z wywołań interop JS, w tym komunikatów o błędach, przed podjęciem jakiejkolwiek akcji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-291">Validate data returned from JS interop invocations, including error messages, before taking any action.</span></span>

### <a name="net-methods-invoked-from-the-browser"></a><span data-ttu-id="9b6f5-292">Metody .NET wywoływane z przeglądarki</span><span class="sxs-lookup"><span data-stu-id="9b6f5-292">.NET methods invoked from the browser</span></span>

<span data-ttu-id="9b6f5-293">Nie ufaj wywołaniom z języka JavaScript do metod .NET.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-293">Don't trust calls from JavaScript to .NET methods.</span></span> <span data-ttu-id="9b6f5-294">Gdy metoda .NET jest narażona na działanie języka JavaScript, należy wziąć pod uwagę sposób wywoływania metody .NET:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-294">When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span></span>

* <span data-ttu-id="9b6f5-295">Traktuj dowolną metodę platformy .NET narażoną na działanie języka JavaScript, tak jak publiczny punkt końcowy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-295">Treat any .NET method exposed to JavaScript as you would a public endpoint to the app.</span></span>
  * <span data-ttu-id="9b6f5-296">Sprawdź poprawność danych wejściowych.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-296">Validate input.</span></span>
    * <span data-ttu-id="9b6f5-297">Upewnij się, że wartości znajdują się w oczekiwanych zakresach.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-297">Ensure that values are within expected ranges.</span></span>
    * <span data-ttu-id="9b6f5-298">Upewnij się, że użytkownik ma uprawnienia do wykonywania żądanej akcji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-298">Ensure that the user has permission to perform the action requested.</span></span>
  * <span data-ttu-id="9b6f5-299">Nie przydzielaj nadmiernej ilości zasobów w ramach wywołania metody .NET.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-299">Don't allocate an excessive quantity of resources as part of the .NET method invocation.</span></span> <span data-ttu-id="9b6f5-300">Na przykład należy przeprowadzić kontrole i umieścić limity użycia procesora CPU i pamięci.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-300">For example, perform checks and place limits on CPU and memory use.</span></span>
  * <span data-ttu-id="9b6f5-301">Należy wziąć pod uwagę, że metody statyczne i wystąpienia mogą być udostępniane klientom JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-301">Take into account that static and instance methods can be exposed to JavaScript clients.</span></span> <span data-ttu-id="9b6f5-302">Należy unikać udostępniania stanu między sesjami, chyba że projekt wymaga udostępniania stanu z odpowiednimi ograniczeniami.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-302">Avoid sharing state across sessions unless the design calls for sharing state with appropriate constraints.</span></span>
    * <span data-ttu-id="9b6f5-303">Na przykład metody `DotNetReference` udostępniane za pośrednictwem obiektów, które są pierwotnie tworzone za pomocą iniekcji zależności (DI), obiekty powinny być rejestrowane jako obiekty o określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-303">For instance methods exposed through `DotNetReference` objects that are originally created through dependency injection (DI), the objects should be registered as scoped objects.</span></span> <span data-ttu-id="9b6f5-304">Dotyczy to dowolnej usługi DI Blazor używanej przez aplikację Serwer.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-304">This applies to any DI service that the Blazor Server app uses.</span></span>
    * <span data-ttu-id="9b6f5-305">W przypadku metod statycznych należy unikać ustanawiania stanu, który nie może być ograniczony do klienta, chyba że aplikacja jawnie udostępnia stan według projektu wszystkich użytkowników w wystąpieniu serwera.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-305">For static methods, avoid establishing state that can't be scoped to the client unless the app is explicitly sharing state by-design across all users on a server instance.</span></span>
  * <span data-ttu-id="9b6f5-306">Unikaj przekazywania danych dostarczonych przez użytkownika w parametrach do wywołań JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-306">Avoid passing user-supplied data in parameters to JavaScript calls.</span></span> <span data-ttu-id="9b6f5-307">Jeśli przekazywanie danych w parametrach jest absolutnie wymagane, upewnij się, że kod JavaScript obsługuje przekazywanie danych bez wprowadzania luk w zabezpieczeniach [skryptów krzyżowych (XSS).](#cross-site-scripting-xss)</span><span class="sxs-lookup"><span data-stu-id="9b6f5-307">If passing data in parameters is absolutely required, ensure that the JavaScript code handles passing the data without introducing [Cross-site scripting (XSS)](#cross-site-scripting-xss) vulnerabilities.</span></span> <span data-ttu-id="9b6f5-308">Na przykład nie zapisuj danych dostarczonych przez użytkownika do modelu `innerHTML` obiektu dokumentu (DOM), ustawiając właściwość elementu.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-308">For example, don't write user-supplied data to the Document Object Model (DOM) by setting the `innerHTML` property of an element.</span></span> <span data-ttu-id="9b6f5-309">Należy rozważyć użycie zasad zabezpieczeń zawartości `eval` [(CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) do wyłączenia i innych niebezpiecznych egoików JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-309">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to disable `eval` and other unsafe JavaScript primitives.</span></span>
* <span data-ttu-id="9b6f5-310">Należy unikać implementacji niestandardowego wysyłania wywołań platformy .NET na podstawie implementacji wysyłki struktury.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-310">Avoid implementing custom dispatching of .NET invocations on top of the framework's dispatching implementation.</span></span> <span data-ttu-id="9b6f5-311">Uwidacznianie metod platformy .NET w przeglądarce jest Blazor scenariuszem zaawansowanym, który nie jest zalecany do ogólnego rozwoju.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-311">Exposing .NET methods to the browser is an advanced scenario, not recommended for general Blazor development.</span></span>

### <a name="events"></a><span data-ttu-id="9b6f5-312">Zdarzenia</span><span class="sxs-lookup"><span data-stu-id="9b6f5-312">Events</span></span>

<span data-ttu-id="9b6f5-313">Zdarzenia zapewniają punkt wejścia Blazor do aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-313">Events provide an entry point to a Blazor Server app.</span></span> <span data-ttu-id="9b6f5-314">Te same reguły dotyczące ochrony punktów końcowych w Blazor aplikacjach sieci web mają zastosowanie do obsługi zdarzeń w aplikacjach serwera.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-314">The same rules for safeguarding endpoints in web apps apply to event handling in Blazor Server apps.</span></span> <span data-ttu-id="9b6f5-315">Złośliwy klient może wysłać dowolne dane, które chce wysłać jako ładunek zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-315">A malicious client can send any data it wishes to send as the payload for an event.</span></span>

<span data-ttu-id="9b6f5-316">Przykład:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-316">For example:</span></span>

* <span data-ttu-id="9b6f5-317">Zdarzenie zmiany dla `<select>` może wysłać wartość, która nie znajduje się w ramach opcji, które aplikacja przedstawiona klientowi.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-317">A change event for a `<select>` could send a value that isn't within the options that the app presented to the client.</span></span>
* <span data-ttu-id="9b6f5-318">Może `<input>` wysłać dowolne dane tekstowe do serwera, z pominięciem sprawdzania poprawności po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-318">An `<input>` could send any text data to the server, bypassing client-side validation.</span></span>

<span data-ttu-id="9b6f5-319">Aplikacja musi sprawdzić poprawność danych dla każdego zdarzenia, które obsługuje aplikacja.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-319">The app must validate the data for any event that the app handles.</span></span> <span data-ttu-id="9b6f5-320">Składniki Blazor [formularzy](xref:blazor/forms-validation) struktury wykonać podstawowe weryfikacje.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-320">The Blazor framework [forms components](xref:blazor/forms-validation) perform basic validations.</span></span> <span data-ttu-id="9b6f5-321">Jeśli aplikacja używa składników formularzy niestandardowych, kod niestandardowy musi być zapisany w celu sprawdzenia poprawności danych zdarzeń, stosownie do potrzeb.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-321">If the app uses custom forms components, custom code must be written to validate event data as appropriate.</span></span>

Blazor<span data-ttu-id="9b6f5-322">Zdarzenia serwera są asynchroniczne, więc wiele zdarzeń można wysłać do serwera, zanim aplikacja ma czas na reakcję, tworząc nowy render.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-322"> Server events are asynchronous, so multiple events can be dispatched to the server before the app has time to react by producing a new render.</span></span> <span data-ttu-id="9b6f5-323">Ma to pewne implikacje dla bezpieczeństwa do rozważenia.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-323">This has some security implications to consider.</span></span> <span data-ttu-id="9b6f5-324">Ograniczanie akcji klienta w aplikacji musi być wykonywane wewnątrz obsługi zdarzeń i nie zależy od bieżącego stanu renderowanego widoku.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-324">Limiting client actions in the app must be performed inside event handlers and not depend on the current rendered view state.</span></span>

<span data-ttu-id="9b6f5-325">Należy wziąć pod uwagę składnik licznika, który powinien umożliwić użytkownikowi przyrost licznika maksymalnie trzy razy.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-325">Consider a counter component that should allow a user to increment a counter a maximum of three times.</span></span> <span data-ttu-id="9b6f5-326">Przycisk, aby przyrost licznika jest warunkowo oparty `count`na wartości :</span><span class="sxs-lookup"><span data-stu-id="9b6f5-326">The button to increment the counter is conditionally based on the value of `count`:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

<span data-ttu-id="9b6f5-327">Klient może wysyłć co najmniej jedno zdarzenia przyrostowe, zanim struktura tworzy nowy render tego składnika.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-327">A client can dispatch one or more increment events before the framework produces a new render of this component.</span></span> <span data-ttu-id="9b6f5-328">Wynik jest, `count` że może być zwiększane *przez trzy razy* przez użytkownika, ponieważ przycisk nie jest usuwany przez interfejs użytkownika wystarczająco szybko.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-328">The result is that the `count` can be incremented *over three times* by the user because the button isn't removed by the UI quickly enough.</span></span> <span data-ttu-id="9b6f5-329">Prawidłowy sposób osiągnięcia limitu `count` trzech przyrostów jest pokazany w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-329">The correct way to achieve the limit of three `count` increments is shown in the following example:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

<span data-ttu-id="9b6f5-330">Dodając `if (count < 3) { ... }` check wewnątrz programu obsługi, decyzja o `count` przyrost jest oparta na bieżącym stanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-330">By adding the `if (count < 3) { ... }` check inside the handler, the decision to increment `count` is based on the current app state.</span></span> <span data-ttu-id="9b6f5-331">Decyzja nie jest oparta na stanie interfejsu użytkownika, jak to było w poprzednim przykładzie, które mogą być tymczasowo przestarzałe.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-331">The decision isn't based on the state of the UI as it was in the previous example, which might be temporarily stale.</span></span>

### <a name="guard-against-multiple-dispatches"></a><span data-ttu-id="9b6f5-332">Ochrona przed wieloma wysyłkami</span><span class="sxs-lookup"><span data-stu-id="9b6f5-332">Guard against multiple dispatches</span></span>

<span data-ttu-id="9b6f5-333">Jeśli wywołanie zwrotne zdarzenia wywołuje długotrwałą operację asynchronicznie, taką jak pobieranie danych z zewnętrznej usługi lub bazy danych, należy rozważyć użycie warty.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-333">If an event callback invokes a long running operation asynchronously, such as fetching data from an external service or database, consider using a guard.</span></span> <span data-ttu-id="9b6f5-334">Osłona może uniemożliwić użytkownikowi kolejkowanie wielu operacji, gdy operacja jest w toku z wizualną informacją zwrotną.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-334">The guard can prevent the user from queueing up multiple operations while the operation is in progress with visual feedback.</span></span> <span data-ttu-id="9b6f5-335">Poniższy kod `isLoading` składnika ustawia się `true` podczas `GetForecastAsync` uzyskiwania danych z serwera.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-335">The following component code sets `isLoading` to `true` while `GetForecastAsync` obtains data from the server.</span></span> <span data-ttu-id="9b6f5-336">While `isLoading` `true`is , przycisk jest wyłączony w interfejsie użytkownika:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-336">While `isLoading` is `true`, the button is disabled in the UI:</span></span>

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

<span data-ttu-id="9b6f5-337">Wzorzec osłony zademonstrowany w poprzednim przykładzie działa, jeśli operacja w `async` - `await` tle jest wykonywana asynchronicznie ze wzorcem.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-337">The guard pattern demonstrated in the preceding example works if the background operation is executed asynchronously with the `async`-`await` pattern.</span></span>

### <a name="cancel-early-and-avoid-use-after-dispose"></a><span data-ttu-id="9b6f5-338">Anuluj wcześniej i unikaj używania po utylizacji</span><span class="sxs-lookup"><span data-stu-id="9b6f5-338">Cancel early and avoid use-after-dispose</span></span>

<span data-ttu-id="9b6f5-339">Oprócz użycia osłony, jak opisano w sekcji Ochrona przed wieloma <xref:System.Threading.CancellationToken> [wysyłkami,](#guard-against-multiple-dispatches) należy rozważyć użycie a, aby anulować długotrwałe operacje, gdy komponent jest usuwany.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-339">In addition to using a guard as described in the [Guard against multiple dispatches](#guard-against-multiple-dispatches) section, consider using a <xref:System.Threading.CancellationToken> to cancel long-running operations when the component is disposed.</span></span> <span data-ttu-id="9b6f5-340">Takie podejście ma dodatkową zaletę, aby uniknąć *użycia po zużyciu* w komponentach:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-340">This approach has the added benefit of avoiding *use-after-dispose* in components:</span></span>

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a><span data-ttu-id="9b6f5-341">Unikaj zdarzeń, które generują duże ilości danych</span><span class="sxs-lookup"><span data-stu-id="9b6f5-341">Avoid events that produce large amounts of data</span></span>

<span data-ttu-id="9b6f5-342">Niektóre zdarzenia DOM, `oninput` `onscroll`takie jak lub , mogą powodować dużą ilość danych.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-342">Some DOM events, such as `oninput` or `onscroll`, can produce a large amount of data.</span></span> <span data-ttu-id="9b6f5-343">Unikaj używania Blazor tych zdarzeń w aplikacjach serwera.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-343">Avoid using these events in Blazor server apps.</span></span>

## <a name="additional-security-guidance"></a><span data-ttu-id="9b6f5-344">Dodatkowe wskazówki dotyczące zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="9b6f5-344">Additional security guidance</span></span>

<span data-ttu-id="9b6f5-345">Wskazówki dotyczące zabezpieczania aplikacji ASP.NET Blazor Core mają zastosowanie do aplikacji serwera i są omówione w następujących sekcjach:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-345">The guidance for securing ASP.NET Core apps apply to Blazor Server apps and are covered in the following sections:</span></span>

* [<span data-ttu-id="9b6f5-346">Rejestrowanie i dane poufne</span><span class="sxs-lookup"><span data-stu-id="9b6f5-346">Logging and sensitive data</span></span>](#logging-and-sensitive-data)
* [<span data-ttu-id="9b6f5-347">Ochrona informacji podczas przesyłania za pomocą protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="9b6f5-347">Protect information in transit with HTTPS</span></span>](#protect-information-in-transit-with-https)
* <span data-ttu-id="9b6f5-348">[Skrypty międzyuczełowe (XSS)](#cross-site-scripting-xss))</span><span class="sxs-lookup"><span data-stu-id="9b6f5-348">[Cross-site scripting (XSS)](#cross-site-scripting-xss))</span></span>
* [<span data-ttu-id="9b6f5-349">Ochrona przed krzyżowym pochodzeniem</span><span class="sxs-lookup"><span data-stu-id="9b6f5-349">Cross-origin protection</span></span>](#cross-origin-protection)
* [<span data-ttu-id="9b6f5-350">Zatrzaskiwanie</span><span class="sxs-lookup"><span data-stu-id="9b6f5-350">Click-jacking</span></span>](#click-jacking)
* [<span data-ttu-id="9b6f5-351">Otwarte przekierowania</span><span class="sxs-lookup"><span data-stu-id="9b6f5-351">Open redirects</span></span>](#open-redirects)

### <a name="logging-and-sensitive-data"></a><span data-ttu-id="9b6f5-352">Rejestrowanie i dane poufne</span><span class="sxs-lookup"><span data-stu-id="9b6f5-352">Logging and sensitive data</span></span>

<span data-ttu-id="9b6f5-353">Interakcje międzyoperacyjne JS między klientem a serwerem są <xref:Microsoft.Extensions.Logging.ILogger> rejestrowane w dziennikach serwera z wystąpieniami.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-353">JS interop interactions between the client and server are recorded in the server's logs with <xref:Microsoft.Extensions.Logging.ILogger> instances.</span></span> Blazor<span data-ttu-id="9b6f5-354">pozwala uniknąć rejestrowania poufnych informacji, takich jak rzeczywiste zdarzenia lub wejścia i wyjścia interop JS.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-354"> avoids logging sensitive information, such as actual events or JS interop inputs and outputs.</span></span>

<span data-ttu-id="9b6f5-355">Gdy wystąpi błąd na serwerze, struktura powiadamia klienta i rozdziera sesję.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-355">When an error occurs on the server, the framework notifies the client and tears down the session.</span></span> <span data-ttu-id="9b6f5-356">Domyślnie klient otrzymuje ogólny komunikat o błędzie, który można zobaczyć w narzędziach programistycznych przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-356">By default, the client receives a generic error message that can be seen in the browser's developer tools.</span></span>

<span data-ttu-id="9b6f5-357">Błąd po stronie klienta nie zawiera callstack i nie zawiera szczegółów na temat przyczyny błędu, ale dzienniki serwera zawierają takie informacje.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-357">The client-side error doesn't include the callstack and doesn't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="9b6f5-358">Do celów programisty, poufne informacje o błędach mogą być udostępniane klientowi, włączając szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-358">For development purposes, sensitive error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="9b6f5-359">Włącz szczegółowe błędy za pomocą:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-359">Enable detailed errors with:</span></span>

* <span data-ttu-id="9b6f5-360">`CircuitOptions.DetailedErrors`.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-360">`CircuitOptions.DetailedErrors`.</span></span>
* <span data-ttu-id="9b6f5-361">`DetailedErrors`konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-361">`DetailedErrors` configuration key.</span></span> <span data-ttu-id="9b6f5-362">Na przykład ustaw `ASPNETCORE_DETAILEDERRORS` zmienną środowiskową `true`na wartość .</span><span class="sxs-lookup"><span data-stu-id="9b6f5-362">For example, set the `ASPNETCORE_DETAILEDERRORS` environment variable to a value of `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="9b6f5-363">Udostępnianie informacji o błędzie klientom w Internecie jest zagrożeniem bezpieczeństwa, których należy zawsze unikać.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-363">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

### <a name="protect-information-in-transit-with-https"></a><span data-ttu-id="9b6f5-364">Ochrona informacji podczas przesyłania za pomocą protokołu HTTPS</span><span class="sxs-lookup"><span data-stu-id="9b6f5-364">Protect information in transit with HTTPS</span></span>

Blazor<span data-ttu-id="9b6f5-365">Serwer używa SignalR do komunikacji między klientem a serwerem.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-365"> Server uses SignalR for communication between the client and the server.</span></span> Blazor<span data-ttu-id="9b6f5-366">Serwer zwykle używa transportu, SignalR który negocjuje, który jest zazwyczaj WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-366"> Server normally uses the transport that SignalR negotiates, which is typically WebSockets.</span></span>

Blazor<span data-ttu-id="9b6f5-367">Serwer nie zapewnia integralności i poufności danych wysyłanych między serwerem a klientem.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-367"> Server doesn't ensure the integrity and confidentiality of the data sent between the server and the client.</span></span> <span data-ttu-id="9b6f5-368">Zawsze używaj protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-368">Always use HTTPS.</span></span>

### <a name="cross-site-scripting-xss"></a><span data-ttu-id="9b6f5-369">Skrypty międzyuczełowe (XSS)</span><span class="sxs-lookup"><span data-stu-id="9b6f5-369">Cross-site scripting (XSS)</span></span>

<span data-ttu-id="9b6f5-370">Skrypty między witrynami (XSS) umożliwiają nieautoryzowanej stronie wykonywanie dowolnej logiki w kontekście przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-370">Cross-site scripting (XSS) allows an unauthorized party to execute arbitrary logic in the context of the browser.</span></span> <span data-ttu-id="9b6f5-371">Rozwiązana aplikacja może potencjalnie uruchomić dowolny kod na kliencie.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-371">A compromised app could potentially run arbitrary code on the client.</span></span> <span data-ttu-id="9b6f5-372">Luka może zostać wykorzystana do potencjalnego wykonania szeregu złośliwych działań przeciwko serwerowi:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-372">The vulnerability could be used to potentially perform a number of malicious actions against the server:</span></span>

* <span data-ttu-id="9b6f5-373">Wysyłanie fałszywych/nieprawidłowych zdarzeń do serwera.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-373">Dispatch fake/invalid events to the server.</span></span>
* <span data-ttu-id="9b6f5-374">Wysyłka nie powiodło się/nieprawidłowe zakończenia renderowania.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-374">Dispatch fail/invalid render completions.</span></span>
* <span data-ttu-id="9b6f5-375">Należy unikać wysyłania uzupełnień renderowania.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-375">Avoid dispatching render completions.</span></span>
* <span data-ttu-id="9b6f5-376">Wysyłanie wywołań międzyoperacyjnych z języka JavaScript do platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-376">Dispatch interop calls from JavaScript to .NET.</span></span>
* <span data-ttu-id="9b6f5-377">Zmodyfikuj odpowiedź wywołań interop z platformy .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-377">Modify the response of interop calls from .NET to JavaScript.</span></span>
* <span data-ttu-id="9b6f5-378">Należy unikać wysyłania .NET do JS interop wyników.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-378">Avoid dispatching .NET to JS interop results.</span></span>

<span data-ttu-id="9b6f5-379">Struktura Blazor serwera podejmuje kroki w celu ochrony przed niektórymi z powyższych zagrożeń:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-379">The Blazor Server framework takes steps to protect against some of the preceding threats:</span></span>

* <span data-ttu-id="9b6f5-380">Zatrzymuje tworzenie nowych aktualizacji interfejsu użytkownika, jeśli klient nie potwierdza partii renderowania.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-380">Stops producing new UI updates if the client isn't acknowledging render batches.</span></span> <span data-ttu-id="9b6f5-381">Skonfigurowano `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`z programem .</span><span class="sxs-lookup"><span data-stu-id="9b6f5-381">Configured with `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.</span></span>
* <span data-ttu-id="9b6f5-382">Po minucie nie pobiera żadnych połączeń z platformy .NET do JavaScript bez odbierania odpowiedzi od klienta.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-382">Times out any .NET to JavaScript call after one minute without receiving a response from the client.</span></span> <span data-ttu-id="9b6f5-383">Skonfigurowano `CircuitOptions.JSInteropDefaultCallTimeout`z programem .</span><span class="sxs-lookup"><span data-stu-id="9b6f5-383">Configured with `CircuitOptions.JSInteropDefaultCallTimeout`.</span></span>
* <span data-ttu-id="9b6f5-384">Wykonuje podstawową walidację wszystkich danych wejściowych pochodzących z przeglądarki podczas js interop:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-384">Performs basic validation on all input coming from the browser during JS interop:</span></span>
  * <span data-ttu-id="9b6f5-385">Odwołania .NET są prawidłowe i typu oczekiwanego przez metodę .NET.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-385">.NET references are valid and of the type expected by the .NET method.</span></span>
  * <span data-ttu-id="9b6f5-386">Dane nie są zniekształcone.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-386">The data isn't malformed.</span></span>
  * <span data-ttu-id="9b6f5-387">W ładunku znajduje się prawidłowa liczba argumentów dla metody.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-387">The correct number of arguments for the method are present in the payload.</span></span>
  * <span data-ttu-id="9b6f5-388">Argumenty lub wynik można deserializacji poprawnie przed wywołaniem metody.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-388">The arguments or result can be deserialized correctly before invoking the method.</span></span>
* <span data-ttu-id="9b6f5-389">Wykonuje podstawowe sprawdzanie poprawności we wszystkich danych wejściowych pochodzących z przeglądarki z wysłanych zdarzeń:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-389">Performs basic validation in all input coming from the browser from dispatched events:</span></span>
  * <span data-ttu-id="9b6f5-390">Zdarzenie ma prawidłowy typ.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-390">The event has a valid type.</span></span>
  * <span data-ttu-id="9b6f5-391">Dane dla zdarzenia mogą być deserializowane.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-391">The data for the event can be deserialized.</span></span>
  * <span data-ttu-id="9b6f5-392">Istnieje program obsługi zdarzeń skojarzony ze zdarzeniem.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-392">There's an event handler associated with the event.</span></span>

<span data-ttu-id="9b6f5-393">Oprócz zabezpieczeń, które implementuje ramach, aplikacja musi być kodowane przez dewelopera w celu ochrony przed zagrożeniami i podjąć odpowiednie działania:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-393">In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span></span>

* <span data-ttu-id="9b6f5-394">Zawsze sprawdzaj poprawność danych podczas obsługi zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-394">Always validate data when handling events.</span></span>
* <span data-ttu-id="9b6f5-395">Podejmij odpowiednie działania po otrzymaniu nieprawidłowych danych:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-395">Take appropriate action upon receiving invalid data:</span></span>
  * <span data-ttu-id="9b6f5-396">Zignoruj dane i wróć.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-396">Ignore the data and return.</span></span> <span data-ttu-id="9b6f5-397">Dzięki temu aplikacja może kontynuować przetwarzanie żądań.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-397">This allows the app to continue processing requests.</span></span>
  * <span data-ttu-id="9b6f5-398">Jeśli aplikacja stwierdzi, że dane wejściowe są bezprawne i nie mogą być produkowane przez legalnego klienta, zgłoś wyjątek.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-398">If the app determines that the input is illegitimate and couldn't be produced by legitimate client, throw an exception.</span></span> <span data-ttu-id="9b6f5-399">Zgłaszanie wyjątku rozrywa obwód i kończy sesję.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-399">Throwing an exception tears down the circuit and ends the session.</span></span>
* <span data-ttu-id="9b6f5-400">Nie ufaj komunikatowi o błędzie dostarczonej przez ukończenia partii renderowania zawarte w dziennikach.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-400">Don't trust the error message provided by render batch completions included in the logs.</span></span> <span data-ttu-id="9b6f5-401">Błąd jest dostarczany przez klienta i zazwyczaj nie można ufać, ponieważ klient może zostać naruszony.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-401">The error is provided by the client and can't generally be trusted, as the client might be compromised.</span></span>
* <span data-ttu-id="9b6f5-402">Nie ufaj wejściom na js interop wywołania w obu kierunkach między JavaScript i .NET metody.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-402">Don't trust the input on JS interop calls in either direction between JavaScript and .NET methods.</span></span>
* <span data-ttu-id="9b6f5-403">Aplikacja jest odpowiedzialna za sprawdzanie poprawności, że zawartość argumentów i wyników są prawidłowe, nawet jeśli argumenty lub wyniki są poprawnie deserializowane.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-403">The app is responsible for validating that the content of arguments and results are valid, even if the arguments or results are correctly deserialized.</span></span>

<span data-ttu-id="9b6f5-404">Aby luka w zabezpieczeniach XSS istniała, aplikacja musi zawierać dane wejściowe użytkownika na renderowanej stronie.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-404">For a XSS vulnerability to exist, the app must incorporate user input in the rendered page.</span></span> Blazor<span data-ttu-id="9b6f5-405">Składniki serwera wykonać krok w czasie kompilacji, gdzie znaczników w pliku *.brzytwa* jest przekształcany w proceduralnych logiki C#.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-405"> Server components execute a compile-time step where the markup in a *.razor* file is transformed into procedural C# logic.</span></span> <span data-ttu-id="9b6f5-406">W czasie wykonywania logiki języka C# tworzy *drzewo renderowania* opisujące elementy, tekst i składniki podrzędne.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-406">At runtime, the C# logic builds a *render tree* describing the elements, text, and child components.</span></span> <span data-ttu-id="9b6f5-407">Jest to stosowane do modelu DOM przeglądarki za pomocą sekwencji instrukcji JavaScript (lub jest serializowane do HTML w przypadku prerenderingu):</span><span class="sxs-lookup"><span data-stu-id="9b6f5-407">This is applied to the browser's DOM via a sequence of JavaScript instructions (or is serialized to HTML in the case of prerendering):</span></span>

* <span data-ttu-id="9b6f5-408">Dane wejściowe użytkownika renderowane za pomocą normalnej składni Razor (na przykład) nie ujawniają luki w zabezpieczeniach XSS, ponieważ składnia Razor jest dodawana do DOM za pomocą poleceń, `@someStringValue`które mogą pisać tylko tekst.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-408">User input rendered via normal Razor syntax (for example, `@someStringValue`) doesn't expose a XSS vulnerability because the Razor syntax is added to the DOM via commands that can only write text.</span></span> <span data-ttu-id="9b6f5-409">Nawet jeśli wartość zawiera znaczniki HTML, wartość jest wyświetlana jako tekst statyczny.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-409">Even if the value includes HTML markup, the value is displayed as static text.</span></span> <span data-ttu-id="9b6f5-410">Podczas prerendering, dane wyjściowe jest zakodowany w formacie HTML, który również wyświetla zawartość jako tekst statyczny.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-410">When prerendering, the output is HTML-encoded, which also displays the content as static text.</span></span>
* <span data-ttu-id="9b6f5-411">Tagi skryptów nie są dozwolone i nie powinny być uwzględniane w drzewie renderowania składników aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-411">Script tags aren't allowed and shouldn't be included in the app's component render tree.</span></span> <span data-ttu-id="9b6f5-412">Jeśli znacznik skryptu znajduje się w znacznikach składnika, generowany jest błąd w czasie kompilacji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-412">If a script tag is included in a component's markup, a compile-time error is generated.</span></span>
* <span data-ttu-id="9b6f5-413">Autorzy składników mogą tworzyć składniki w języku C# bez użycia Razor.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-413">Component authors can author components in C# without using Razor.</span></span> <span data-ttu-id="9b6f5-414">Autor składnika jest odpowiedzialny za używanie poprawnych interfejsów API podczas emitowania danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-414">The component author is responsible for using the correct APIs when emitting output.</span></span> <span data-ttu-id="9b6f5-415">Na przykład `builder.AddContent(0, someUserSuppliedString)` użyj, a *nie* `builder.AddMarkupContent(0, someUserSuppliedString)`, ponieważ ta ostatnia może utworzyć lukę W ZABEZPIECZENIACH XSS.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-415">For example, use `builder.AddContent(0, someUserSuppliedString)` and *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, as the latter could create a XSS vulnerability.</span></span>

<span data-ttu-id="9b6f5-416">W ramach ochrony przed atakami XSS należy rozważyć wdrożenie środków ograniczających zagrożenie XSS, takich jak [zasady zabezpieczeń zawartości (CSP).](https://developer.mozilla.org/docs/Web/HTTP/CSP)</span><span class="sxs-lookup"><span data-stu-id="9b6f5-416">As part of protecting against XSS attacks, consider implementing XSS mitigations, such as [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span></span>

<span data-ttu-id="9b6f5-417">Aby uzyskać więcej informacji, zobacz <xref:security/cross-site-scripting>.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-417">For more information, see <xref:security/cross-site-scripting>.</span></span>

### <a name="cross-origin-protection"></a><span data-ttu-id="9b6f5-418">Ochrona przed krzyżowym pochodzeniem</span><span class="sxs-lookup"><span data-stu-id="9b6f5-418">Cross-origin protection</span></span>

<span data-ttu-id="9b6f5-419">Ataki między źródłami obejmują klienta z innego pochodzenia wykonującego akcję przeciwko serwerowi.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-419">Cross-origin attacks involve a client from a different origin performing an action against the server.</span></span> <span data-ttu-id="9b6f5-420">Złośliwa akcja jest zazwyczaj żądanie GET lub post formularza (Cross-Site Request Forgery, CSRF), ale otwarcie złośliwego WebSocket jest również możliwe.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-420">The malicious action is typically a GET request or a form POST (Cross-Site Request Forgery, CSRF), but opening a malicious WebSocket is also possible.</span></span> Blazor<span data-ttu-id="9b6f5-421">Aplikacje serwera oferują [takie SignalR same gwarancje, że każda inna aplikacja korzystająca z protokołu koncentratora oferuje:](xref:signalr/security)</span><span class="sxs-lookup"><span data-stu-id="9b6f5-421"> Server apps offer [the same guarantees that any other SignalR app using the hub protocol offer](xref:signalr/security):</span></span>

* Blazor<span data-ttu-id="9b6f5-422">Aplikacje serwera są dostępne cross-origin, chyba że zostaną podjęte dodatkowe środki, aby temu zapobiec.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-422"> Server apps can be accessed cross-origin unless additional measures are taken to prevent it.</span></span> <span data-ttu-id="9b6f5-423">Aby wyłączyć dostęp między źródłami, wyłącz cors w punkcie końcowym, dodając oprogramowanie pośredniczące CORS do potoku i dodając `DisableCorsAttribute` do metadanych punktu Blazor końcowego lub ogranicz zestaw dozwolonych źródeł, [konfigurując SignalR udostępnianie zasobów między źródłami.](xref:signalr/security#cross-origin-resource-sharing)</span><span class="sxs-lookup"><span data-stu-id="9b6f5-423">To disable cross-origin access, either disable CORS in the endpoint by adding the CORS middleware to the pipeline and adding the `DisableCorsAttribute` to the Blazor endpoint metadata or limit the set of allowed origins by [configuring SignalR for cross-origin resource sharing](xref:signalr/security#cross-origin-resource-sharing).</span></span>
* <span data-ttu-id="9b6f5-424">Jeśli usługa CORS jest włączona, w zależności od konfiguracji cors mogą być wymagane dodatkowe kroki w celu ochrony aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-424">If CORS is enabled, extra steps might be required to protect the app depending on the CORS configuration.</span></span> <span data-ttu-id="9b6f5-425">Jeśli usługa CORS jest globalnie włączona, Blazor można wyłączyć `DisableCorsAttribute` usługę CORS dla centrum Serwer, dodając metadane do metadanych punktu końcowego po wywołaniu `hub.MapBlazorHub()`.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-425">If CORS is globally enabled, CORS can be disabled for the Blazor Server hub by adding the `DisableCorsAttribute` metadata to the endpoint metadata after calling `hub.MapBlazorHub()`.</span></span>

<span data-ttu-id="9b6f5-426">Aby uzyskać więcej informacji, zobacz <xref:security/anti-request-forgery>.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-426">For more information, see <xref:security/anti-request-forgery>.</span></span>

### <a name="click-jacking"></a><span data-ttu-id="9b6f5-427">Zatrzaskiwanie</span><span class="sxs-lookup"><span data-stu-id="9b6f5-427">Click-jacking</span></span>

<span data-ttu-id="9b6f5-428">Kliknięcie polega na renderowaniu witryny `<iframe>` jako witryny z innego pochodzenia, aby skłonić użytkownika do wykonywania działań w zaatakowanej witrynie.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-428">Click-jacking involves rendering a site as an `<iframe>` inside a site from a different origin in order to trick the user into performing actions on the site under attack.</span></span>

<span data-ttu-id="9b6f5-429">Aby chronić aplikację przed renderowaniem `<iframe>`wewnątrz programu , użyj zasady zabezpieczeń zawartości [(CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) i nagłówka. `X-Frame-Options`</span><span class="sxs-lookup"><span data-stu-id="9b6f5-429">To protect an app from rendering inside of an `<iframe>`, use [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) and the `X-Frame-Options` header.</span></span> <span data-ttu-id="9b6f5-430">Aby uzyskać więcej informacji, zobacz [dokumenty internetowe MDN: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span><span class="sxs-lookup"><span data-stu-id="9b6f5-430">For more information, see [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span></span>

### <a name="open-redirects"></a><span data-ttu-id="9b6f5-431">Otwarte przekierowania</span><span class="sxs-lookup"><span data-stu-id="9b6f5-431">Open redirects</span></span>

<span data-ttu-id="9b6f5-432">Po Blazor uruchomieniu sesji aplikacji serwera serwer wykonuje podstawową sprawdzanie poprawności adresów URL wysłanych w ramach uruchamiania sesji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-432">When a Blazor Server app session starts, the server performs basic validation of the URLs sent as part of starting the session.</span></span> <span data-ttu-id="9b6f5-433">Struktura sprawdza, czy podstawowy adres URL jest nadrzędnym bieżącym adresem URL przed ustanowieniem obwodu.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-433">The framework checks that the base URL is a parent of the current URL before establishing the circuit.</span></span> <span data-ttu-id="9b6f5-434">Żadne dodatkowe kontrole nie są wykonywane przez ramy.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-434">No additional checks are performed by the framework.</span></span>

<span data-ttu-id="9b6f5-435">Gdy użytkownik wybierze łącze na kliencie, adres URL łącza jest wysyłany do serwera, który określa, jakie działania należy podjąć.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-435">When a user selects a link on the client, the URL for the link is sent to the server, which determines what action to take.</span></span> <span data-ttu-id="9b6f5-436">Na przykład aplikacja może wykonać nawigację po stronie klienta lub wskazać w przeglądarce, aby przejść do nowej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-436">For example, the app may perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="9b6f5-437">Składniki mogą również programowo wyzwalać żądania `NavigationManager`nawigacji za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="9b6f5-437">Components can also trigger navigation requests programatically through the use of `NavigationManager`.</span></span> <span data-ttu-id="9b6f5-438">W takich scenariuszach aplikacja może wykonać nawigację po stronie klienta lub wskazać w przeglądarce, aby przejść do nowej lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-438">In such scenarios, the app might perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="9b6f5-439">Składniki muszą:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-439">Components must:</span></span>

* <span data-ttu-id="9b6f5-440">Należy unikać używania danych wejściowych użytkownika jako część argumentów wywołania nawigacji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-440">Avoid using user input as part of the navigation call arguments.</span></span>
* <span data-ttu-id="9b6f5-441">Sprawdź poprawność argumentów, aby upewnić się, że obiekt docelowy jest dozwolony przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-441">Validate arguments to ensure that the target is allowed by the app.</span></span>

<span data-ttu-id="9b6f5-442">W przeciwnym razie złośliwy użytkownik może wymusić na przeglądarce, aby przejść do witryny kontrolowanej przez osobę atakującą.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-442">Otherwise, a malicious user can force the browser to go to an attacker-controlled site.</span></span> <span data-ttu-id="9b6f5-443">W tym scenariuszu osoba atakująca sztuczki aplikacji do korzystania z niektórych danych wejściowych użytkownika w ramach wywołania `NavigationManager.Navigate` metody.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-443">In this scenario, the attacker tricks the app into using some user input as part of the invocation of the `NavigationManager.Navigate` method.</span></span>

<span data-ttu-id="9b6f5-444">Ta rada ma również zastosowanie podczas renderowania linków w ramach aplikacji:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-444">This advice also applies when rendering links as part of the app:</span></span>

* <span data-ttu-id="9b6f5-445">Jeśli to możliwe, użyj linków względnych.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-445">If possible, use relative links.</span></span>
* <span data-ttu-id="9b6f5-446">Sprawdź, czy bezwzględne miejsca docelowe łączy są prawidłowe przed dołączeniem ich do strony.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-446">Validate that absolute link destinations are valid before including them in a page.</span></span>

<span data-ttu-id="9b6f5-447">Aby uzyskać więcej informacji, zobacz <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-447">For more information, see <xref:security/preventing-open-redirects>.</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="9b6f5-448">Uwierzytelnianie i autoryzacja</span><span class="sxs-lookup"><span data-stu-id="9b6f5-448">Authentication and authorization</span></span>

<span data-ttu-id="9b6f5-449">Aby uzyskać wskazówki dotyczące <xref:security/blazor/index>uwierzytelniania i autoryzacji, zobacz .</span><span class="sxs-lookup"><span data-stu-id="9b6f5-449">For guidance on authentication and authorization, see <xref:security/blazor/index>.</span></span>

## <a name="security-checklist"></a><span data-ttu-id="9b6f5-450">Lista kontrolna zabezpieczeń</span><span class="sxs-lookup"><span data-stu-id="9b6f5-450">Security checklist</span></span>

<span data-ttu-id="9b6f5-451">Poniższa lista zagadnień dotyczących zabezpieczeń nie jest wyczerpująca:</span><span class="sxs-lookup"><span data-stu-id="9b6f5-451">The following list of security considerations isn't comprehensive:</span></span>

* <span data-ttu-id="9b6f5-452">Sprawdzanie poprawności argumentów ze zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-452">Validate arguments from events.</span></span>
* <span data-ttu-id="9b6f5-453">Sprawdź poprawność danych wejściowych i wyników wywołań interop JS.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-453">Validate inputs and results from JS interop calls.</span></span>
* <span data-ttu-id="9b6f5-454">Należy unikać używania (lub sprawdzania poprawności wcześniej) dane wejściowe użytkownika dla .NET do JS wywołania interop.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-454">Avoid using (or validate beforehand) user input for .NET to JS interop calls.</span></span>
* <span data-ttu-id="9b6f5-455">Uniemożliwić klientowi przydzielanie niezwiązanej ilości pamięci.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-455">Prevent the client from allocating an unbound amount of memory.</span></span>
  * <span data-ttu-id="9b6f5-456">Dane w składniku.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-456">Data within the component.</span></span>
  * <span data-ttu-id="9b6f5-457">`DotNetObject`odwołania zwrócone do klienta.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-457">`DotNetObject` references returned to the client.</span></span>
* <span data-ttu-id="9b6f5-458">Ochrona przed wieloma wysyłkami.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-458">Guard against multiple dispatches.</span></span>
* <span data-ttu-id="9b6f5-459">Anuluj długotrwałe operacje, gdy składnik jest usuwany.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-459">Cancel long-running operations when the component is disposed.</span></span>
* <span data-ttu-id="9b6f5-460">Unikaj zdarzeń, które generują duże ilości danych.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-460">Avoid events that produce large amounts of data.</span></span>
* <span data-ttu-id="9b6f5-461">Należy unikać używania danych `NavigationManager.Navigate` wejściowych użytkownika w ramach wywołań i sprawdzania poprawności danych wejściowych użytkownika dla adresów URL względem zestawu dozwolonych źródeł najpierw, jeśli jest to nieuniknione.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-461">Avoid using user input as part of calls to `NavigationManager.Navigate` and validate user input for URLs against a set of allowed origins first if unavoidable.</span></span>
* <span data-ttu-id="9b6f5-462">Nie podejmuj decyzji dotyczących autoryzacji na podstawie stanu interfejsu użytkownika, ale tylko ze stanu składnika.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-462">Don't make authorization decisions based on the state of the UI but only from component state.</span></span>
* <span data-ttu-id="9b6f5-463">Należy rozważyć użycie [zasad zabezpieczeń zawartości (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) do ochrony przed atakami XSS.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-463">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to protect against XSS attacks.</span></span>
* <span data-ttu-id="9b6f5-464">Należy rozważyć użycie CSP i [X-Frame-Options,](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) aby chronić przed zatrzaskiem.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-464">Consider using CSP and [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) to protect against click-jacking.</span></span>
* <span data-ttu-id="9b6f5-465">Upewnij się, że ustawienia cors są odpowiednie, gdy Blazor włączasz cors lub jawnie wyłączyć CORS dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-465">Ensure CORS settings are appropriate when enabling CORS or explicitly disable CORS for Blazor apps.</span></span>
* <span data-ttu-id="9b6f5-466">Przetestuj, aby upewnić Blazor się, że limity po stronie serwera dla aplikacji zapewniają akceptowalne środowisko użytkownika bez niedopuszczalnych poziomów ryzyka.</span><span class="sxs-lookup"><span data-stu-id="9b6f5-466">Test to ensure that the server-side limits for the Blazor app provide an acceptable user experience without unacceptable levels of risk.</span></span>
