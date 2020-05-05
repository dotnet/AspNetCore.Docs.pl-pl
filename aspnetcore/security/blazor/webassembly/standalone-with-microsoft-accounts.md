---
title: Zabezpieczanie aplikacji Blazor autonomicznej ASP.NET Core webassembly przy użyciu kont Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 3ea2b7632fc41e1c8ad72292e45a93e081b6edbe
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776158"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="2008f-102">Zabezpieczanie aplikacji Blazor autonomicznej ASP.NET Core webassembly przy użyciu kont Microsoft</span><span class="sxs-lookup"><span data-stu-id="2008f-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="2008f-103">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2008f-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="2008f-104">Aby utworzyć Blazor autonomiczną aplikację webassembly, która używa [kont Microsoft z usługą Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) do uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="2008f-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="2008f-105">Tworzenie dzierżawy usługi AAD i aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="2008f-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="2008f-106">Zarejestruj aplikację usługi AAD w obszarze **Azure Active Directory** > **rejestracje aplikacji** Azure Active Directory w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="2008f-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="2008f-107">1 \.</span><span class="sxs-lookup"><span data-stu-id="2008f-107">1\.</span></span> <span data-ttu-id="2008f-108">Podaj **nazwę** aplikacji (na przykład \*\* Blazor klienta AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="2008f-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="2008f-109">2 \.</span><span class="sxs-lookup"><span data-stu-id="2008f-109">2\.</span></span> <span data-ttu-id="2008f-110">W obszarze **obsługiwane typy kont**wybierz pozycję **konta w dowolnym katalogu organizacyjnym**.</span><span class="sxs-lookup"><span data-stu-id="2008f-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="2008f-111">3 \.</span><span class="sxs-lookup"><span data-stu-id="2008f-111">3\.</span></span> <span data-ttu-id="2008f-112">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** na **Sieć Web**i podaj identyfikator URI `https://localhost:5001/authentication/login-callback`przekierowania.</span><span class="sxs-lookup"><span data-stu-id="2008f-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="2008f-113">4 \.</span><span class="sxs-lookup"><span data-stu-id="2008f-113">4\.</span></span> <span data-ttu-id="2008f-114">Wyłącz pole wyboru**Przyznaj administratorowi uprawnienia do OpenID Connect i uprawnień offline_access** . **Permissions** > </span><span class="sxs-lookup"><span data-stu-id="2008f-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="2008f-115">5 \.</span><span class="sxs-lookup"><span data-stu-id="2008f-115">5\.</span></span> <span data-ttu-id="2008f-116">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="2008f-116">Select **Register**.</span></span>

   <span data-ttu-id="2008f-117">W obszarze**konfiguracje** > platformy **uwierzytelniania** > w**sieci Web**:</span><span class="sxs-lookup"><span data-stu-id="2008f-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="2008f-118">1 \.</span><span class="sxs-lookup"><span data-stu-id="2008f-118">1\.</span></span> <span data-ttu-id="2008f-119">Upewnij się, że `https://localhost:5001/authentication/login-callback` jest obecny **Identyfikator URI przekierowania** .</span><span class="sxs-lookup"><span data-stu-id="2008f-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="2008f-120">2 \.</span><span class="sxs-lookup"><span data-stu-id="2008f-120">2\.</span></span> <span data-ttu-id="2008f-121">W przypadku **niejawnego przydzielenia**zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="2008f-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="2008f-122">3 \.</span><span class="sxs-lookup"><span data-stu-id="2008f-122">3\.</span></span> <span data-ttu-id="2008f-123">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="2008f-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="2008f-124">4 \.</span><span class="sxs-lookup"><span data-stu-id="2008f-124">4\.</span></span> <span data-ttu-id="2008f-125">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="2008f-125">Select the **Save** button.</span></span>

   <span data-ttu-id="2008f-126">Zapisz identyfikator aplikacji (identyfikator klienta) (na przykład `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="2008f-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="2008f-127">Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="2008f-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="2008f-128">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, Uwzględnij opcję Output w poleceniu z ścieżką (na przykład `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="2008f-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="2008f-129">Nazwa folderu jest również częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="2008f-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="2008f-130">Po utworzeniu aplikacji powinno być możliwe:</span><span class="sxs-lookup"><span data-stu-id="2008f-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="2008f-131">Zaloguj się do aplikacji przy użyciu konta Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2008f-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="2008f-132">Zażądaj tokenów dostępu dla interfejsów API firmy Microsoft, korzystając Blazor z tego samego podejścia jak w przypadku aplikacji autonomicznych, pod warunkiem, że aplikacja została prawidłowo skonfigurowana.</span><span class="sxs-lookup"><span data-stu-id="2008f-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="2008f-133">Aby uzyskać więcej informacji, zobacz [Szybki Start: Konfigurowanie aplikacji do udostępniania interfejsów API sieci Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="2008f-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="2008f-134">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="2008f-134">Authentication package</span></span>

<span data-ttu-id="2008f-135">Gdy aplikacja zostanie utworzona w celu korzystania z kont służbowych (`SingleOrg`), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="2008f-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="2008f-136">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="2008f-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="2008f-137">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="2008f-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="2008f-138">Zastąp `{VERSION}` odwołanie do poprzedniego pakietu wersją `Microsoft.AspNetCore.Blazor.Templates` pakietu pokazanego w <xref:blazor/get-started> artykule.</span><span class="sxs-lookup"><span data-stu-id="2008f-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="2008f-139">Pakiet `Microsoft.Authentication.WebAssembly.Msal` zawiera przechodnie Dodawanie `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2008f-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="2008f-140">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="2008f-140">Authentication service support</span></span>

<span data-ttu-id="2008f-141">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu metody `AddMsalAuthentication` rozszerzenia dostarczonej przez `Microsoft.Authentication.WebAssembly.Msal` pakiet.</span><span class="sxs-lookup"><span data-stu-id="2008f-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="2008f-142">Ta metoda umożliwia skonfigurowanie wszystkich usług wymaganych przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="2008f-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="2008f-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2008f-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="2008f-144">`AddMsalAuthentication` Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2008f-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="2008f-145">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji kont Microsoft podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2008f-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="2008f-146">Konfiguracja jest dostarczana przez plik *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="2008f-146">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="2008f-147">Przykład:</span><span class="sxs-lookup"><span data-stu-id="2008f-147">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="2008f-148">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="2008f-148">Access token scopes</span></span>

<span data-ttu-id="2008f-149">Blazor Szablon webassembly nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="2008f-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="2008f-150">Aby zainicjować obsługę administracyjną tokenu dostępu w ramach przepływu logowania, Dodaj zakres do domyślnych zakresów tokenów dostępu `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="2008f-150">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="2008f-151">Jeśli Azure Portal udostępnia identyfikator URI zakresu, a **aplikacja zgłasza nieobsłużony wyjątek** , gdy odbierze *401 nieautoryzowaną* odpowiedź z interfejsu API, spróbuj użyć identyfikatora URI zakresu, który nie zawiera schematu i hosta.</span><span class="sxs-lookup"><span data-stu-id="2008f-151">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="2008f-152">Na przykład Azure Portal może podać jeden z następujących formatów identyfikatorów URI zakresu:</span><span class="sxs-lookup"><span data-stu-id="2008f-152">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="2008f-153">Podaj identyfikator URI zakresu bez schematu i hosta:</span><span class="sxs-lookup"><span data-stu-id="2008f-153">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="2008f-154">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="2008f-154">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="2008f-155">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="2008f-155">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="2008f-156">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="2008f-156">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="2008f-157">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="2008f-157">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="2008f-158">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="2008f-158">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="2008f-159">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="2008f-159">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="2008f-160">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="2008f-160">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="2008f-161">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="2008f-161">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="2008f-162">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="2008f-162">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="2008f-163">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="2008f-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="2008f-164">Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="2008f-164">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="2008f-165">Szybki Start: Konfigurowanie aplikacji do udostępniania interfejsów API sieci Web</span><span class="sxs-lookup"><span data-stu-id="2008f-165">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
