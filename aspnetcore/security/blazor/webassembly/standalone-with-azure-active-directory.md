---
title: Zabezpieczanie autonomicznej Blazor aplikacji ASP.NET Core WebAssembly za pomocą usługi Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 7e132723657b7e12803b67ec12c3a33f1945baa3
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977002"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="fa406-102">Zabezpieczanie autonomicznej Blazor aplikacji ASP.NET Core WebAssembly za pomocą usługi Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="fa406-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="fa406-103">Autorstwa [Javiera Calvarro Nelsona](https://github.com/javiercn) i [Luke'a Lathama](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fa406-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="fa406-104">Aby utworzyć Blazor autonomiczną aplikację webassembly, która używa [usługi Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="fa406-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="fa406-105">[Tworzenie dzierżawy usługi AAD i aplikacji sieci web:](/azure/active-directory/develop/v2-overview)</span><span class="sxs-lookup"><span data-stu-id="fa406-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="fa406-106">Zarejestruj aplikację usługi AAD w obszarze**rejestracji aplikacji** usługi Azure **Active Directory** > w witrynie Azure portal:</span><span class="sxs-lookup"><span data-stu-id="fa406-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="fa406-107">Podaj **nazwę** aplikacji (na przykład \*\* Blazor client AAD).\*\*</span><span class="sxs-lookup"><span data-stu-id="fa406-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="fa406-108">Wybierz **typy obsługiwanych kont**.</span><span class="sxs-lookup"><span data-stu-id="fa406-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="fa406-109">Dla tego środowiska można wybrać opcję **Konta w tym katalogu organizacyjnym.**</span><span class="sxs-lookup"><span data-stu-id="fa406-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="fa406-110">Pozostaw zestaw rozwijany **Przekierowania identyfikatora URI** `https://localhost:5001/authentication/login-callback`do sieci **Web**i podaj identyfikator URI przekierowania .</span><span class="sxs-lookup"><span data-stu-id="fa406-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="fa406-111">Wyłącz pole **wyboru Uprawnienia** > **udzielaj administracyjnej do otwierania i offline_access uprawnień.**</span><span class="sxs-lookup"><span data-stu-id="fa406-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="fa406-112">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="fa406-112">Select **Register**.</span></span>

<span data-ttu-id="fa406-113">W**konfiguracji** > platformy **uwierzytelniania** > **w sieci Web:**</span><span class="sxs-lookup"><span data-stu-id="fa406-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="fa406-114">Upewnij się, że `https://localhost:5001/authentication/login-callback` identyfikator **URI przekierowania** jest obecny.</span><span class="sxs-lookup"><span data-stu-id="fa406-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="fa406-115">W przypadku **dotacji niejawnych**zaznacz pola wyboru **tokenów programu Access** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="fa406-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="fa406-116">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="fa406-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="fa406-117">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="fa406-117">Select the **Save** button.</span></span>

<span data-ttu-id="fa406-118">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="fa406-118">Record the following information:</span></span>

* <span data-ttu-id="fa406-119">Identyfikator aplikacji (identyfikator klienta) (na `11111111-1111-1111-1111-111111111111`przykład)</span><span class="sxs-lookup"><span data-stu-id="fa406-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="fa406-120">Identyfikator katalogu (identyfikator dzierżawy) (na `22222222-2222-2222-2222-222222222222`przykład )</span><span class="sxs-lookup"><span data-stu-id="fa406-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="fa406-121">Zastąp symbole zastępcze w następującym poleceniu informacjami zarejestrowanymi wcześniej i wykonaj polecenie w powłoce polecenia:</span><span class="sxs-lookup"><span data-stu-id="fa406-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="fa406-122">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, należy dołączyć opcję `-o BlazorSample`wyjściową w poleceniu ze ścieżką (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="fa406-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="fa406-123">Nazwa folderu również staje się częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="fa406-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="fa406-124">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="fa406-124">Authentication package</span></span>

<span data-ttu-id="fa406-125">Po utworzeniu aplikacji do używania`SingleOrg`kont służbowych ( aplikacja automatycznie otrzymuje odwołanie`Microsoft.Authentication.WebAssembly.Msal`do pakietu dla [biblioteki uwierzytelniania Firmy Microsoft](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="fa406-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="fa406-126">Pakiet zawiera zestaw uwierzytelniania, które pomagają aplikacji uwierzytelniać użytkowników i uzyskać tokeny do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="fa406-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="fa406-127">Jeśli dodasz uwierzytelnianie do aplikacji, ręcznie dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="fa406-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="fa406-128">Zastąp `{VERSION}` w poprzednim odwołaniu `Microsoft.AspNetCore.Blazor.Templates` do pakietu <xref:blazor/get-started> wersją pakietu przedstawioną w artykule.</span><span class="sxs-lookup"><span data-stu-id="fa406-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="fa406-129">Pakiet `Microsoft.Authentication.WebAssembly.Msal` przechodnie dodaje `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakiet do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fa406-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="fa406-130">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="fa406-130">Authentication service support</span></span>

<span data-ttu-id="fa406-131">Obsługa uwierzytelniania użytkowników jest zarejestrowana w `AddMsalAuthentication` kontenerze usługi `Microsoft.Authentication.WebAssembly.Msal` przy użyciu metody rozszerzenia dostarczonej przez pakiet.</span><span class="sxs-lookup"><span data-stu-id="fa406-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="fa406-132">Ta metoda konfiguruje wszystkie usługi wymagane dla aplikacji do interakcji z dostawcą tożsamości (IP).</span><span class="sxs-lookup"><span data-stu-id="fa406-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="fa406-133">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="fa406-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="fa406-134">Metoda `AddMsalAuthentication` akceptuje wywołanie zwrotne, aby skonfigurować parametry wymagane do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fa406-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="fa406-135">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD usługi Azure Portal podczas rejestracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fa406-135">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="fa406-136">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="fa406-136">Access token scopes</span></span>

<span data-ttu-id="fa406-137">Szablon Blazor WebAssembly nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="fa406-137">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="fa406-138">Aby aprowizować token jako część przepływu logowania, dodaj zakres do domyślnych zakresów tokenu `MsalProviderOptions`dostępu:</span><span class="sxs-lookup"><span data-stu-id="fa406-138">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="fa406-139">Jeśli portal Azure udostępnia identyfikator URI zakresu i **aplikacja zgłasza nieobsługiwanie wyjątek** po otrzymaniu *401 nieautoryzowanej* odpowiedzi z interfejsu API, spróbuj użyć identyfikatora URI zakresu, który nie zawiera schematu i hosta.</span><span class="sxs-lookup"><span data-stu-id="fa406-139">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="fa406-140">Na przykład witryny Azure portal może zapewnić jeden z następujących formatów identyfikatora URI zakresu:</span><span class="sxs-lookup"><span data-stu-id="fa406-140">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="fa406-141">Podać identyfikator URI zakresu bez schematu i hosta:</span><span class="sxs-lookup"><span data-stu-id="fa406-141">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="fa406-142">Aby uzyskać więcej informacji, zobacz <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="fa406-142">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="fa406-143">Import pliku</span><span class="sxs-lookup"><span data-stu-id="fa406-143">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="fa406-144">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="fa406-144">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="fa406-145">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="fa406-145">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="fa406-146">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="fa406-146">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="fa406-147">Składnik LogowanieWydajnik</span><span class="sxs-lookup"><span data-stu-id="fa406-147">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="fa406-148">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="fa406-148">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="fa406-149">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="fa406-149">Additional resources</span></span>

* [<span data-ttu-id="fa406-150">Żądanie dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="fa406-150">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="fa406-151">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="fa406-151">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
