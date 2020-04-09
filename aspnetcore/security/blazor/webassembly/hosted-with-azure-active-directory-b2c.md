---
title: Zabezpieczanie hosta Blazor hostowanego przez ASP.NET Core aplikacji sieci Web za pomocą usługi Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 4c79f7530e18b9f70262812a64abb55122701d15
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977161"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="81591-102">Zabezpieczanie hosta Blazor hostowanego przez ASP.NET Core aplikacji sieci Web za pomocą usługi Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="81591-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="81591-103">Autorstwa [Javiera Calvarro Nelsona](https://github.com/javiercn) i [Luke'a Lathama](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="81591-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="81591-104">W tym artykule Blazor opisano sposób tworzenia autonomicznej aplikacji WebAssembly, która używa [usługi Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="81591-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="81591-105">Rejestrowanie aplikacji w UAD B2C i tworzenie rozwiązania</span><span class="sxs-lookup"><span data-stu-id="81591-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="81591-106">Tworzenie dzierżawy</span><span class="sxs-lookup"><span data-stu-id="81591-106">Create a tenant</span></span>

<span data-ttu-id="81591-107">Postępuj zgodnie ze wskazówkami zawartymi w [samouczku: Utwórz dzierżawę usługi Azure Active Directory B2C,](/azure/active-directory-b2c/tutorial-create-tenant) aby utworzyć dzierżawę usługi AAD B2C i zarejestrować następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="81591-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="81591-108">AAD B2C (na `https://contoso.b2clogin.com/`przykład , który zawiera ukośnik)</span><span class="sxs-lookup"><span data-stu-id="81591-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="81591-109">Domena dzierżawy AAD B2C (na `contoso.onmicrosoft.com`przykład)</span><span class="sxs-lookup"><span data-stu-id="81591-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="81591-110">Rejestrowanie aplikacji interfejsu API serwera</span><span class="sxs-lookup"><span data-stu-id="81591-110">Register a server API app</span></span>

<span data-ttu-id="81591-111">Postępuj zgodnie ze wskazówkami w [samouczku: Zarejestruj aplikację w usłudze Azure Active Directory B2C,](/azure/active-directory-b2c/tutorial-register-applications) aby zarejestrować aplikację usługi AAD dla *aplikacji interfejsu API serwera* w obszarze rejestracji**aplikacji** usługi Azure Active Directory w **witrynie** > Azure portal:</span><span class="sxs-lookup"><span data-stu-id="81591-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="81591-112">Wybierz **pozycję Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="81591-112">Select **New registration**.</span></span>
1. <span data-ttu-id="81591-113">Podaj **nazwę** aplikacji (na przykład \*\* Blazor Serwer AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="81591-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="81591-114">W przypadku **obsługiwanych typów kont**wybierz pozycję **Konta w dowolnym katalogu organizacji lub dowolnym dostawcy tożsamości. Do uwierzytelniania użytkowników za pomocą usługi Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="81591-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="81591-115">(multi-tenant) dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="81591-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="81591-116">*Aplikacja interfejsu API serwera* nie wymaga **identyfikatora URI przekierowania** w tym scenariuszu, więc pozostaw zestaw rozwijany do **sieci Web** i nie należy wprowadzać identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="81591-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="81591-117">Upewnij się, że **uprawnienia** > **Udzielić administratora concent do openid i offline_access uprawnienia** jest włączona.</span><span class="sxs-lookup"><span data-stu-id="81591-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="81591-118">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="81591-118">Select **Register**.</span></span>

<span data-ttu-id="81591-119">W **expose interfejsu API:**</span><span class="sxs-lookup"><span data-stu-id="81591-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="81591-120">Wybierz polecenie **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="81591-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="81591-121">Wybierz przycisk **Zapisz i kontynuuj**.</span><span class="sxs-lookup"><span data-stu-id="81591-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="81591-122">Podaj **nazwę zakresu** `API.Access`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="81591-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="81591-123">Podaj **nazwę wyświetlaną zgody administratora** (na przykład `Access API`).</span><span class="sxs-lookup"><span data-stu-id="81591-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="81591-124">Podaj **opis zgody administratora** (na `Allows the app to access server app API endpoints.`przykład).</span><span class="sxs-lookup"><span data-stu-id="81591-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="81591-125">Potwierdź, że **stan** jest ustawiony na **Włączone**.</span><span class="sxs-lookup"><span data-stu-id="81591-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="81591-126">Wybierz **pozycję Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="81591-126">Select **Add scope**.</span></span>

<span data-ttu-id="81591-127">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="81591-127">Record the following information:</span></span>

* <span data-ttu-id="81591-128">*Aplikacja interfejsu API serwera* Identyfikator aplikacji (identyfikator klienta) (na `11111111-1111-1111-1111-111111111111`przykład)</span><span class="sxs-lookup"><span data-stu-id="81591-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="81591-129">Identyfikator URI aplikacji (na `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`przykład , lub podana wartość niestandardowa)</span><span class="sxs-lookup"><span data-stu-id="81591-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="81591-130">Identyfikator katalogu (identyfikator dzierżawy) (na `222222222-2222-2222-2222-222222222222`przykład )</span><span class="sxs-lookup"><span data-stu-id="81591-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="81591-131">*Aplikacja interfejsu API serwera* Identyfikator URI identyfikatora aplikacji `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`(na przykład portal Azure może domyślnie wartość identyfikatora klienta)</span><span class="sxs-lookup"><span data-stu-id="81591-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="81591-132">Domyślny zakres (na przykład `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="81591-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="81591-133">Rejestrowanie aplikacji klienckiej</span><span class="sxs-lookup"><span data-stu-id="81591-133">Register a client app</span></span>

<span data-ttu-id="81591-134">Postępuj zgodnie ze wskazówkami w [samouczku: Zarejestruj aplikację w usłudze Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) ponownie, aby zarejestrować aplikację usługi AAD dla *aplikacji klienckiej* w obszarze**rejestracji aplikacji** usługi Azure Active Directory w **witrynie** > Azure portal:</span><span class="sxs-lookup"><span data-stu-id="81591-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="81591-135">Wybierz **pozycję Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="81591-135">Select **New registration**.</span></span>
1. <span data-ttu-id="81591-136">Podaj **nazwę** aplikacji (na przykład \*\* Blazor client AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="81591-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="81591-137">W przypadku **obsługiwanych typów kont**wybierz pozycję **Konta w dowolnym katalogu organizacji lub dowolnym dostawcy tożsamości. Do uwierzytelniania użytkowników za pomocą usługi Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="81591-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="81591-138">(multi-tenant) dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="81591-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="81591-139">Pozostaw zestaw rozwijany **Przekierowania identyfikatora URI** `https://localhost:5001/authentication/login-callback`do sieci **Web**i podaj identyfikator URI przekierowania .</span><span class="sxs-lookup"><span data-stu-id="81591-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="81591-140">Upewnij się, że **uprawnienia** > **Udzielić administratora concent do openid i offline_access uprawnienia** jest włączona.</span><span class="sxs-lookup"><span data-stu-id="81591-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="81591-141">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="81591-141">Select **Register**.</span></span>

<span data-ttu-id="81591-142">W**konfiguracji** > platformy **uwierzytelniania** > **w sieci Web:**</span><span class="sxs-lookup"><span data-stu-id="81591-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="81591-143">Upewnij się, że `https://localhost:5001/authentication/login-callback` identyfikator **URI przekierowania** jest obecny.</span><span class="sxs-lookup"><span data-stu-id="81591-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="81591-144">W przypadku **dotacji niejawnych**zaznacz pola wyboru **tokenów programu Access** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="81591-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="81591-145">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="81591-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="81591-146">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="81591-146">Select the **Save** button.</span></span>

<span data-ttu-id="81591-147">W **uprawnieniach interfejsu API:**</span><span class="sxs-lookup"><span data-stu-id="81591-147">In **API permissions**:</span></span>

1. <span data-ttu-id="81591-148">Upewnij się, że aplikacja ma uprawnienie **Microsoft Graph** > **User.Read.**</span><span class="sxs-lookup"><span data-stu-id="81591-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="81591-149">Wybierz **pozycję Dodaj uprawnienie,** a następnie **moje interfejsy API**.</span><span class="sxs-lookup"><span data-stu-id="81591-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="81591-150">Wybierz *aplikację Interfejsu API serwera* z kolumny **Nazwa** (na przykład \*\* Blazor Serwer AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="81591-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="81591-151">Otwórz listę **interfejsu API.**</span><span class="sxs-lookup"><span data-stu-id="81591-151">Open the **API** list.</span></span>
1. <span data-ttu-id="81591-152">Włącz dostęp do interfejsu API `API.Access`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="81591-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="81591-153">Wybierz pozycję **Dodaj uprawnienia**.</span><span class="sxs-lookup"><span data-stu-id="81591-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="81591-154">Wybierz **pozycję Przyznaj zawartość administratora dla przycisku {NAZWA NAJEMCY}.**</span><span class="sxs-lookup"><span data-stu-id="81591-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="81591-155">Wybierz pozycję **Tak**, aby potwierdzić.</span><span class="sxs-lookup"><span data-stu-id="81591-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="81591-156">W **domowej** > **usłudze Azure AD B2C** > **przepływy użytkowników:**</span><span class="sxs-lookup"><span data-stu-id="81591-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="81591-157">Tworzenie przepływu użytkowników rejestracji i logowania</span><span class="sxs-lookup"><span data-stu-id="81591-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="81591-158">Co najmniej wybierz atrybut Użytkownika Nazwa > **wyświetlana** **oświadczeń aplikacji,** `LoginDisplay` aby wypełnić `context.User.Identity.Name` składnik (*Shared/LoginDisplay.brzytwa*).</span><span class="sxs-lookup"><span data-stu-id="81591-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="81591-159">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="81591-159">Record the following information:</span></span>

* <span data-ttu-id="81591-160">Zarejestruj identyfikator aplikacji *klienckiej* (identyfikator klienta) `33333333-3333-3333-3333-333333333333`(na przykład ).</span><span class="sxs-lookup"><span data-stu-id="81591-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="81591-161">Nagraj nazwę przepływu użytkownika rejestracji i logowania utworzoną `B2C_1_signupsignin`dla aplikacji (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="81591-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="81591-162">Tworzymy aplikację.</span><span class="sxs-lookup"><span data-stu-id="81591-162">Create the app</span></span>

<span data-ttu-id="81591-163">Zastąp symbole zastępcze w następującym poleceniu informacjami zarejestrowanymi wcześniej i wykonaj polecenie w powłoce polecenia:</span><span class="sxs-lookup"><span data-stu-id="81591-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="81591-164">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, należy dołączyć opcję `-o BlazorSample`wyjściową w poleceniu ze ścieżką (na przykład ).</span><span class="sxs-lookup"><span data-stu-id="81591-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="81591-165">Nazwa folderu również staje się częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="81591-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="81591-166">Przekaż identyfikator URI identyfikatora `app-id-uri` aplikacji do opcji, ale zanotuj, że zmiana konfiguracji może być wymagana w aplikacji klienckiej, która jest opisana w sekcji [Zakresy tokenów dostępu.](#access-token-scopes)</span><span class="sxs-lookup"><span data-stu-id="81591-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="81591-167">Konfiguracja aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="81591-167">Server app configuration</span></span>

<span data-ttu-id="81591-168">*Ta sekcja dotyczy aplikacji **Server** rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="81591-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="81591-169">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="81591-169">Authentication package</span></span>

<span data-ttu-id="81591-170">Obsługa uwierzytelniania i autoryzowania wywołań ASP.NET core interfejsów API sieci `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`Web jest świadczona przez:</span><span class="sxs-lookup"><span data-stu-id="81591-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="81591-171">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="81591-171">Authentication service support</span></span>

<span data-ttu-id="81591-172">Metoda `AddAuthentication` konfiguruje usługi uwierzytelniania w aplikacji i konfiguruje program obsługi nośnika JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="81591-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="81591-173">Metoda `AddAzureADB2CBearer` konfiguruje określone parametry w programie obsługi nośnika JWT wymaganym do sprawdzania poprawności tokenów emitowanych przez usługę Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="81591-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="81591-174">`UseAuthentication`i `UseAuthorization` zapewnić, że:</span><span class="sxs-lookup"><span data-stu-id="81591-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="81591-175">Aplikacja próbuje przeanalizować i sprawdzić poprawność tokenów w żądaniach przychodzących.</span><span class="sxs-lookup"><span data-stu-id="81591-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="81591-176">Każde żądanie próbujące uzyskać dostęp do chronionego zasobu bez odpowiednich poświadczeń kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="81591-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="81591-177">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="81591-177">User.Identity.Name</span></span>

<span data-ttu-id="81591-178">Domyślnie `User.Identity.Name` nie jest wypełniona.</span><span class="sxs-lookup"><span data-stu-id="81591-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="81591-179">Aby skonfigurować aplikację do odbierania `name` wartości z typu oświadczenia, należy skonfigurować [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="81591-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="81591-180">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="81591-180">App settings</span></span>

<span data-ttu-id="81591-181">Plik *appsettings.json* zawiera opcje konfigurowania programu obsługi na okaziciela JWT używanego do sprawdzania poprawności tokenów dostępu.</span><span class="sxs-lookup"><span data-stu-id="81591-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="81591-182">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="81591-182">WeatherForecast controller</span></span>

<span data-ttu-id="81591-183">Kontroler WeatherForecast *(Controllers/WeatherForecastController.cs)* udostępnia chroniony interfejs `[Authorize]` API z atrybutem zastosowanym do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="81591-183">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="81591-184">**Ważne** jest, aby zrozumieć, że:</span><span class="sxs-lookup"><span data-stu-id="81591-184">It's **important** to understand that:</span></span>

* <span data-ttu-id="81591-185">Atrybut `[Authorize]` w tym kontrolerze interfejsu API jest jedyną rzeczą, która chroni ten interfejs API przed nieautoryzowanym dostępem.</span><span class="sxs-lookup"><span data-stu-id="81591-185">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="81591-186">Atrybut `[Authorize]` używany w Blazor aplikacji WebAssembly służy tylko jako wskazówka do aplikacji, że użytkownik powinien być autoryzowany dla aplikacji do poprawnego działania.</span><span class="sxs-lookup"><span data-stu-id="81591-186">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="81591-187">Konfiguracja aplikacji klienckiej</span><span class="sxs-lookup"><span data-stu-id="81591-187">Client app configuration</span></span>

<span data-ttu-id="81591-188">*Ta sekcja dotyczy aplikacji **klienta** rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="81591-188">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="81591-189">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="81591-189">Authentication package</span></span>

<span data-ttu-id="81591-190">Po utworzeniu aplikacji do korzystania z indywidualnego konta B2C (`IndividualB2C`aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania Firmy Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="81591-190">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="81591-191">Pakiet zawiera zestaw uwierzytelniania, które pomagają aplikacji uwierzytelniać użytkowników i uzyskać tokeny do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="81591-191">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="81591-192">Jeśli dodasz uwierzytelnianie do aplikacji, ręcznie dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="81591-192">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="81591-193">Zastąp `{VERSION}` w poprzednim odwołaniu `Microsoft.AspNetCore.Blazor.Templates` do pakietu <xref:blazor/get-started> wersją pakietu przedstawioną w artykule.</span><span class="sxs-lookup"><span data-stu-id="81591-193">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="81591-194">Pakiet `Microsoft.Authentication.WebAssembly.Msal` przechodnie dodaje `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakiet do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="81591-194">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="81591-195">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="81591-195">Authentication service support</span></span>

<span data-ttu-id="81591-196">Obsługa uwierzytelniania użytkowników jest zarejestrowana w `AddMsalAuthentication` kontenerze usługi `Microsoft.Authentication.WebAssembly.Msal` przy użyciu metody rozszerzenia dostarczonej przez pakiet.</span><span class="sxs-lookup"><span data-stu-id="81591-196">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="81591-197">Ta metoda konfiguruje wszystkie usługi wymagane dla aplikacji do interakcji z dostawcą tożsamości (IP).</span><span class="sxs-lookup"><span data-stu-id="81591-197">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="81591-198">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="81591-198">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="81591-199">Metoda `AddMsalAuthentication` akceptuje wywołanie zwrotne, aby skonfigurować parametry wymagane do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="81591-199">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="81591-200">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD usługi Azure Portal podczas rejestracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="81591-200">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="81591-201">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="81591-201">Access token scopes</span></span>

<span data-ttu-id="81591-202">Domyślne zakresy tokenów dostępu reprezentują listę zakresów tokenów dostępu, które są:</span><span class="sxs-lookup"><span data-stu-id="81591-202">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="81591-203">Domyślnie uwzględnione w żądaniu logowania.</span><span class="sxs-lookup"><span data-stu-id="81591-203">Included by default in the sign in request.</span></span>
* <span data-ttu-id="81591-204">Służy do inicjowania obsługi administracyjnej tokenu dostępu natychmiast po uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="81591-204">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="81591-205">Wszystkie zakresy muszą należeć do tej samej aplikacji zgodnie z regułami usługi Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="81591-205">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="81591-206">W razie potrzeby można dodać dodatkowe zakresy dla dodatkowych aplikacji interfejsu API:</span><span class="sxs-lookup"><span data-stu-id="81591-206">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="81591-207">Jeśli portal Azure udostępnia identyfikator URI zakresu i **aplikacja zgłasza nieobsługiwanie wyjątek** po otrzymaniu *401 nieautoryzowanej* odpowiedzi z interfejsu API, spróbuj użyć identyfikatora URI zakresu, który nie zawiera schematu i hosta.</span><span class="sxs-lookup"><span data-stu-id="81591-207">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="81591-208">Na przykład witryny Azure portal może zapewnić jeden z następujących formatów identyfikatora URI zakresu:</span><span class="sxs-lookup"><span data-stu-id="81591-208">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="81591-209">Podać identyfikator URI zakresu bez schematu i hosta:</span><span class="sxs-lookup"><span data-stu-id="81591-209">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="81591-210">Aby uzyskać więcej informacji, zobacz <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="81591-210">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="81591-211">Import pliku</span><span class="sxs-lookup"><span data-stu-id="81591-211">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="81591-212">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="81591-212">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="81591-213">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="81591-213">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="81591-214">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="81591-214">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="81591-215">Składnik LogowanieWydajnik</span><span class="sxs-lookup"><span data-stu-id="81591-215">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="81591-216">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="81591-216">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="81591-217">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="81591-217">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="81591-218">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="81591-218">Run the app</span></span>

<span data-ttu-id="81591-219">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="81591-219">Run the app from the Server project.</span></span> <span data-ttu-id="81591-220">Korzystając z programu Visual Studio, wybierz projekt serwera w **Eksploratorze rozwiązań** i wybierz przycisk **Uruchom** na pasku narzędzi lub uruchom aplikację z menu **debugowania.**</span><span class="sxs-lookup"><span data-stu-id="81591-220">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->
[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="81591-221">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="81591-221">Additional resources</span></span>

* [<span data-ttu-id="81591-222">Żądanie dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="81591-222">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="81591-223">Samouczek: tworzenie dzierżawy usługi Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="81591-223">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="81591-224">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="81591-224">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
