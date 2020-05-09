---
title: Zabezpiecz aplikację hostowaną ASP.NET Core Blazor webassembly przy użyciu Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: aaae2b755d6d6e74db0cb7676820d01964c2add4
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976808"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="1b0e8-102">Zabezpiecz aplikację hostowaną ASP.NET Core Blazor webassembly przy użyciu Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="1b0e8-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="1b0e8-103">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1b0e8-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="1b0e8-104">W tym artykule opisano sposób tworzenia [ Blazor aplikacji hostowanej w programie webassembly](xref:blazor/hosting-models#blazor-webassembly) , która używa usługi [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="1b0e8-105">Rejestrowanie aplikacji w AAD B2C i tworzenie rozwiązania</span><span class="sxs-lookup"><span data-stu-id="1b0e8-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="1b0e8-106">Tworzenie dzierżawy</span><span class="sxs-lookup"><span data-stu-id="1b0e8-106">Create a tenant</span></span>

<span data-ttu-id="1b0e8-107">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Konfigurowanie dzierżawy](/azure/active-directory/develop/quickstart-create-new-tenant) w celu utworzenia dzierżawy w usłudze AAD.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="1b0e8-108">Rejestrowanie aplikacji interfejsu API serwera</span><span class="sxs-lookup"><span data-stu-id="1b0e8-108">Register a server API app</span></span>

<span data-ttu-id="1b0e8-109">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Zarejestruj aplikację przy użyciu platformy tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app) i kolejnych tematów usługi Azure AAD, aby zarejestrować aplikację w usłudze AAD dla *aplikacji interfejsu API serwera* w obszarze **Azure Active Directory** > **rejestracje aplikacji** w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="1b0e8-110">Wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-110">Select **New registration**.</span></span>
1. <span data-ttu-id="1b0e8-111">Podaj **nazwę** aplikacji (na przykład \*\* Blazor serwer AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="1b0e8-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="1b0e8-112">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="1b0e8-113">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="1b0e8-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="1b0e8-114">*Aplikacja interfejsu API serwera* nie wymaga **identyfikatora URI przekierowania** w tym scenariuszu, więc pozostaw listę rozwijaną w **sieci Web** i nie wprowadzaj identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="1b0e8-115">Wyłącz pole wyboru**Przyznaj administratorowi uprawnienia do OpenID Connect i uprawnień offline_access** . **Permissions** > </span><span class="sxs-lookup"><span data-stu-id="1b0e8-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="1b0e8-116">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-116">Select **Register**.</span></span>

<span data-ttu-id="1b0e8-117">W obszarze **uprawnienia interfejsu API**usuń uprawnienie **Microsoft Graph** > **User. Read** , ponieważ aplikacja nie wymaga dostępu do profilu Logowanie lub UER.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="1b0e8-118">W obszarze **Uwidacznianie interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="1b0e8-119">Wybierz polecenie **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="1b0e8-120">Wybierz przycisk **Zapisz i kontynuuj**.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="1b0e8-121">Podaj **nazwę zakresu** (na przykład `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="1b0e8-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="1b0e8-122">Podaj **nazwę wyświetlaną zgody administratora** (na przykład `Access API`).</span><span class="sxs-lookup"><span data-stu-id="1b0e8-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="1b0e8-123">Podaj **Opis zgody administratora** (na przykład `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="1b0e8-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="1b0e8-124">Upewnij się, że **stan** jest ustawiony na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="1b0e8-125">Wybierz pozycję **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-125">Select **Add scope**.</span></span>

<span data-ttu-id="1b0e8-126">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-126">Record the following information:</span></span>

* <span data-ttu-id="1b0e8-127">*Aplikacja interfejsu API serwera* Identyfikator aplikacji (identyfikator klienta) (na przykład `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="1b0e8-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="1b0e8-128">Identyfikator URI identyfikatora aplikacji (na przykład `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`, lub podana wartość niestandardowa)</span><span class="sxs-lookup"><span data-stu-id="1b0e8-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="1b0e8-129">Identyfikator katalogu (identyfikator dzierżawy) (na przykład `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="1b0e8-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="1b0e8-130">Domena dzierżawy usługi AAD (na `contoso.onmicrosoft.com`przykład)</span><span class="sxs-lookup"><span data-stu-id="1b0e8-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="1b0e8-131">Zakres domyślny (na przykład `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="1b0e8-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="1b0e8-132">Rejestrowanie aplikacji klienckiej</span><span class="sxs-lookup"><span data-stu-id="1b0e8-132">Register a client app</span></span>

<span data-ttu-id="1b0e8-133">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Zarejestruj aplikację przy użyciu platformy tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app) i kolejnych tematów usługi Azure AAD, aby zarejestrować aplikację w usłudze AAD dla *aplikacji klienckiej* w obszarze **Azure Active Directory** > **rejestracje aplikacji** w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="1b0e8-134">Wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-134">Select **New registration**.</span></span>
1. <span data-ttu-id="1b0e8-135">Podaj **nazwę** aplikacji (na przykład \*\* Blazor klienta AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="1b0e8-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="1b0e8-136">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="1b0e8-137">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="1b0e8-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="1b0e8-138">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** na **Sieć Web**i podaj identyfikator URI `https://localhost:5001/authentication/login-callback`przekierowania.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="1b0e8-139">Wyłącz pole wyboru**Przyznaj administratorowi uprawnienia do OpenID Connect i uprawnień offline_access** . **Permissions** > </span><span class="sxs-lookup"><span data-stu-id="1b0e8-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="1b0e8-140">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-140">Select **Register**.</span></span>

<span data-ttu-id="1b0e8-141">W obszarze**konfiguracje** > platformy **uwierzytelniania** > w**sieci Web**:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="1b0e8-142">Upewnij się, że `https://localhost:5001/authentication/login-callback` jest obecny **Identyfikator URI przekierowania** .</span><span class="sxs-lookup"><span data-stu-id="1b0e8-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="1b0e8-143">W przypadku **niejawnego przydzielenia**zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="1b0e8-144">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="1b0e8-145">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-145">Select the **Save** button.</span></span>

<span data-ttu-id="1b0e8-146">W **uprawnienia interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-146">In **API permissions**:</span></span>

1. <span data-ttu-id="1b0e8-147">Upewnij się, że aplikacja ma **Microsoft Graph** > uprawnienie**użytkownika. odczyt** .</span><span class="sxs-lookup"><span data-stu-id="1b0e8-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="1b0e8-148">Wybierz pozycję **Dodaj uprawnienia** , a następnie **Moje interfejsy API**.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="1b0e8-149">Wybierz *aplikację interfejsu API serwera* z kolumny **Nazwa** (na przykład \*\* Blazor serwer AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="1b0e8-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="1b0e8-150">Otwórz listę **interfejsów API** .</span><span class="sxs-lookup"><span data-stu-id="1b0e8-150">Open the **API** list.</span></span>
1. <span data-ttu-id="1b0e8-151">Włącz dostęp do interfejsu API (na przykład `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="1b0e8-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="1b0e8-152">Wybierz pozycję **Dodaj uprawnienia**.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="1b0e8-153">Wybierz przycisk **Udziel zawartości administratora dla {Nazwa dzierżawy}** .</span><span class="sxs-lookup"><span data-stu-id="1b0e8-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="1b0e8-154">Kliknij przycisk **Tak**, aby potwierdzić.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="1b0e8-155">Zapisz identyfikator aplikacji *klienta* (identyfikator klienta) (na przykład `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="1b0e8-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="1b0e8-156">Tworzymy aplikację.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-156">Create the app</span></span>

<span data-ttu-id="1b0e8-157">Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="1b0e8-158">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, Uwzględnij opcję Output w poleceniu z ścieżką (na przykład `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="1b0e8-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="1b0e8-159">Nazwa folderu jest również częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="1b0e8-160">Przekaż identyfikator URI aplikacji do `app-id-uri` opcji, ale Uwaga w aplikacji klienckiej może być wymagana zmiana konfiguracji, która jest opisana w sekcji [zakresy tokenu dostępu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="1b0e8-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="1b0e8-161">Konfiguracja aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="1b0e8-161">Server app configuration</span></span>

<span data-ttu-id="1b0e8-162">*Ta sekcja dotyczy aplikacji **serwerowej** rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="1b0e8-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="1b0e8-163">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1b0e8-163">Authentication package</span></span>

<span data-ttu-id="1b0e8-164">Obsługa uwierzytelniania i autoryzowania wywołań ASP.NET Core interfejsów API sieci Web jest zapewniana przez `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="1b0e8-165">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1b0e8-165">Authentication service support</span></span>

<span data-ttu-id="1b0e8-166">`AddAuthentication` Metoda konfiguruje usługi uwierzytelniania w ramach aplikacji i konfiguruje procedurę obsługi okaziciela JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="1b0e8-167">`AddAzureADBearer` Metoda ustawia określone parametry w obsłudze okaziciela JWT wymagane do weryfikacji tokenów emitowanych przez Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="1b0e8-168">`UseAuthentication`i `UseAuthorization` upewnij się, że:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="1b0e8-169">Aplikacja próbuje analizować tokeny i sprawdzać ich poprawność w żądaniach przychodzących.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="1b0e8-170">Wszystkie żądania próbujące uzyskać dostęp do chronionego zasobu bez poprawnego poświadczenia nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="1b0e8-171">Użytkownicy. Identity. Nazwij</span><span class="sxs-lookup"><span data-stu-id="1b0e8-171">User.Identity.Name</span></span>

<span data-ttu-id="1b0e8-172">Domyślnie interfejs API aplikacji serwera wypełnia `User.Identity.Name` wartość z `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` typu "typ" (na przykład `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span><span class="sxs-lookup"><span data-stu-id="1b0e8-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="1b0e8-173">`name` Aby skonfigurować aplikację do odbierania wartości z typu, należy skonfigurować [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w programie: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="1b0e8-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="1b0e8-174">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="1b0e8-174">App settings</span></span>

<span data-ttu-id="1b0e8-175">Plik *appSettings. JSON* zawiera opcje konfigurowania procedury obsługi okaziciela JWT używanej do sprawdzania poprawności tokenów dostępu.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="1b0e8-176">Przykład:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-176">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="1b0e8-177">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="1b0e8-177">WeatherForecast controller</span></span>

<span data-ttu-id="1b0e8-178">Kontroler WeatherForecast (*controllers/WeatherForecastController. cs*) ujawnia chroniony interfejs API z `[Authorize]` atrybutem zastosowanym do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-178">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="1b0e8-179">**Ważne** jest, aby zrozumieć, że:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-179">It's **important** to understand that:</span></span>

* <span data-ttu-id="1b0e8-180">`[Authorize]` Atrybut w tym kontrolerze interfejsu API jest jedynym warunkiem, że ten interfejs API jest chroniony przed nieautoryzowanym dostępem.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-180">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="1b0e8-181">`[Authorize]` Atrybut używany w Blazor aplikacji webassembly służy tylko jako Wskazówka dla aplikacji, którą użytkownik powinien mieć autoryzację, aby aplikacja działała poprawnie.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-181">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="1b0e8-182">Konfiguracja aplikacji klienta</span><span class="sxs-lookup"><span data-stu-id="1b0e8-182">Client app configuration</span></span>

<span data-ttu-id="1b0e8-183">*Ta sekcja dotyczy aplikacji **klienckiej** rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="1b0e8-183">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="1b0e8-184">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1b0e8-184">Authentication package</span></span>

<span data-ttu-id="1b0e8-185">Gdy aplikacja zostanie utworzona w celu korzystania z kont służbowych (`SingleOrg`), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="1b0e8-185">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="1b0e8-186">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-186">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="1b0e8-187">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-187">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="1b0e8-188">Zastąp `{VERSION}` odwołanie do poprzedniego pakietu wersją `Microsoft.AspNetCore.Blazor.Templates` pakietu pokazanego w <xref:blazor/get-started> artykule.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-188">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="1b0e8-189">Pakiet `Microsoft.Authentication.WebAssembly.Msal` zawiera przechodnie Dodawanie `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-189">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="1b0e8-190">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1b0e8-190">Authentication service support</span></span>

<span data-ttu-id="1b0e8-191">Dodano obsługę `HttpClient` wystąpień, które obejmują tokeny dostępu podczas wykonywania żądań do projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-191">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="1b0e8-192">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="1b0e8-193">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu metody `AddMsalAuthentication` rozszerzenia dostarczonej przez `Microsoft.Authentication.WebAssembly.Msal` pakiet.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-193">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="1b0e8-194">Ta metoda umożliwia skonfigurowanie wszystkich usług wymaganych przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="1b0e8-194">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="1b0e8-195">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-195">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="1b0e8-196">`AddMsalAuthentication` Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-196">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="1b0e8-197">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD w witrynie Azure Portal podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-197">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="1b0e8-198">Konfiguracja jest dostarczana przez plik *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="1b0e8-198">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="1b0e8-199">Przykład:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-199">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="1b0e8-200">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="1b0e8-200">Access token scopes</span></span>

<span data-ttu-id="1b0e8-201">Domyślne zakresy tokenów dostępu reprezentują listę zakresów tokenów dostępu, które są następujące:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-201">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="1b0e8-202">Uwzględnione domyślnie w żądaniu logowania.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-202">Included by default in the sign in request.</span></span>
* <span data-ttu-id="1b0e8-203">Służy do aprowizacji tokenu dostępu zaraz po uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-203">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="1b0e8-204">Wszystkie zakresy muszą należeć do tej samej aplikacji na Azure Active Directory reguł.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-204">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="1b0e8-205">Dodatkowe zakresy można dodać do dodatkowych aplikacji interfejsu API w razie potrzeby:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-205">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="1b0e8-206">Jeśli Azure Portal udostępnia identyfikator URI zakresu, a **aplikacja zgłasza nieobsłużony wyjątek** , gdy odbierze *401 nieautoryzowaną* odpowiedź z interfejsu API, spróbuj użyć identyfikatora URI zakresu, który nie zawiera schematu i hosta.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-206">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="1b0e8-207">Na przykład Azure Portal może podać jeden z następujących formatów identyfikatorów URI zakresu:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-207">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="1b0e8-208">Podaj identyfikator URI zakresu bez schematu i hosta:</span><span class="sxs-lookup"><span data-stu-id="1b0e8-208">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="1b0e8-209">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="1b0e8-209">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="1b0e8-210">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="1b0e8-210">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="1b0e8-211">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="1b0e8-211">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="1b0e8-212">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="1b0e8-212">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="1b0e8-213">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="1b0e8-213">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="1b0e8-214">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="1b0e8-214">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="1b0e8-215">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="1b0e8-215">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="1b0e8-216">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="1b0e8-216">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="1b0e8-217">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="1b0e8-217">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="1b0e8-218">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="1b0e8-218">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="1b0e8-219">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="1b0e8-219">Run the app</span></span>

<span data-ttu-id="1b0e8-220">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="1b0e8-220">Run the app from the Server project.</span></span> <span data-ttu-id="1b0e8-221">W przypadku korzystania z programu Visual Studio wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .</span><span class="sxs-lookup"><span data-stu-id="1b0e8-221">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="1b0e8-222">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="1b0e8-222">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="1b0e8-223">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="1b0e8-223">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
