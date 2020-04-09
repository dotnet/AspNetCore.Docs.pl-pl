---
title: Zabezpieczanie autonomicznej Blazor aplikacji ASP.NET Core WebAssembly za pomocą kont Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977083"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="8cb56-102">Zabezpieczanie autonomicznej Blazor aplikacji ASP.NET Core WebAssembly za pomocą kont Microsoft</span><span class="sxs-lookup"><span data-stu-id="8cb56-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="8cb56-103">Autorstwa [Javiera Calvarro Nelsona](https://github.com/javiercn) i [Luke'a Lathama](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8cb56-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="8cb56-104">Aby utworzyć Blazor autonomiczną aplikację WebAssembly, która używa [kont Microsoft z usługą Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) do uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="8cb56-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="8cb56-105">Tworzenie dzierżawy usługi AAD i aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="8cb56-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="8cb56-106">Zarejestruj aplikację usługi AAD w obszarze**rejestracji aplikacji** usługi Azure **Active Directory** > w witrynie Azure portal:</span><span class="sxs-lookup"><span data-stu-id="8cb56-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="8cb56-107">1\.</span><span class="sxs-lookup"><span data-stu-id="8cb56-107">1\.</span></span> <span data-ttu-id="8cb56-108">Podaj **nazwę** aplikacji (na przykład \*\* Blazor client AAD).\*\*</span><span class="sxs-lookup"><span data-stu-id="8cb56-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="8cb56-109">2\.</span><span class="sxs-lookup"><span data-stu-id="8cb56-109">2\.</span></span> <span data-ttu-id="8cb56-110">W **obszarze Obsługiwane typy kont**wybierz pozycję Konta w dowolnym katalogu **organizacji**.</span><span class="sxs-lookup"><span data-stu-id="8cb56-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="8cb56-111">3\.</span><span class="sxs-lookup"><span data-stu-id="8cb56-111">3\.</span></span> <span data-ttu-id="8cb56-112">Pozostaw zestaw rozwijany **Przekierowania identyfikatora URI** `https://localhost:5001/authentication/login-callback`do sieci **Web**i podaj identyfikator URI przekierowania .</span><span class="sxs-lookup"><span data-stu-id="8cb56-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="8cb56-113">4\.</span><span class="sxs-lookup"><span data-stu-id="8cb56-113">4\.</span></span> <span data-ttu-id="8cb56-114">Wyłącz pole **wyboru Uprawnienia** > **udzielaj administracyjnej do otwierania i offline_access uprawnień.**</span><span class="sxs-lookup"><span data-stu-id="8cb56-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="8cb56-115">5\.</span><span class="sxs-lookup"><span data-stu-id="8cb56-115">5\.</span></span> <span data-ttu-id="8cb56-116">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="8cb56-116">Select **Register**.</span></span>

   <span data-ttu-id="8cb56-117">W**konfiguracji** > platformy **uwierzytelniania** > **w sieci Web:**</span><span class="sxs-lookup"><span data-stu-id="8cb56-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="8cb56-118">1\.</span><span class="sxs-lookup"><span data-stu-id="8cb56-118">1\.</span></span> <span data-ttu-id="8cb56-119">Upewnij się, że `https://localhost:5001/authentication/login-callback` identyfikator **URI przekierowania** jest obecny.</span><span class="sxs-lookup"><span data-stu-id="8cb56-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="8cb56-120">2\.</span><span class="sxs-lookup"><span data-stu-id="8cb56-120">2\.</span></span> <span data-ttu-id="8cb56-121">W przypadku **dotacji niejawnych**zaznacz pola wyboru **tokenów programu Access** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="8cb56-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="8cb56-122">3\.</span><span class="sxs-lookup"><span data-stu-id="8cb56-122">3\.</span></span> <span data-ttu-id="8cb56-123">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="8cb56-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="8cb56-124">4\.</span><span class="sxs-lookup"><span data-stu-id="8cb56-124">4\.</span></span> <span data-ttu-id="8cb56-125">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="8cb56-125">Select the **Save** button.</span></span>

   <span data-ttu-id="8cb56-126">Zarejestruj identyfikator aplikacji (identyfikator klienta) (na `11111111-1111-1111-1111-111111111111`przykład ).</span><span class="sxs-lookup"><span data-stu-id="8cb56-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="8cb56-127">Zastąp symbole zastępcze w następującym poleceniu informacjami zarejestrowanymi wcześniej i wykonaj polecenie w powłoce polecenia:</span><span class="sxs-lookup"><span data-stu-id="8cb56-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="8cb56-128">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, należy dołączyć opcję `-o BlazorSample`wyjściową w poleceniu ze ścieżką (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="8cb56-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="8cb56-129">Nazwa folderu również staje się częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="8cb56-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="8cb56-130">Po utworzeniu aplikacji powinieneś być w stanie:</span><span class="sxs-lookup"><span data-stu-id="8cb56-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="8cb56-131">Zaloguj się do aplikacji przy użyciu konta Microsoft.</span><span class="sxs-lookup"><span data-stu-id="8cb56-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="8cb56-132">Żądaj tokenów dostępu dla interfejsów API firmy Microsoft Blazor przy użyciu tego samego podejścia, co w przypadku aplikacji autonomicznych, pod warunkiem że aplikacja została poprawnie skonfigurowana.</span><span class="sxs-lookup"><span data-stu-id="8cb56-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="8cb56-133">Aby uzyskać więcej informacji, zobacz [Szybki start: Konfigurowanie aplikacji w celu udostępnienia interfejsów API sieci Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="8cb56-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="8cb56-134">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="8cb56-134">Authentication package</span></span>

<span data-ttu-id="8cb56-135">Po utworzeniu aplikacji do używania`SingleOrg`kont służbowych ( aplikacja automatycznie otrzymuje odwołanie`Microsoft.Authentication.WebAssembly.Msal`do pakietu dla [biblioteki uwierzytelniania Firmy Microsoft](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="8cb56-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="8cb56-136">Pakiet zawiera zestaw uwierzytelniania, które pomagają aplikacji uwierzytelniać użytkowników i uzyskać tokeny do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="8cb56-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="8cb56-137">Jeśli dodasz uwierzytelnianie do aplikacji, ręcznie dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="8cb56-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="8cb56-138">Zastąp `{VERSION}` w poprzednim odwołaniu `Microsoft.AspNetCore.Blazor.Templates` do pakietu <xref:blazor/get-started> wersją pakietu przedstawioną w artykule.</span><span class="sxs-lookup"><span data-stu-id="8cb56-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="8cb56-139">Pakiet `Microsoft.Authentication.WebAssembly.Msal` przechodnie dodaje `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakiet do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8cb56-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="8cb56-140">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="8cb56-140">Authentication service support</span></span>

<span data-ttu-id="8cb56-141">Obsługa uwierzytelniania użytkowników jest zarejestrowana w `AddMsalAuthentication` kontenerze usługi `Microsoft.Authentication.WebAssembly.Msal` przy użyciu metody rozszerzenia dostarczonej przez pakiet.</span><span class="sxs-lookup"><span data-stu-id="8cb56-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="8cb56-142">Ta metoda konfiguruje wszystkie usługi wymagane dla aplikacji do interakcji z dostawcą tożsamości (IP).</span><span class="sxs-lookup"><span data-stu-id="8cb56-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="8cb56-143">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="8cb56-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="8cb56-144">Metoda `AddMsalAuthentication` akceptuje wywołanie zwrotne, aby skonfigurować parametry wymagane do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8cb56-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="8cb56-145">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji kont Microsoft podczas rejestracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8cb56-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="8cb56-146">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="8cb56-146">Access token scopes</span></span>

<span data-ttu-id="8cb56-147">Szablon Blazor WebAssembly nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="8cb56-147">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="8cb56-148">Aby aprowizować token jako część przepływu logowania, dodaj zakres do domyślnych zakresów tokenu `MsalProviderOptions`dostępu:</span><span class="sxs-lookup"><span data-stu-id="8cb56-148">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="8cb56-149">Jeśli portal Azure udostępnia identyfikator URI zakresu i **aplikacja zgłasza nieobsługiwanie wyjątek** po otrzymaniu *401 nieautoryzowanej* odpowiedzi z interfejsu API, spróbuj użyć identyfikatora URI zakresu, który nie zawiera schematu i hosta.</span><span class="sxs-lookup"><span data-stu-id="8cb56-149">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="8cb56-150">Na przykład witryny Azure portal może zapewnić jeden z następujących formatów identyfikatora URI zakresu:</span><span class="sxs-lookup"><span data-stu-id="8cb56-150">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="8cb56-151">Podać identyfikator URI zakresu bez schematu i hosta:</span><span class="sxs-lookup"><span data-stu-id="8cb56-151">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="8cb56-152">Aby uzyskać więcej informacji, zobacz <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="8cb56-152">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="8cb56-153">Import pliku</span><span class="sxs-lookup"><span data-stu-id="8cb56-153">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="8cb56-154">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="8cb56-154">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="8cb56-155">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="8cb56-155">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="8cb56-156">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="8cb56-156">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="8cb56-157">Składnik LogowanieWydajnik</span><span class="sxs-lookup"><span data-stu-id="8cb56-157">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="8cb56-158">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="8cb56-158">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="8cb56-159">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="8cb56-159">Additional resources</span></span>

* [<span data-ttu-id="8cb56-160">Żądanie dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="8cb56-160">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="8cb56-161">Szybki start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="8cb56-161">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="8cb56-162">Szybki start: konfigurowanie aplikacji do udostępniania interfejsów API sieci Web</span><span class="sxs-lookup"><span data-stu-id="8cb56-162">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
