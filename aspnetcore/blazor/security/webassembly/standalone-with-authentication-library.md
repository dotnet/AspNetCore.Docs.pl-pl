---
title: Zabezpieczanie ASP.NET Core Blazor WebAssembly autonomicznej aplikacji przy użyciu biblioteki uwierzytelniania
author: guardrex
description: Dowiedz się, jak zabezpieczyć Blazor WebAssembly aplikację autonomiczną ASP.NET Core przy użyciu biblioteki uwierzytelniania.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2021
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: a198606caf55232c221f1d1f1224918d3f87f04c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280889"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="b1e96-103">Zabezpieczanie ASP.NET Core Blazor WebAssembly autonomicznej aplikacji przy użyciu biblioteki uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="b1e96-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="b1e96-104">*W przypadku Azure Active Directory (AAD) i Azure Active Directory B2C (AAD B2C) nie postępuj zgodnie ze wskazówkami w tym temacie. Zobacz tematy dotyczące usługi AAD i AAD B2C w tym węźle spisu treści.*</span><span class="sxs-lookup"><span data-stu-id="b1e96-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="b1e96-105">Aby utworzyć [autonomiczną Blazor WebAssembly aplikację](xref:blazor/hosting-models#blazor-webassembly) korzystającą z [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) biblioteki, postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia.</span><span class="sxs-lookup"><span data-stu-id="b1e96-105">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span> <span data-ttu-id="b1e96-106">W przypadku dodawania obsługi uwierzytelniania programu zapoznaj się z poniższymi sekcjami tego artykułu, aby uzyskać wskazówki dotyczące konfigurowania i konfigurowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1e96-106">If adding support for authentication, see the following sections of this article for guidance on setting up and configuring the app.</span></span>

> [!NOTE]
> <span data-ttu-id="b1e96-107">IdentityDostawca (IP) musi używać [OpenID Connect Connect (OIDC)](https://openid.net/connect/).</span><span class="sxs-lookup"><span data-stu-id="b1e96-107">The Identity Provider (IP) must use [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span> <span data-ttu-id="b1e96-108">Na przykład adres IP w serwisie Facebook nie jest zgodny z OIDC, więc wskazówki zawarte w tym temacie nie działają z adresem IP w usłudze Facebook.</span><span class="sxs-lookup"><span data-stu-id="b1e96-108">For example, Facebook's IP isn't an OIDC-compliant provider, so the guidance in this topic doesn't work with the Facebook IP.</span></span> <span data-ttu-id="b1e96-109">Aby uzyskać więcej informacji, zobacz <xref:blazor/security/webassembly/index#authentication-library>.</span><span class="sxs-lookup"><span data-stu-id="b1e96-109">For more information, see <xref:blazor/security/webassembly/index#authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b1e96-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1e96-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b1e96-111">Aby utworzyć nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="b1e96-111">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="b1e96-112">Po wybraniu szablonu **Blazor WebAssembly aplikacji** w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="b1e96-112">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="b1e96-113">Wybierz pozycję **indywidualne konta użytkowników** z opcją **Zapisz konta użytkowników w aplikacji** , aby użyć systemu ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="b1e96-113">Select **Individual User Accounts** with the **Store user accounts in-app** option to use ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span> <span data-ttu-id="b1e96-114">Wybranie tej opcji powoduje dodanie obsługi uwierzytelniania i nie powoduje przechowywania użytkowników w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="b1e96-114">This selection adds authentication support and doesn't result in storing users in a database.</span></span> <span data-ttu-id="b1e96-115">Poniższe sekcje tego artykułu zawierają szczegółowe informacje.</span><span class="sxs-lookup"><span data-stu-id="b1e96-115">The following sections of this article provide further details.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="b1e96-116">Visual Studio Code/interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="b1e96-116">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="b1e96-117">Utwórz nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania w pustym folderze.</span><span class="sxs-lookup"><span data-stu-id="b1e96-117">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="b1e96-118">Określ `Individual` mechanizm uwierzytelniania przy `-au|--auth` użyciu opcji używania [Identity](xref:security/authentication/identity) systemu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b1e96-118">Specify the `Individual` authentication mechanism with the `-au|--auth` option to use ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span> <span data-ttu-id="b1e96-119">Wybranie tej opcji powoduje dodanie obsługi uwierzytelniania i nie powoduje przechowywania użytkowników w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="b1e96-119">This selection adds authentication support and doesn't result in storing users in a database.</span></span> <span data-ttu-id="b1e96-120">Poniższe sekcje tego artykułu zawierają szczegółowe informacje.</span><span class="sxs-lookup"><span data-stu-id="b1e96-120">The following sections of this article provide further details.</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="b1e96-121">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="b1e96-121">Placeholder</span></span>  | <span data-ttu-id="b1e96-122">Przykład</span><span class="sxs-lookup"><span data-stu-id="b1e96-122">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="b1e96-123">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1e96-123">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="b1e96-124">Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b1e96-124">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b1e96-125">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="b1e96-125">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b1e96-126">Aby utworzyć nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="b1e96-126">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="b1e96-127">W kroku **Skonfiguruj nową Blazor WebAssembly aplikację** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="b1e96-127">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="b1e96-128">Aplikacja zostanie utworzona, aby używać ASP.NET Core [Identity](xref:security/authentication/identity) i nie powoduje przechowywania użytkowników w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="b1e96-128">The app is created to use ASP.NET Core [Identity](xref:security/authentication/identity) and doesn't result in storing users in a database.</span></span> <span data-ttu-id="b1e96-129">Poniższe sekcje tego artykułu zawierają szczegółowe informacje.</span><span class="sxs-lookup"><span data-stu-id="b1e96-129">The following sections of this article provide further details.</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="b1e96-130">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="b1e96-130">Authentication package</span></span>

<span data-ttu-id="b1e96-131">Gdy aplikacja zostanie utworzona w celu używania poszczególnych kont użytkowników, aplikacja automatycznie otrzymuje odwołanie do pakietu [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1e96-131">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="b1e96-132">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="b1e96-132">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="b1e96-133">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="b1e96-133">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="b1e96-134">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="b1e96-134">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="b1e96-135">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="b1e96-135">Authentication service support</span></span>

<span data-ttu-id="b1e96-136">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakiet.</span><span class="sxs-lookup"><span data-stu-id="b1e96-136">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="b1e96-137">Ta metoda konfiguruje usługi wymagane przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="b1e96-137">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="b1e96-138">Dla nowej aplikacji podaj wartości dla `{AUTHORITY}` `{CLIENT ID}` symboli zastępczych i w poniższej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="b1e96-138">For a new app, provide values for the `{AUTHORITY}` and `{CLIENT ID}` placeholders in the following configuration.</span></span> <span data-ttu-id="b1e96-139">Podaj inne wartości konfiguracyjne, które są wymagane do użycia z adresem IP aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1e96-139">Provide other configuration values that are required for use with the app's IP.</span></span> <span data-ttu-id="b1e96-140">Przykład dotyczy usługi Google, która wymaga `PostLogoutRedirectUri` , `RedirectUri` i `ResponseType` .</span><span class="sxs-lookup"><span data-stu-id="b1e96-140">The example is for Google, which requires `PostLogoutRedirectUri`, `RedirectUri`, and `ResponseType`.</span></span> <span data-ttu-id="b1e96-141">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj następujący kod i konfigurację do aplikacji z wartościami dla symboli zastępczych i innych wartości konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="b1e96-141">If adding authentication to an app, manually add the following code and configuration to the app with values for the placeholders and other configuration values.</span></span>

<span data-ttu-id="b1e96-142">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="b1e96-142">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="b1e96-143">Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="b1e96-143">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="b1e96-144">Przykład OIDC usługi Google OAuth 2,0:</span><span class="sxs-lookup"><span data-stu-id="b1e96-144">Google OAuth 2.0 OIDC example:</span></span>

```json
{
  "Local": {
    "Authority": "https://accounts.google.com/",
    "ClientId": "2.......7-e.....................q.apps.googleusercontent.com",
    "PostLogoutRedirectUri": "https://localhost:5001/authentication/logout-callback",
    "RedirectUri": "https://localhost:5001/authentication/login-callback",
    "ResponseType": "id_token"
  }
}
```

<span data-ttu-id="b1e96-145">Identyfikator URI przekierowania ( `https://localhost:5001/authentication/login-callback` ) jest zarejestrowany w [konsoli interfejsów API usługi Google](https://console.developers.google.com/apis/dashboard) w obszarze **poświadczenia**  >  **`{NAME}`**  >  **autoryzowanych identyfikatorów URI przekierowania**, gdzie `{NAME}` to nazwa klienta aplikacji na liście **identyfikatorów klienta OAuth 2,0** w konsoli interfejsów API Google.</span><span class="sxs-lookup"><span data-stu-id="b1e96-145">The redirect URI (`https://localhost:5001/authentication/login-callback`) is registered in the [Google APIs console](https://console.developers.google.com/apis/dashboard) in **Credentials** > **`{NAME}`** > **Authorized redirect URIs**, where `{NAME}` is the app's client name in the **OAuth 2.0 Client IDs** app list of the Google APIs console.</span></span>

<span data-ttu-id="b1e96-146">Obsługa uwierzytelniania dla aplikacji autonomicznych jest oferowana przy użyciu OpenID Connect Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="b1e96-146">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="b1e96-147"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelniania aplikacji przy użyciu OIDC.</span><span class="sxs-lookup"><span data-stu-id="b1e96-147">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="b1e96-148">Wartości wymagane do skonfigurowania aplikacji można uzyskać z adresu IP zgodnego z OIDC.</span><span class="sxs-lookup"><span data-stu-id="b1e96-148">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="b1e96-149">Uzyskaj wartości podczas rejestrowania aplikacji, która zwykle odbywa się w portalu online.</span><span class="sxs-lookup"><span data-stu-id="b1e96-149">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="b1e96-150">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="b1e96-150">Access token scopes</span></span>

<span data-ttu-id="b1e96-151">Blazor WebAssemblySzablon automatycznie konfiguruje domyślne zakresy dla `openid` i `profile` .</span><span class="sxs-lookup"><span data-stu-id="b1e96-151">The Blazor WebAssembly template automatically configures default scopes for `openid` and `profile`.</span></span>

<span data-ttu-id="b1e96-152">Blazor WebAssemblySzablon nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="b1e96-152">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="b1e96-153">Aby zainicjować obsługę administracyjną tokenu dostępu w ramach przepływu logowania, Dodaj zakres do domyślnych zakresów tokenów <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> .</span><span class="sxs-lookup"><span data-stu-id="b1e96-153">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>.</span></span> <span data-ttu-id="b1e96-154">W przypadku dodawania uwierzytelniania do aplikacji należy ręcznie dodać poniższy kod i skonfigurować identyfikator URI zakresu.</span><span class="sxs-lookup"><span data-stu-id="b1e96-154">If adding authentication to an app, manually add the following code and configure the scope URI.</span></span>

<span data-ttu-id="b1e96-155">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="b1e96-155">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="b1e96-156">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="b1e96-156">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="b1e96-157">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="b1e96-157">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="b1e96-158">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="b1e96-158">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="b1e96-159">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="b1e96-159">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="b1e96-160">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="b1e96-160">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="b1e96-161">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="b1e96-161">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="b1e96-162">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="b1e96-162">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="b1e96-163">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="b1e96-163">LoginDisplay component</span></span>

<span data-ttu-id="b1e96-164">`LoginDisplay`Składnik ( `Shared/LoginDisplay.razor` ) jest renderowany w `MainLayout` składniku ( `Shared/MainLayout.razor` ) i zarządza następującymi zachowaniami:</span><span class="sxs-lookup"><span data-stu-id="b1e96-164">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="b1e96-165">Dla uwierzytelnionych użytkowników:</span><span class="sxs-lookup"><span data-stu-id="b1e96-165">For authenticated users:</span></span>
  * <span data-ttu-id="b1e96-166">Wyświetla bieżącą nazwę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b1e96-166">Displays the current username.</span></span>
  * <span data-ttu-id="b1e96-167">Oferuje przycisk umożliwiający wylogowanie się z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b1e96-167">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="b1e96-168">W przypadku użytkowników anonimowych program oferuje opcję logowania.</span><span class="sxs-lookup"><span data-stu-id="b1e96-168">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="b1e96-169">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="b1e96-169">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="b1e96-170">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="b1e96-170">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="b1e96-171">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="b1e96-171">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="b1e96-172"><xref:host-and-deploy/proxy-load-balancer>: Zawiera wskazówki dotyczące:</span><span class="sxs-lookup"><span data-stu-id="b1e96-172"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="b1e96-173">Używanie przekierowanych nagłówków oprogramowania do zachowywania informacji schematu HTTPS między serwerami proxy i sieciami wewnętrznymi.</span><span class="sxs-lookup"><span data-stu-id="b1e96-173">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="b1e96-174">Dodatkowe scenariusze i przypadki użycia, w tym konfiguracja schematu ręcznego, zmiany ścieżki żądań dla poprawnego routingu żądań i przekazywanie schematu żądań dla systemu Linux i innych niż usługi IIS zwrotnych serwerów proxy.</span><span class="sxs-lookup"><span data-stu-id="b1e96-174">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
