---
title: Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core przy użyciu Azure Active Directory B2C
author: guardrex
description: Dowiedz się, jak zabezpieczyć Blazor WebAssembly aplikację hostowaną ASP.NET Core przy użyciu Azure Active Directory B2C.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: adc45293a6dfd324c12482d2dfffdeaa25eee4a3
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712444"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="d0a35-103">Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core przy użyciu Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="d0a35-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="d0a35-104">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d0a35-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d0a35-105">W tym artykule opisano sposób tworzenia [hostowanej Blazor WebAssembly aplikacji](xref:blazor/hosting-models#blazor-webassembly) korzystającej z usługi [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) na potrzeby uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="d0a35-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="d0a35-106">Rejestrowanie aplikacji w AAD B2C i tworzenie rozwiązania</span><span class="sxs-lookup"><span data-stu-id="d0a35-106">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="d0a35-107">Tworzenie dzierżawy</span><span class="sxs-lookup"><span data-stu-id="d0a35-107">Create a tenant</span></span>

<span data-ttu-id="d0a35-108">Postępuj zgodnie ze wskazówkami w [samouczku: Tworzenie dzierżawy Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) , aby utworzyć dzierżawę AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="d0a35-108">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="d0a35-109">Zapisz wystąpienie AAD B2C (na przykład, w `https://contoso.b2clogin.com/` którym znajduje się ukośnik końcowy).</span><span class="sxs-lookup"><span data-stu-id="d0a35-109">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="d0a35-110">Wystąpienie jest schematem i hostem rejestracji aplikacji Azure B2C, którą można znaleźć, otwierając okno **punkty końcowe** ze strony **rejestracje aplikacji** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="d0a35-110">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="d0a35-111">Rejestrowanie aplikacji interfejsu API serwera</span><span class="sxs-lookup"><span data-stu-id="d0a35-111">Register a server API app</span></span>

<span data-ttu-id="d0a35-112">Postępuj zgodnie ze wskazówkami w [samouczku: Zarejestruj aplikację w Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) , aby zarejestrować aplikację usługi AAD dla *aplikacji interfejsu API serwera* , a następnie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="d0a35-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="d0a35-113">W **Azure Active Directory**  >  **rejestracje aplikacji**wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="d0a35-113">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="d0a35-114">Podaj **nazwę** aplikacji (na przykład \*\* Blazor Server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="d0a35-114">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="d0a35-115">W przypadku **obsługiwanych typów kont**wybierz opcję wiele dzierżawców: **konta w dowolnym katalogu organizacyjnym lub dowolnego dostawcę tożsamości. Do uwierzytelniania użytkowników za pomocą Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="d0a35-115">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="d0a35-116">*Aplikacja interfejsu API serwera* nie wymaga **identyfikatora URI przekierowania** w tym scenariuszu, więc pozostaw listę rozwijaną w **sieci Web** i nie wprowadzaj identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="d0a35-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="d0a35-117">Upewnij się, że **uprawnienia**  >  **udzielają zgody administratora na OpenID Connect, a uprawnienia offline_access** są włączone.</span><span class="sxs-lookup"><span data-stu-id="d0a35-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="d0a35-118">Wybierz pozycję **Rejestruj**.</span><span class="sxs-lookup"><span data-stu-id="d0a35-118">Select **Register**.</span></span>

<span data-ttu-id="d0a35-119">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="d0a35-119">Record the following information:</span></span>

* <span data-ttu-id="d0a35-120">*Aplikacja interfejsu API serwera* Identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="d0a35-120">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="d0a35-121">Domena podstawowa/Wydawca/dzierżawa usługi AAD (na przykład `contoso.onmicrosoft.com` ): domena jest dostępna jako **domena wydawcy** w bloku **znakowania** Azure Portal dla zarejestrowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d0a35-121">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="d0a35-122">W obszarze **Uwidacznianie interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="d0a35-122">In **Expose an API**:</span></span>

1. <span data-ttu-id="d0a35-123">Wybierz polecenie **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="d0a35-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="d0a35-124">Wybierz przycisk **Zapisz i kontynuuj**.</span><span class="sxs-lookup"><span data-stu-id="d0a35-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="d0a35-125">Podaj **nazwę zakresu** (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="d0a35-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="d0a35-126">Podaj **nazwę wyświetlaną zgody administratora** (na przykład `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="d0a35-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="d0a35-127">Podaj **Opis zgody administratora** (na przykład `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="d0a35-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="d0a35-128">Upewnij się, że **stan** jest ustawiony na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="d0a35-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="d0a35-129">Wybierz pozycję **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="d0a35-129">Select **Add scope**.</span></span>

<span data-ttu-id="d0a35-130">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="d0a35-130">Record the following information:</span></span>

* <span data-ttu-id="d0a35-131">Identyfikator URI identyfikatora aplikacji (na przykład `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` , `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` lub podana wartość niestandardowa)</span><span class="sxs-lookup"><span data-stu-id="d0a35-131">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="d0a35-132">Nazwa zakresu (na przykład `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="d0a35-132">Scope name (for example, `API.Access`)</span></span>

<span data-ttu-id="d0a35-133">Identyfikator URI aplikacji może wymagać specjalnej konfiguracji w aplikacji klienckiej, która jest opisana w sekcji [zakresy tokenu dostępu](#access-token-scopes) w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="d0a35-133">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="d0a35-134">Rejestrowanie aplikacji klienckiej</span><span class="sxs-lookup"><span data-stu-id="d0a35-134">Register a client app</span></span>

<span data-ttu-id="d0a35-135">Postępuj zgodnie ze wskazówkami w [samouczku: Zarejestruj aplikację w Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) ponownie, aby zarejestrować aplikację usługi AAD dla *aplikacji klienckiej* , a następnie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="d0a35-135">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="d0a35-136">W **Azure Active Directory**  >  **rejestracje aplikacji**wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="d0a35-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="d0a35-137">Podaj **nazwę** aplikacji (na przykład \*\* Blazor AAD B2C klienta\*\*).</span><span class="sxs-lookup"><span data-stu-id="d0a35-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="d0a35-138">W przypadku **obsługiwanych typów kont**wybierz opcję wiele dzierżawców: **konta w dowolnym katalogu organizacyjnym lub dowolnego dostawcę tożsamości. Do uwierzytelniania użytkowników za pomocą Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="d0a35-138">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="d0a35-139">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="d0a35-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="d0a35-140">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="d0a35-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="d0a35-141">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d0a35-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="d0a35-142">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we właściwościach aplikacji serwera w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="d0a35-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="d0a35-143">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="d0a35-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="d0a35-144">W sekcji [Tworzenie aplikacji](#create-the-app) zostanie wyświetlona informacja przypominająca IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="d0a35-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="d0a35-145">Upewnij się, że **uprawnienia**  >  **udzielają zgody administratora na OpenID Connect, a uprawnienia offline_access** są włączone.</span><span class="sxs-lookup"><span data-stu-id="d0a35-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="d0a35-146">Wybierz pozycję **Rejestruj**.</span><span class="sxs-lookup"><span data-stu-id="d0a35-146">Select **Register**.</span></span>

<span data-ttu-id="d0a35-147">Zapisz identyfikator aplikacji (klienta) (na przykład `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="d0a35-147">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="d0a35-148">W **Authentication**obszarze  >  **konfiguracje platformy**uwierzytelniania w  >  **sieci Web**:</span><span class="sxs-lookup"><span data-stu-id="d0a35-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="d0a35-149">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="d0a35-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="d0a35-150">W przypadku **niejawnego przydzielenia**zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="d0a35-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="d0a35-151">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="d0a35-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="d0a35-152">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="d0a35-152">Select the **Save** button.</span></span>

<span data-ttu-id="d0a35-153">W **uprawnienia interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="d0a35-153">In **API permissions**:</span></span>

1. <span data-ttu-id="d0a35-154">Wybierz pozycję **Dodaj uprawnienia** , a następnie **Moje interfejsy API**.</span><span class="sxs-lookup"><span data-stu-id="d0a35-154">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="d0a35-155">Wybierz *aplikację interfejsu API serwera* z kolumny **Nazwa** (na przykład \*\* Blazor Server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="d0a35-155">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="d0a35-156">Otwórz listę **interfejsów API** .</span><span class="sxs-lookup"><span data-stu-id="d0a35-156">Open the **API** list.</span></span>
1. <span data-ttu-id="d0a35-157">Włącz dostęp do interfejsu API (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="d0a35-157">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="d0a35-158">Wybierz pozycję **Dodaj uprawnienia**.</span><span class="sxs-lookup"><span data-stu-id="d0a35-158">Select **Add permissions**.</span></span>
1. <span data-ttu-id="d0a35-159">Wybierz przycisk **Udziel zgody administratora na {nazwa dzierżawy}** .</span><span class="sxs-lookup"><span data-stu-id="d0a35-159">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="d0a35-160">Wybierz pozycję **Tak**, aby potwierdzić.</span><span class="sxs-lookup"><span data-stu-id="d0a35-160">Select **Yes** to confirm.</span></span>

<span data-ttu-id="d0a35-161">W **Home**obszarze  >  **Azure AD B2C**  >  **przepływy użytkowników**w Azure AD B2C domowej:</span><span class="sxs-lookup"><span data-stu-id="d0a35-161">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="d0a35-162">Tworzenie przepływu użytkownika dotyczącego rejestracji i logowania</span><span class="sxs-lookup"><span data-stu-id="d0a35-162">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="d0a35-163">Wybierz co najmniej **Application claims**  >  atrybut użytkownika**Nazwa wyświetlana** oświadczenia aplikacji, aby wypełnić `context.User.Identity.Name` `LoginDisplay` składnik ( `Shared/LoginDisplay.razor` ).</span><span class="sxs-lookup"><span data-stu-id="d0a35-163">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="d0a35-164">Zapisz nazwę nowego przepływu logowania i logowania utworzonego dla aplikacji (na przykład `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="d0a35-164">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="d0a35-165">Tworzenie aplikacji</span><span class="sxs-lookup"><span data-stu-id="d0a35-165">Create the app</span></span>

<span data-ttu-id="d0a35-166">Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="d0a35-166">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="d0a35-167">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="d0a35-167">Placeholder</span></span>                   | <span data-ttu-id="d0a35-168">Nazwa Azure Portal</span><span class="sxs-lookup"><span data-stu-id="d0a35-168">Azure portal name</span></span>                                     | <span data-ttu-id="d0a35-169">Przykład</span><span class="sxs-lookup"><span data-stu-id="d0a35-169">Example</span></span>                                |
| ----------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="d0a35-170">Wystąpienie</span><span class="sxs-lookup"><span data-stu-id="d0a35-170">Instance</span></span>                                              | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="d0a35-171">Identyfikator aplikacji (klienta) dla *aplikacji klienckiej*</span><span class="sxs-lookup"><span data-stu-id="d0a35-171">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="d0a35-172">Nazwa zakresu</span><span class="sxs-lookup"><span data-stu-id="d0a35-172">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="d0a35-173">Identyfikator aplikacji (klienta) dla *aplikacji interfejsu API serwera*</span><span class="sxs-lookup"><span data-stu-id="d0a35-173">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="d0a35-174">Identyfikator URI identyfikatora aplikacji ([patrz Uwaga](#access-token-scopes))</span><span class="sxs-lookup"><span data-stu-id="d0a35-174">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="d0a35-175">Przepływ użytkownika rejestracji/logowania</span><span class="sxs-lookup"><span data-stu-id="d0a35-175">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="d0a35-176">Domena podstawowa/Wydawca/dzierżawa</span><span class="sxs-lookup"><span data-stu-id="d0a35-176">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |

<span data-ttu-id="d0a35-177">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d0a35-177">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="d0a35-178">Przekaż identyfikator URI aplikacji do `app-id-uri` opcji, ale Uwaga w aplikacji klienckiej może być wymagana zmiana konfiguracji, która jest opisana w sekcji [zakresy tokenu dostępu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="d0a35-178">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="d0a35-179">Ponadto zakres skonfigurowany przez Blazor szablon hostowany może być powtórzony na HOŚCIE identyfikatora URI aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d0a35-179">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="d0a35-180">Upewnij się, że zakres skonfigurowany dla `DefaultAccessTokenScopes` kolekcji jest poprawny w `Program.Main` ( `Program.cs` ) *aplikacji klienckiej*.</span><span class="sxs-lookup"><span data-stu-id="d0a35-180">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="d0a35-181">W Azure Portal konfiguracja platformy **uwierzytelniania**w sieci Web dla *aplikacji klienta*  >  **Platform configurations**  >  **Web**  >  **Redirect URI** jest skonfigurowana dla portu 5001 dla aplikacji, które działają na serwerze Kestrel z ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="d0a35-181">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="d0a35-182">Jeśli *aplikacja kliencka* jest uruchamiana na losowo IIS Express porcie, port aplikacji można znaleźć we właściwościach *aplikacji interfejsu API serwera* w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="d0a35-182">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="d0a35-183">Jeśli port nie został wcześniej skonfigurowany przy użyciu znanego portu *aplikacji klienta* , Wróć do rejestracji *aplikacji klienckiej* w Azure Portal i zaktualizuj identyfikator URI przekierowania z prawidłowym portem.</span><span class="sxs-lookup"><span data-stu-id="d0a35-183">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="d0a35-184">Konfiguracja aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="d0a35-184">Server app configuration</span></span>

<span data-ttu-id="d0a35-185">*Ta sekcja dotyczy **`Server`** aplikacji rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="d0a35-185">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="d0a35-186">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="d0a35-186">Authentication package</span></span>

<span data-ttu-id="d0a35-187">Pakiet zawiera wsparcie w zakresie uwierzytelniania i autoryzowania wywołań ASP.NET Core interfejsów API sieci Web [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) .</span><span class="sxs-lookup"><span data-stu-id="d0a35-187">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="d0a35-188">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="d0a35-188">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="d0a35-189">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="d0a35-189">Authentication service support</span></span>

<span data-ttu-id="d0a35-190">Metoda konfiguruje `AddAuthentication` usługi uwierzytelniania w ramach aplikacji i konfiguruje procedurę obsługi okaziciela JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="d0a35-190">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="d0a35-191"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A>Metoda ustawia określone parametry w obsłudze okaziciela JWT wymagane do weryfikacji tokenów emitowanych przez Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="d0a35-191">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="d0a35-192"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> i <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> upewnij się, że:</span><span class="sxs-lookup"><span data-stu-id="d0a35-192"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="d0a35-193">Aplikacja próbuje analizować tokeny i sprawdzać ich poprawność w żądaniach przychodzących.</span><span class="sxs-lookup"><span data-stu-id="d0a35-193">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="d0a35-194">Wszystkie żądania próbujące uzyskać dostęp do chronionego zasobu bez poprawnego poświadczenia nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="d0a35-194">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="d0a35-195">Użytkownik. Identity . Nazwij</span><span class="sxs-lookup"><span data-stu-id="d0a35-195">User.Identity.Name</span></span>

<span data-ttu-id="d0a35-196">Domyślnie wartość `User.Identity.Name` nie jest wypełniona.</span><span class="sxs-lookup"><span data-stu-id="d0a35-196">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="d0a35-197">Aby skonfigurować aplikację do odbierania wartości z `name` typu, należy skonfigurować <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d0a35-197">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="d0a35-198">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="d0a35-198">App settings</span></span>

<span data-ttu-id="d0a35-199">`appsettings.json`Plik zawiera opcje konfigurowania procedury obsługi okaziciela JWT używanej do sprawdzania poprawności tokenów dostępu.</span><span class="sxs-lookup"><span data-stu-id="d0a35-199">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="d0a35-200">Przykład:</span><span class="sxs-lookup"><span data-stu-id="d0a35-200">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="d0a35-201">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="d0a35-201">WeatherForecast controller</span></span>

<span data-ttu-id="d0a35-202">Kontroler WeatherForecast (*controllers/WeatherForecastController. cs*) ujawnia chroniony interfejs API z [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybutem zastosowanym do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="d0a35-202">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="d0a35-203">**Ważne** jest, aby zrozumieć, że:</span><span class="sxs-lookup"><span data-stu-id="d0a35-203">It's **important** to understand that:</span></span>

* <span data-ttu-id="d0a35-204">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atrybut w tym kontrolerze interfejsu API jest jedynym warunkiem, że ten interfejs API jest chroniony przed nieautoryzowanym dostępem.</span><span class="sxs-lookup"><span data-stu-id="d0a35-204">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="d0a35-205">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atrybut używany w Blazor WebAssembly aplikacji służy tylko jako Wskazówka dla aplikacji, którą użytkownik powinien mieć autoryzację, aby aplikacja działała poprawnie.</span><span class="sxs-lookup"><span data-stu-id="d0a35-205">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="d0a35-206">Konfiguracja aplikacji klienta</span><span class="sxs-lookup"><span data-stu-id="d0a35-206">Client app configuration</span></span>

<span data-ttu-id="d0a35-207">*Ta sekcja dotyczy **`Client`** aplikacji rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="d0a35-207">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="d0a35-208">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="d0a35-208">Authentication package</span></span>

<span data-ttu-id="d0a35-209">Gdy aplikacja zostanie utworzona w celu korzystania z pojedynczego konta B2C ( `IndividualB2C` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="d0a35-209">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="d0a35-210">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="d0a35-210">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="d0a35-211">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="d0a35-211">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="d0a35-212">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="d0a35-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="d0a35-213">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Pakiet zawiera przechodnie Dodawanie [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d0a35-213">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="d0a35-214">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="d0a35-214">Authentication service support</span></span>

<span data-ttu-id="d0a35-215">Dodano obsługę <xref:System.Net.Http.HttpClient> wystąpień, które obejmują tokeny dostępu podczas wykonywania żądań do projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="d0a35-215">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="d0a35-216">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="d0a35-216">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="d0a35-217">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="d0a35-217">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="d0a35-218">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pakiet.</span><span class="sxs-lookup"><span data-stu-id="d0a35-218">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="d0a35-219">Ta metoda konfiguruje usługi wymagane przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="d0a35-219">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="d0a35-220">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="d0a35-220">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="d0a35-221"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d0a35-221">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="d0a35-222">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD w witrynie Azure Portal podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d0a35-222">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="d0a35-223">Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="d0a35-223">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="d0a35-224">Przykład:</span><span class="sxs-lookup"><span data-stu-id="d0a35-224">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="d0a35-225">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="d0a35-225">Access token scopes</span></span>

<span data-ttu-id="d0a35-226">Domyślne zakresy tokenów dostępu reprezentują listę zakresów tokenów dostępu, które są następujące:</span><span class="sxs-lookup"><span data-stu-id="d0a35-226">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="d0a35-227">Uwzględnione domyślnie w żądaniu logowania.</span><span class="sxs-lookup"><span data-stu-id="d0a35-227">Included by default in the sign in request.</span></span>
* <span data-ttu-id="d0a35-228">Służy do aprowizacji tokenu dostępu zaraz po uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="d0a35-228">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="d0a35-229">Wszystkie zakresy muszą należeć do tej samej aplikacji na Azure Active Directory reguł.</span><span class="sxs-lookup"><span data-stu-id="d0a35-229">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="d0a35-230">Dodatkowe zakresy można dodać do dodatkowych aplikacji interfejsu API w razie potrzeby:</span><span class="sxs-lookup"><span data-stu-id="d0a35-230">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="d0a35-231">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="d0a35-231">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="d0a35-232">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="d0a35-232">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="d0a35-233">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="d0a35-233">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="d0a35-234">Tryb logowania</span><span class="sxs-lookup"><span data-stu-id="d0a35-234">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="d0a35-235">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="d0a35-235">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="d0a35-236">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="d0a35-236">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="d0a35-237">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="d0a35-237">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="d0a35-238">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="d0a35-238">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="d0a35-239">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="d0a35-239">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="d0a35-240">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="d0a35-240">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="d0a35-241">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="d0a35-241">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="d0a35-242">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="d0a35-242">Run the app</span></span>

<span data-ttu-id="d0a35-243">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="d0a35-243">Run the app from the Server project.</span></span> <span data-ttu-id="d0a35-244">W przypadku korzystania z programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d0a35-244">When using Visual Studio, either:</span></span>

* <span data-ttu-id="d0a35-245">Ustaw listę rozwijaną **projekty startowe** na pasku narzędzi do *aplikacji interfejsu API serwera* i wybierz przycisk **Uruchom** .</span><span class="sxs-lookup"><span data-stu-id="d0a35-245">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="d0a35-246">Wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .</span><span class="sxs-lookup"><span data-stu-id="d0a35-246">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="d0a35-247">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="d0a35-247">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="d0a35-248">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="d0a35-248">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="d0a35-249">Samouczek: Tworzenie dzierżawy usługi Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="d0a35-249">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="d0a35-250">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="d0a35-250">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
