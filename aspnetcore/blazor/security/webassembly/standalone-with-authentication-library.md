---
title: Zabezpieczanie ASP.NET Core Blazor WebAssembly autonomicznej aplikacji przy użyciu biblioteki uwierzytelniania
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
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
ms.openlocfilehash: b5c74d9563dc25722d8696482dd5d6eaf00efae4
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504609"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="0179b-102">Zabezpieczanie ASP.NET Core Blazor WebAssembly autonomicznej aplikacji przy użyciu biblioteki uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="0179b-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="0179b-103">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0179b-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0179b-104">*W przypadku Azure Active Directory (AAD) i Azure Active Directory B2C (AAD B2C) nie postępuj zgodnie ze wskazówkami w tym temacie. Zobacz tematy dotyczące usługi AAD i AAD B2C w tym węźle spisu treści.*</span><span class="sxs-lookup"><span data-stu-id="0179b-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="0179b-105">Aby utworzyć [autonomiczną Blazor WebAssembly aplikację](xref:blazor/hosting-models#blazor-webassembly) korzystającą z [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) biblioteki, postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia.</span><span class="sxs-lookup"><span data-stu-id="0179b-105">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0179b-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0179b-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0179b-107">Aby utworzyć nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="0179b-107">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="0179b-108">Po wybraniu szablonu \*\* Blazor WebAssembly aplikacji\*\* w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="0179b-108">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="0179b-109">Wybierz opcję **konta poszczególnych użytkowników** z opcją **Zapisz konta użytkowników w aplikacji** , aby przechowywać użytkowników w ramach aplikacji przy użyciu [Identity](xref:security/authentication/identity) systemu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0179b-109">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="0179b-110">Visual Studio Code/interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="0179b-110">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="0179b-111">Utwórz nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania w pustym folderze.</span><span class="sxs-lookup"><span data-stu-id="0179b-111">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="0179b-112">Określ `Individual` mechanizm uwierzytelniania z `-au|--auth` opcją zapisania użytkowników w aplikacji przy użyciu [Identity](xref:security/authentication/identity) systemu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0179b-112">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="0179b-113">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="0179b-113">Placeholder</span></span>  | <span data-ttu-id="0179b-114">Przykład</span><span class="sxs-lookup"><span data-stu-id="0179b-114">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="0179b-115">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0179b-115">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="0179b-116">Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0179b-116">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0179b-117">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="0179b-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0179b-118">Aby utworzyć nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="0179b-118">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="0179b-119">W kroku **Skonfiguruj nową Blazor WebAssembly aplikację** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .</span><span class="sxs-lookup"><span data-stu-id="0179b-119">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="0179b-120">Aplikacja jest tworzona dla poszczególnych użytkowników przechowywanych w aplikacji z ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="0179b-120">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="0179b-121">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="0179b-121">Authentication package</span></span>

<span data-ttu-id="0179b-122">Gdy aplikacja zostanie utworzona w celu używania poszczególnych kont użytkowników, aplikacja automatycznie otrzymuje odwołanie do pakietu [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) w pliku projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0179b-122">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="0179b-123">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="0179b-123">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="0179b-124">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="0179b-124">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="0179b-125">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="0179b-125">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="0179b-126">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="0179b-126">Authentication service support</span></span>

<span data-ttu-id="0179b-127">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakiet.</span><span class="sxs-lookup"><span data-stu-id="0179b-127">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="0179b-128">Ta metoda konfiguruje usługi wymagane przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="0179b-128">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="0179b-129">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="0179b-129">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="0179b-130">Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="0179b-130">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="0179b-131">Obsługa uwierzytelniania dla aplikacji autonomicznych jest oferowana przy użyciu OpenID Connect Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="0179b-131">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="0179b-132"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelniania aplikacji przy użyciu OIDC.</span><span class="sxs-lookup"><span data-stu-id="0179b-132">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="0179b-133">Wartości wymagane do skonfigurowania aplikacji można uzyskać z adresu IP zgodnego z OIDC.</span><span class="sxs-lookup"><span data-stu-id="0179b-133">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="0179b-134">Uzyskaj wartości podczas rejestrowania aplikacji, która zwykle odbywa się w portalu online.</span><span class="sxs-lookup"><span data-stu-id="0179b-134">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="0179b-135">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="0179b-135">Access token scopes</span></span>

<span data-ttu-id="0179b-136">Blazor WebAssemblySzablon nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="0179b-136">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="0179b-137">Aby zainicjować obsługę administracyjną tokenu dostępu w ramach przepływu logowania, Dodaj zakres do domyślnych zakresów tokenów <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="0179b-137">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="0179b-138">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="0179b-138">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="0179b-139">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="0179b-139">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="0179b-140">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="0179b-140">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="0179b-141">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="0179b-141">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="0179b-142">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="0179b-142">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="0179b-143">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="0179b-143">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="0179b-144">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="0179b-144">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="0179b-145">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="0179b-145">LoginDisplay component</span></span>

<span data-ttu-id="0179b-146">`LoginDisplay`Składnik ( `Shared/LoginDisplay.razor` ) jest renderowany w `MainLayout` składniku ( `Shared/MainLayout.razor` ) i zarządza następującymi zachowaniami:</span><span class="sxs-lookup"><span data-stu-id="0179b-146">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="0179b-147">Dla uwierzytelnionych użytkowników:</span><span class="sxs-lookup"><span data-stu-id="0179b-147">For authenticated users:</span></span>
  * <span data-ttu-id="0179b-148">Wyświetla bieżącą nazwę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="0179b-148">Displays the current username.</span></span>
  * <span data-ttu-id="0179b-149">Oferuje przycisk umożliwiający wylogowanie się z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0179b-149">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="0179b-150">W przypadku użytkowników anonimowych program oferuje opcję logowania.</span><span class="sxs-lookup"><span data-stu-id="0179b-150">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="0179b-151">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="0179b-151">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="0179b-152">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="0179b-152">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="0179b-153">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="0179b-153">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
