---
title: Zabezpieczanie ASP.NET Core Blazor WebAssembly aplikacji autonomicznej przy użyciu kont Microsoft
author: guardrex
description: Dowiedz się, jak zabezpieczyć ASP.NET Core Blazor WebAssembly autonomiczną aplikację z kontami Microsoft.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: cddde7d3125dba1a250563085c366122eb26e509
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280913"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="96230-103">Zabezpieczanie ASP.NET Core Blazor WebAssembly aplikacji autonomicznej przy użyciu kont Microsoft</span><span class="sxs-lookup"><span data-stu-id="96230-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="96230-104">W tym artykule opisano sposób tworzenia [autonomicznej Blazor WebAssembly aplikacji](xref:blazor/hosting-models#blazor-webassembly) korzystającej [z kont Microsoft z usługą Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="96230-104">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication.</span></span>

<span data-ttu-id="96230-105">Zarejestruj aplikację usługi AAD w obszarze   >  **rejestracje aplikacji** Azure Active Directory w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="96230-105">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="96230-106">Podaj **nazwę** aplikacji (na przykład **Blazor autonomiczne konta Microsoft AAD**).</span><span class="sxs-lookup"><span data-stu-id="96230-106">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="96230-107">W obszarze **obsługiwane typy kont** wybierz pozycję **konta w dowolnym katalogu organizacyjnym**.</span><span class="sxs-lookup"><span data-stu-id="96230-107">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="96230-108">Ustaw listę rozwijaną **URI przekierowania** na **aplikację jednostronicową (Spa)** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="96230-108">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="96230-109">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="96230-109">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="96230-110">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96230-110">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="96230-111">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="96230-111">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="96230-112">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="96230-112">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="96230-113">Uwaga zostanie wyświetlona w dalszej części tego tematu, aby przypominać IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="96230-113">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="96230-114">Wyczyść pole wyboru **uprawnienia** > **Udziel uprawnień administrator do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="96230-114">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="96230-115">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="96230-115">Select **Register**.</span></span>

<span data-ttu-id="96230-116">Zapisz identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="96230-116">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="96230-117">W  obszarze > **Konfiguracja platformy** uwierzytelniania > **aplikacja jednostronicowa (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="96230-117">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="96230-118">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="96230-118">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="96230-119">W przypadku **niejawnego udzielenia** upewnij się, że **nie** wybrano pól wyboru dla **tokenów dostępu** i **tokenów identyfikatorów** .</span><span class="sxs-lookup"><span data-stu-id="96230-119">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="96230-120">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="96230-120">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="96230-121">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="96230-121">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="96230-122">Podaj **nazwę** aplikacji (na przykład **Blazor autonomiczne konta Microsoft AAD**).</span><span class="sxs-lookup"><span data-stu-id="96230-122">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="96230-123">W obszarze **obsługiwane typy kont** wybierz pozycję **konta w dowolnym katalogu organizacyjnym**.</span><span class="sxs-lookup"><span data-stu-id="96230-123">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="96230-124">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="96230-124">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="96230-125">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="96230-125">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="96230-126">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96230-126">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="96230-127">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="96230-127">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="96230-128">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="96230-128">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="96230-129">Uwaga zostanie wyświetlona w dalszej części tego tematu, aby przypominać IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="96230-129">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="96230-130">Wyczyść pole wyboru **uprawnienia** > **Udziel uprawnień administrator do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="96230-130">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="96230-131">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="96230-131">Select **Register**.</span></span>

<span data-ttu-id="96230-132">Zapisz identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="96230-132">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="96230-133">W  obszarze > **konfiguracje platformy** uwierzytelniania w > **sieci Web**:</span><span class="sxs-lookup"><span data-stu-id="96230-133">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="96230-134">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="96230-134">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="96230-135">W przypadku **niejawnego przydzielenia** zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="96230-135">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="96230-136">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="96230-136">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="96230-137">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="96230-137">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="96230-138">Utwórz aplikację.</span><span class="sxs-lookup"><span data-stu-id="96230-138">Create the app.</span></span> <span data-ttu-id="96230-139">Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="96230-139">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| <span data-ttu-id="96230-140">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="96230-140">Placeholder</span></span>   | <span data-ttu-id="96230-141">Nazwa Azure Portal</span><span class="sxs-lookup"><span data-stu-id="96230-141">Azure portal name</span></span>       | <span data-ttu-id="96230-142">Przykład</span><span class="sxs-lookup"><span data-stu-id="96230-142">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="96230-143">Identyfikator aplikacji (klienta)</span><span class="sxs-lookup"><span data-stu-id="96230-143">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

<span data-ttu-id="96230-144">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96230-144">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="96230-145">W Azure Portal **Identyfikator URI przekierowania** konfiguracji platformy aplikacji jest skonfigurowany dla portu 5001 dla aplikacji, które działają na serwerze Kestrel z ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="96230-145">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="96230-146">Jeśli aplikacja jest uruchamiana na losowo IIS Express porcie, port aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="96230-146">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="96230-147">Jeśli port nie został wcześniej skonfigurowany przy użyciu znanego portu aplikacji, Wróć do rejestracji aplikacji w Azure Portal i zaktualizuj identyfikator URI przekierowania z prawidłowym portem.</span><span class="sxs-lookup"><span data-stu-id="96230-147">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="96230-148">Po utworzeniu aplikacji powinno być możliwe:</span><span class="sxs-lookup"><span data-stu-id="96230-148">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="96230-149">Zaloguj się do aplikacji przy użyciu konto Microsoft.</span><span class="sxs-lookup"><span data-stu-id="96230-149">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="96230-150">Zażądaj tokenów dostępu dla interfejsów API firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="96230-150">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="96230-151">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="96230-151">For more information, see:</span></span>
  * [<span data-ttu-id="96230-152">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="96230-152">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="96230-153">[Szybki Start: Konfigurowanie aplikacji do udostępniania interfejsów API sieci Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="96230-153">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="96230-154">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="96230-154">Authentication package</span></span>

<span data-ttu-id="96230-155">Gdy aplikacja zostanie utworzona w celu korzystania z kont służbowych ( `SingleOrg` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="96230-155">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="96230-156">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="96230-156">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="96230-157">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="96230-157">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="96230-158">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="96230-158">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="96230-159">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Pakiet zawiera przechodnie Dodawanie [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96230-159">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="96230-160">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="96230-160">Authentication service support</span></span>

<span data-ttu-id="96230-161">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pakiet.</span><span class="sxs-lookup"><span data-stu-id="96230-161">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="96230-162">Ta metoda umożliwia skonfigurowanie wszystkich usług wymaganych przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="96230-162">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="96230-163">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="96230-163">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="96230-164"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96230-164">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="96230-165">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="96230-165">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="96230-166">Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="96230-166">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="96230-167">Przykład:</span><span class="sxs-lookup"><span data-stu-id="96230-167">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="96230-168">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="96230-168">Access token scopes</span></span>

<span data-ttu-id="96230-169">Blazor WebAssemblySzablon nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="96230-169">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="96230-170">Aby zainicjować obsługę administracyjną tokenu dostępu w ramach przepływu logowania, Dodaj zakres do domyślnych zakresów tokenów dostępu <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="96230-170">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="96230-171">Określ dodatkowe zakresy z `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="96230-171">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="96230-172">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="96230-172">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="96230-173">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="96230-173">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="96230-174">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="96230-174">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="96230-175">Tryb logowania</span><span class="sxs-lookup"><span data-stu-id="96230-175">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="96230-176">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="96230-176">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="96230-177">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="96230-177">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="96230-178">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="96230-178">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="96230-179">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="96230-179">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="96230-180">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="96230-180">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="96230-181">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="96230-181">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="96230-182">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="96230-182">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="96230-183">Utwórz niestandardową wersję biblioteki MSAL JavaScript.</span><span class="sxs-lookup"><span data-stu-id="96230-183">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="96230-184">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="96230-184">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [<span data-ttu-id="96230-185">Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="96230-185">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="96230-186">Szybki Start: Konfigurowanie aplikacji do udostępniania interfejsów API sieci Web</span><span class="sxs-lookup"><span data-stu-id="96230-186">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
