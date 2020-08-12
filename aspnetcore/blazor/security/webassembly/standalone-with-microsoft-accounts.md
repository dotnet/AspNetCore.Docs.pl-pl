---
title: Zabezpieczanie ASP.NET Core Blazor WebAssembly aplikacji autonomicznej przy użyciu kont Microsoft
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
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8f48165e2501ea17de6d5cd540f1d6ff088d3173
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130278"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="adc66-102">Zabezpieczanie ASP.NET Core Blazor WebAssembly aplikacji autonomicznej przy użyciu kont Microsoft</span><span class="sxs-lookup"><span data-stu-id="adc66-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="adc66-103">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="adc66-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="adc66-104">Aby utworzyć [autonomiczną Blazor WebAssembly aplikację](xref:blazor/hosting-models#blazor-webassembly) korzystającą [z kont Microsoft z usługą Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) do uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="adc66-104">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="adc66-105">Tworzenie dzierżawy usługi AAD i aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="adc66-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="adc66-106">Zarejestruj aplikację usługi AAD w obszarze **Azure Active Directory**  >  **rejestracje aplikacji** Azure Active Directory w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="adc66-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="adc66-107">Podaj **nazwę** aplikacji (na przykład \*\* Blazor autonomiczne konta Microsoft AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="adc66-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="adc66-108">W obszarze **obsługiwane typy kont**wybierz pozycję **konta w dowolnym katalogu organizacyjnym**.</span><span class="sxs-lookup"><span data-stu-id="adc66-108">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="adc66-109">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="adc66-109">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="adc66-110">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="adc66-110">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="adc66-111">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="adc66-111">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="adc66-112">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="adc66-112">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="adc66-113">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="adc66-113">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="adc66-114">Uwaga zostanie wyświetlona w dalszej części tego tematu, aby przypominać IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="adc66-114">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="adc66-115">Wyłącz **Permissions**  >  pole wyboru**Przyznaj zgodę na uprawnienia administratora OpenID Connect i offline_access** .</span><span class="sxs-lookup"><span data-stu-id="adc66-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="adc66-116">Wybierz pozycję **Rejestruj**.</span><span class="sxs-lookup"><span data-stu-id="adc66-116">Select **Register**.</span></span>

<span data-ttu-id="adc66-117">Zapisz identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="adc66-117">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="adc66-118">W **Authentication**obszarze  >  **konfiguracje platformy**uwierzytelniania w  >  **sieci Web**:</span><span class="sxs-lookup"><span data-stu-id="adc66-118">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="adc66-119">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="adc66-119">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="adc66-120">W przypadku **niejawnego przydzielenia**zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="adc66-120">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="adc66-121">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="adc66-121">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="adc66-122">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="adc66-122">Select the **Save** button.</span></span>

<span data-ttu-id="adc66-123">Utwórz aplikację.</span><span class="sxs-lookup"><span data-stu-id="adc66-123">Create the app.</span></span> <span data-ttu-id="adc66-124">Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj następujące polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="adc66-124">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| <span data-ttu-id="adc66-125">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="adc66-125">Placeholder</span></span>   | <span data-ttu-id="adc66-126">Nazwa Azure Portal</span><span class="sxs-lookup"><span data-stu-id="adc66-126">Azure portal name</span></span>       | <span data-ttu-id="adc66-127">Przykład</span><span class="sxs-lookup"><span data-stu-id="adc66-127">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="adc66-128">Identyfikator aplikacji (klienta)</span><span class="sxs-lookup"><span data-stu-id="adc66-128">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

<span data-ttu-id="adc66-129">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="adc66-129">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="adc66-130">W Azure Portal **Authentication**  >  **Konfiguracja platformy**uwierzytelniania w  >  **sieci Web**  >  **Identyfikator URI przekierowania** dla aplikacji jest skonfigurowany dla portu 5001 dla aplikacji, które działają na serwerze Kestrel z ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="adc66-130">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="adc66-131">Jeśli aplikacja jest uruchamiana na losowo IIS Express porcie, port aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="adc66-131">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="adc66-132">Jeśli port nie został wcześniej skonfigurowany przy użyciu znanego portu aplikacji, Wróć do rejestracji aplikacji w Azure Portal i zaktualizuj identyfikator URI przekierowania z prawidłowym portem.</span><span class="sxs-lookup"><span data-stu-id="adc66-132">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="adc66-133">Po utworzeniu aplikacji powinno być możliwe:</span><span class="sxs-lookup"><span data-stu-id="adc66-133">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="adc66-134">Zaloguj się do aplikacji przy użyciu konto Microsoft.</span><span class="sxs-lookup"><span data-stu-id="adc66-134">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="adc66-135">Zażądaj tokenów dostępu dla interfejsów API firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="adc66-135">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="adc66-136">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="adc66-136">For more information, see:</span></span>
  * [<span data-ttu-id="adc66-137">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="adc66-137">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="adc66-138">[Szybki Start: Konfigurowanie aplikacji do udostępniania interfejsów API sieci Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="adc66-138">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="adc66-139">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="adc66-139">Authentication package</span></span>

<span data-ttu-id="adc66-140">Gdy aplikacja zostanie utworzona w celu korzystania z kont służbowych ( `SingleOrg` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) ).</span><span class="sxs-lookup"><span data-stu-id="adc66-140">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="adc66-141">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="adc66-141">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="adc66-142">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="adc66-142">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="adc66-143">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)Pakiet zawiera przechodnie Dodawanie [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="adc66-143">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="adc66-144">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="adc66-144">Authentication service support</span></span>

<span data-ttu-id="adc66-145">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pakiet.</span><span class="sxs-lookup"><span data-stu-id="adc66-145">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="adc66-146">Ta metoda umożliwia skonfigurowanie wszystkich usług wymaganych przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="adc66-146">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="adc66-147">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="adc66-147">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="adc66-148"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="adc66-148">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="adc66-149">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="adc66-149">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="adc66-150">Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="adc66-150">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="adc66-151">Przykład:</span><span class="sxs-lookup"><span data-stu-id="adc66-151">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="adc66-152">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="adc66-152">Access token scopes</span></span>

<span data-ttu-id="adc66-153">Blazor WebAssemblySzablon nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="adc66-153">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="adc66-154">Aby zainicjować obsługę administracyjną tokenu dostępu w ramach przepływu logowania, Dodaj zakres do domyślnych zakresów tokenów dostępu <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="adc66-154">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="adc66-155">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="adc66-155">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="adc66-156">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="adc66-156">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="adc66-157">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="adc66-157">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="adc66-158">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="adc66-158">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="adc66-159">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="adc66-159">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="adc66-160">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="adc66-160">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="adc66-161">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="adc66-161">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="adc66-162">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="adc66-162">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="adc66-163">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="adc66-163">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="adc66-164">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="adc66-164">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="adc66-165">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="adc66-165">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [<span data-ttu-id="adc66-166">Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="adc66-166">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="adc66-167">Szybki Start: Konfigurowanie aplikacji do udostępniania interfejsów API sieci Web</span><span class="sxs-lookup"><span data-stu-id="adc66-167">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
