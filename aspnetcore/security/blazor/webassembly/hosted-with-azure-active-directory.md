---
title: Zabezpieczanie hostowanego Blazor przez ASP.NET core aplikacji sieci Web za pomocą usługi Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: a80be8d145b7c58be35e2c353a448db7e234e20b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661831"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="bfd8c-102">Zabezpieczanie hostowanego Blazor przez ASP.NET core aplikacji sieci Web za pomocą usługi Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="bfd8c-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="bfd8c-103">Autorstwa [Javiera Calvarro Nelsona](https://github.com/javiercn) i [Luke'a Lathama](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bfd8c-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="bfd8c-104">W tym artykule opisano sposób tworzenia [ Blazor aplikacji hostowanej webassembly,](xref:blazor/hosting-models#blazor-webassembly) która używa [usługi Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="bfd8c-105">Rejestrowanie aplikacji w UAD B2C i tworzenie rozwiązania</span><span class="sxs-lookup"><span data-stu-id="bfd8c-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="bfd8c-106">Tworzenie dzierżawy</span><span class="sxs-lookup"><span data-stu-id="bfd8c-106">Create a tenant</span></span>

<span data-ttu-id="bfd8c-107">Postępuj zgodnie ze wskazówkami w [przewodniku Szybki start: Konfigurowanie dzierżawy](/azure/active-directory/develop/quickstart-create-new-tenant) w celu utworzenia dzierżawy w ujrzętm dalej.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="bfd8c-108">Rejestrowanie aplikacji interfejsu API serwera</span><span class="sxs-lookup"><span data-stu-id="bfd8c-108">Register a server API app</span></span>

<span data-ttu-id="bfd8c-109">Postępuj zgodnie ze wskazówkami w [przewodniku Szybki start: Zarejestruj aplikację na platformie tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app) i kolejnych tematach usługi Azure AAD, aby zarejestrować aplikację usługi AAD dla *aplikacji interfejsu API serwera* w obszarze**rejestracji aplikacji** usługi Azure **Active Directory** > w portalu Azure portal:</span><span class="sxs-lookup"><span data-stu-id="bfd8c-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="bfd8c-110">Wybierz **pozycję Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-110">Select **New registration**.</span></span>
1. <span data-ttu-id="bfd8c-111">Podaj **nazwę** aplikacji (na przykład \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="bfd8c-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="bfd8c-112">Wybierz **typy obsługiwanych kont**.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="bfd8c-113">Dla tego środowiska można wybrać opcję **Konta w tym katalogu organizacyjnym (tylko** pojedyncza dzierżawa).</span><span class="sxs-lookup"><span data-stu-id="bfd8c-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="bfd8c-114">*Aplikacja interfejsu API serwera* nie wymaga **identyfikatora URI przekierowania** w tym scenariuszu, więc pozostaw zestaw rozwijany do **sieci Web** i nie należy wprowadzać identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="bfd8c-115">Wyłącz pole **wyboru Uprawnienia** > **udzielaj administracyjnej do otwierania i offline_access uprawnień.**</span><span class="sxs-lookup"><span data-stu-id="bfd8c-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="bfd8c-116">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-116">Select **Register**.</span></span>

<span data-ttu-id="bfd8c-117">W **uprawnieniach interfejsu API**usuń uprawnienie Microsoft **Graph** > **User.Read,** ponieważ aplikacja nie wymaga logowania ani dostępu do profilu uer.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="bfd8c-118">W **expose interfejsu API:**</span><span class="sxs-lookup"><span data-stu-id="bfd8c-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="bfd8c-119">Wybierz polecenie **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="bfd8c-120">Wybierz przycisk **Zapisz i kontynuuj**.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="bfd8c-121">Podaj **nazwę zakresu** `API.Access`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="bfd8c-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="bfd8c-122">Podaj **nazwę wyświetlaną zgody administratora** (na przykład `Access API`).</span><span class="sxs-lookup"><span data-stu-id="bfd8c-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="bfd8c-123">Podaj **opis zgody administratora** (na `Allows the app to access server app API endpoints.`przykład).</span><span class="sxs-lookup"><span data-stu-id="bfd8c-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="bfd8c-124">Potwierdź, że **stan** jest ustawiony na **Włączone**.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="bfd8c-125">Wybierz **pozycję Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-125">Select **Add scope**.</span></span>

<span data-ttu-id="bfd8c-126">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="bfd8c-126">Record the following information:</span></span>

* <span data-ttu-id="bfd8c-127">*Aplikacja interfejsu API serwera* Identyfikator aplikacji (identyfikator klienta) (na `11111111-1111-1111-1111-111111111111`przykład)</span><span class="sxs-lookup"><span data-stu-id="bfd8c-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="bfd8c-128">Identyfikator URI aplikacji (na `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`przykład , lub podana wartość niestandardowa)</span><span class="sxs-lookup"><span data-stu-id="bfd8c-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="bfd8c-129">Identyfikator katalogu (identyfikator dzierżawy) (na `222222222-2222-2222-2222-222222222222`przykład )</span><span class="sxs-lookup"><span data-stu-id="bfd8c-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="bfd8c-130">Domena dzierżawy usługi AAD (na przykład `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="bfd8c-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="bfd8c-131">Domyślny zakres (na przykład `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="bfd8c-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="bfd8c-132">Rejestrowanie aplikacji klienckiej</span><span class="sxs-lookup"><span data-stu-id="bfd8c-132">Register a client app</span></span>

<span data-ttu-id="bfd8c-133">Postępuj zgodnie ze wskazówkami w [przewodniku Szybki start: Zarejestruj aplikację za pomocą platformy tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app) i kolejnych tematów usługi Azure AAD, aby zarejestrować aplikację usługi AAD dla aplikacji *klienta* w obszarze**rejestracji aplikacji** usługi Azure **Active Directory** > w portalu Azure:</span><span class="sxs-lookup"><span data-stu-id="bfd8c-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="bfd8c-134">Wybierz **pozycję Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-134">Select **New registration**.</span></span>
1. <span data-ttu-id="bfd8c-135">Podaj **nazwę** aplikacji (na przykład \*\* Blazor client AAD).\*\*</span><span class="sxs-lookup"><span data-stu-id="bfd8c-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="bfd8c-136">Wybierz **typy obsługiwanych kont**.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="bfd8c-137">Dla tego środowiska można wybrać opcję **Konta w tym katalogu organizacyjnym (tylko** pojedyncza dzierżawa).</span><span class="sxs-lookup"><span data-stu-id="bfd8c-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="bfd8c-138">Pozostaw zestaw rozwijany **Przekierowania identyfikatora URI** `https://localhost:5001/authentication/login-callback`do sieci **Web**i podaj identyfikator URI przekierowania .</span><span class="sxs-lookup"><span data-stu-id="bfd8c-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="bfd8c-139">Wyłącz pole **wyboru Uprawnienia** > **udzielaj administracyjnej do otwierania i offline_access uprawnień.**</span><span class="sxs-lookup"><span data-stu-id="bfd8c-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="bfd8c-140">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-140">Select **Register**.</span></span>

<span data-ttu-id="bfd8c-141">W**konfiguracji** > platformy **uwierzytelniania** > **w sieci Web:**</span><span class="sxs-lookup"><span data-stu-id="bfd8c-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="bfd8c-142">Upewnij się, że `https://localhost:5001/authentication/login-callback` identyfikator **URI przekierowania** jest obecny.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="bfd8c-143">W przypadku **dotacji niejawnych**zaznacz pola wyboru **tokenów programu Access** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="bfd8c-144">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="bfd8c-145">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-145">Select the **Save** button.</span></span>

<span data-ttu-id="bfd8c-146">W **uprawnieniach interfejsu API:**</span><span class="sxs-lookup"><span data-stu-id="bfd8c-146">In **API permissions**:</span></span>

1. <span data-ttu-id="bfd8c-147">Upewnij się, że aplikacja ma uprawnienie **Microsoft Graph** > **User.Read.**</span><span class="sxs-lookup"><span data-stu-id="bfd8c-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="bfd8c-148">Wybierz **pozycję Dodaj uprawnienie,** a następnie **moje interfejsy API**.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="bfd8c-149">Wybierz *aplikację Interfejsu API serwera* z kolumny **Nazwa** (na przykład \*\* Blazor Serwer AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="bfd8c-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="bfd8c-150">Otwórz listę **interfejsu API.**</span><span class="sxs-lookup"><span data-stu-id="bfd8c-150">Open the **API** list.</span></span>
1. <span data-ttu-id="bfd8c-151">Włącz dostęp do interfejsu API `API.Access`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="bfd8c-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="bfd8c-152">Wybierz pozycję **Dodaj uprawnienia**.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="bfd8c-153">Wybierz **pozycję Przyznaj zawartość administratora dla przycisku {NAZWA NAJEMCY}.**</span><span class="sxs-lookup"><span data-stu-id="bfd8c-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="bfd8c-154">Wybierz pozycję **Tak**, aby potwierdzić.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="bfd8c-155">Zarejestruj identyfikator aplikacji *klienckiej* (identyfikator klienta) `33333333-3333-3333-3333-333333333333`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="bfd8c-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="bfd8c-156">Tworzymy aplikację.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-156">Create the app</span></span>

<span data-ttu-id="bfd8c-157">Zastąp symbole zastępcze w następującym poleceniu informacjami zarejestrowanymi wcześniej i wykonaj polecenie w powłoce polecenia:</span><span class="sxs-lookup"><span data-stu-id="bfd8c-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="bfd8c-158">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, należy dołączyć opcję `-o BlazorSample`wyjściową w poleceniu ze ścieżką (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="bfd8c-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="bfd8c-159">Nazwa folderu również staje się częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="bfd8c-160">Przekaż identyfikator URI identyfikatora `app-id-uri` aplikacji do opcji, ale zanotuj, że zmiana konfiguracji może być wymagana w aplikacji klienckiej, która jest opisana w sekcji [Zakresy tokenów dostępu.](#access-token-scopes)</span><span class="sxs-lookup"><span data-stu-id="bfd8c-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="bfd8c-161">Konfiguracja aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="bfd8c-161">Server app configuration</span></span>

<span data-ttu-id="bfd8c-162">*Ta sekcja dotyczy aplikacji **Server** rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="bfd8c-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="bfd8c-163">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="bfd8c-163">Authentication package</span></span>

<span data-ttu-id="bfd8c-164">Obsługa uwierzytelniania i autoryzowania wywołań ASP.NET core interfejsów API sieci `Microsoft.AspNetCore.Authentication.AzureAD.UI`Web jest świadczona przez:</span><span class="sxs-lookup"><span data-stu-id="bfd8c-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="bfd8c-165">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="bfd8c-165">Authentication service support</span></span>

<span data-ttu-id="bfd8c-166">Metoda `AddAuthentication` konfiguruje usługi uwierzytelniania w aplikacji i konfiguruje program obsługi nośnika JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="bfd8c-167">Metoda `AddAzureADBearer` konfiguruje określone parametry w programie obsługi nośnika JWT wymaganym do sprawdzania poprawności tokenów emitowanych przez usługę Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="bfd8c-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="bfd8c-168">`UseAuthentication`i `UseAuthorization` zapewnić, że:</span><span class="sxs-lookup"><span data-stu-id="bfd8c-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="bfd8c-169">Aplikacja próbuje przeanalizować i sprawdzić poprawność tokenów w żądaniach przychodzących.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="bfd8c-170">Każde żądanie próbujące uzyskać dostęp do chronionego zasobu bez odpowiednich poświadczeń kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="bfd8c-171">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="bfd8c-171">User.Identity.Name</span></span>

<span data-ttu-id="bfd8c-172">Domyślnie interfejs API aplikacji serwera `User.Identity.Name` wypełnia wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` z typu oświadczenia `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="bfd8c-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="bfd8c-173">Aby skonfigurować aplikację do odbierania `name` wartości z typu oświadczenia, należy skonfigurować [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="bfd8c-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="bfd8c-174">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="bfd8c-174">App settings</span></span>

<span data-ttu-id="bfd8c-175">Plik *appsettings.json* zawiera opcje konfigurowania programu obsługi na okaziciela JWT używanego do sprawdzania poprawności tokenów dostępu.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="bfd8c-176">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="bfd8c-176">WeatherForecast controller</span></span>

<span data-ttu-id="bfd8c-177">Kontroler WeatherForecast *(Controllers/WeatherForecastController.cs)* udostępnia chroniony interfejs `[Authorize]` API z atrybutem zastosowanym do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-177">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="bfd8c-178">**Ważne** jest, aby zrozumieć, że:</span><span class="sxs-lookup"><span data-stu-id="bfd8c-178">It's **important** to understand that:</span></span>

* <span data-ttu-id="bfd8c-179">Atrybut `[Authorize]` w tym kontrolerze interfejsu API jest jedyną rzeczą, która chroni ten interfejs API przed nieautoryzowanym dostępem.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-179">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="bfd8c-180">Atrybut `[Authorize]` używany w Blazor aplikacji WebAssembly służy tylko jako wskazówka do aplikacji, że użytkownik powinien być autoryzowany dla aplikacji do poprawnego działania.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-180">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="bfd8c-181">Konfiguracja aplikacji klienckiej</span><span class="sxs-lookup"><span data-stu-id="bfd8c-181">Client app configuration</span></span>

<span data-ttu-id="bfd8c-182">*Ta sekcja dotyczy aplikacji **klienta** rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="bfd8c-182">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="bfd8c-183">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="bfd8c-183">Authentication package</span></span>

<span data-ttu-id="bfd8c-184">Po utworzeniu aplikacji do używania`SingleOrg`kont służbowych ( aplikacja automatycznie otrzymuje odwołanie`Microsoft.Authentication.WebAssembly.Msal`do pakietu dla [biblioteki uwierzytelniania Firmy Microsoft](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="bfd8c-184">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="bfd8c-185">Pakiet zawiera zestaw uwierzytelniania, które pomagają aplikacji uwierzytelniać użytkowników i uzyskać tokeny do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-185">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="bfd8c-186">Jeśli dodasz uwierzytelnianie do aplikacji, ręcznie dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="bfd8c-186">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="bfd8c-187">Zastąp `{VERSION}` w poprzednim odwołaniu `Microsoft.AspNetCore.Blazor.Templates` do pakietu <xref:blazor/get-started> wersją pakietu przedstawioną w artykule.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-187">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="bfd8c-188">Pakiet `Microsoft.Authentication.WebAssembly.Msal` przechodnie dodaje `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakiet do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-188">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="bfd8c-189">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="bfd8c-189">Authentication service support</span></span>

<span data-ttu-id="bfd8c-190">Obsługa uwierzytelniania użytkowników jest zarejestrowana w `AddMsalAuthentication` kontenerze usługi `Microsoft.Authentication.WebAssembly.Msal` przy użyciu metody rozszerzenia dostarczonej przez pakiet.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-190">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="bfd8c-191">Ta metoda konfiguruje wszystkie usługi wymagane dla aplikacji do interakcji z dostawcą tożsamości (IP).</span><span class="sxs-lookup"><span data-stu-id="bfd8c-191">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="bfd8c-192">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="bfd8c-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="bfd8c-193">Metoda `AddMsalAuthentication` akceptuje wywołanie zwrotne, aby skonfigurować parametry wymagane do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-193">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="bfd8c-194">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD usługi Azure Portal podczas rejestracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-194">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="bfd8c-195">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="bfd8c-195">Access token scopes</span></span>

<span data-ttu-id="bfd8c-196">Domyślne zakresy tokenów dostępu reprezentują listę zakresów tokenów dostępu, które są:</span><span class="sxs-lookup"><span data-stu-id="bfd8c-196">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="bfd8c-197">Domyślnie uwzględnione w żądaniu logowania.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-197">Included by default in the sign in request.</span></span>
* <span data-ttu-id="bfd8c-198">Służy do inicjowania obsługi administracyjnej tokenu dostępu natychmiast po uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-198">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="bfd8c-199">Wszystkie zakresy muszą należeć do tej samej aplikacji zgodnie z regułami usługi Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-199">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="bfd8c-200">W razie potrzeby można dodać dodatkowe zakresy dla dodatkowych aplikacji interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="bfd8c-200">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="bfd8c-201">Jeśli portal Azure udostępnia identyfikator URI zakresu i **aplikacja zgłasza nieobsługiwanie wyjątek** po otrzymaniu *401 nieautoryzowanej* odpowiedzi z interfejsu API, spróbuj użyć identyfikatora URI zakresu, który nie zawiera schematu i hosta.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-201">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="bfd8c-202">Na przykład witryny Azure portal może zapewnić jeden z następujących formatów identyfikatora URI zakresu:</span><span class="sxs-lookup"><span data-stu-id="bfd8c-202">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="bfd8c-203">Podać identyfikator URI zakresu bez schematu i hosta:</span><span class="sxs-lookup"><span data-stu-id="bfd8c-203">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="bfd8c-204">Aby uzyskać więcej informacji, zobacz <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-204">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="bfd8c-205">Import pliku</span><span class="sxs-lookup"><span data-stu-id="bfd8c-205">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="bfd8c-206">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="bfd8c-206">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="bfd8c-207">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="bfd8c-207">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="bfd8c-208">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="bfd8c-208">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="bfd8c-209">Składnik LogowanieWydajnik</span><span class="sxs-lookup"><span data-stu-id="bfd8c-209">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="bfd8c-210">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="bfd8c-210">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="bfd8c-211">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="bfd8c-211">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="bfd8c-212">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="bfd8c-212">Run the app</span></span>

<span data-ttu-id="bfd8c-213">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="bfd8c-213">Run the app from the Server project.</span></span> <span data-ttu-id="bfd8c-214">Korzystając z programu Visual Studio, wybierz projekt serwera w **Eksploratorze rozwiązań** i wybierz przycisk **Uruchom** na pasku narzędzi lub uruchom aplikację z menu **debugowania.**</span><span class="sxs-lookup"><span data-stu-id="bfd8c-214">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="bfd8c-215">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="bfd8c-215">Additional resources</span></span>

* [<span data-ttu-id="bfd8c-216">Żądanie dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="bfd8c-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="bfd8c-217">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="bfd8c-217">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
