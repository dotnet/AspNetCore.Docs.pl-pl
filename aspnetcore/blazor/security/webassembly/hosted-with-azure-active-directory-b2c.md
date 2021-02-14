---
title: Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core przy użyciu Azure Active Directory B2C
author: guardrex
description: Dowiedz się, jak zabezpieczyć Blazor WebAssembly aplikację hostowaną ASP.NET Core przy użyciu Azure Active Directory B2C.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/26/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: c67f8e8d81fbdbd8a1f103dd1bd258212efe014f
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280956"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="45dbc-103">Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core przy użyciu Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="45dbc-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="45dbc-104">W tym artykule opisano sposób tworzenia [hostowanej Blazor WebAssembly aplikacji](xref:blazor/hosting-models#blazor-webassembly) korzystającej z usługi [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) na potrzeby uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="45dbc-104">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="45dbc-105">Rejestrowanie aplikacji w AAD B2C i tworzenie rozwiązania</span><span class="sxs-lookup"><span data-stu-id="45dbc-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="45dbc-106">Tworzenie dzierżawy</span><span class="sxs-lookup"><span data-stu-id="45dbc-106">Create a tenant</span></span>

<span data-ttu-id="45dbc-107">Postępuj zgodnie ze wskazówkami w [samouczku: Tworzenie dzierżawy Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) , aby utworzyć dzierżawę AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="45dbc-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span> <span data-ttu-id="45dbc-108">Wróć do tego artykułu natychmiast po utworzeniu lub zidentyfikowaniu dzierżawy do użycia.</span><span class="sxs-lookup"><span data-stu-id="45dbc-108">Return to this article immediately after creating or identifying a tenant to use.</span></span>

<span data-ttu-id="45dbc-109">Zapisz wystąpienie AAD B2C (na przykład, w `https://contoso.b2clogin.com/` którym znajduje się ukośnik końcowy).</span><span class="sxs-lookup"><span data-stu-id="45dbc-109">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="45dbc-110">Wystąpienie jest schematem i hostem rejestracji aplikacji Azure B2C, którą można znaleźć, otwierając okno **punkty końcowe** ze strony **rejestracje aplikacji** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="45dbc-110">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="45dbc-111">Rejestrowanie aplikacji interfejsu API serwera</span><span class="sxs-lookup"><span data-stu-id="45dbc-111">Register a server API app</span></span>

<span data-ttu-id="45dbc-112">Zarejestruj aplikację AAD B2C dla *aplikacji interfejsu API serwera*:</span><span class="sxs-lookup"><span data-stu-id="45dbc-112">Register an AAD B2C app for the *Server API app*:</span></span>

1. <span data-ttu-id="45dbc-113">W **Azure Active Directory**  >  **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="45dbc-113">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="45dbc-114">Podaj **nazwę** aplikacji (na przykład **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="45dbc-114">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="45dbc-115">W przypadku **obsługiwanych typów kont** wybierz opcję wiele dzierżawców: **konta w dowolnym dostawcy tożsamości lub katalogu organizacyjnego (do uwierzytelniania użytkowników za pomocą przepływów użytkowników)**</span><span class="sxs-lookup"><span data-stu-id="45dbc-115">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="45dbc-116">*Aplikacja interfejsu API serwera* nie wymaga **identyfikatora URI przekierowania** w tym scenariuszu, więc pozostaw listę rozwijaną w **sieci Web** i nie wprowadzaj identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="45dbc-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="45dbc-117">Upewnij się, że **uprawnienia**  >  **udzielają zgody administratora na OpenID Connect, a uprawnienia offline_access** są zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="45dbc-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="45dbc-118">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="45dbc-118">Select **Register**.</span></span>

<span data-ttu-id="45dbc-119">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="45dbc-119">Record the following information:</span></span>

* <span data-ttu-id="45dbc-120">*Aplikacja interfejsu API serwera* Identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="45dbc-120">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="45dbc-121">Domena podstawowa/Wydawca/dzierżawa usługi AAD (na przykład `contoso.onmicrosoft.com` ): domena jest dostępna jako **domena wydawcy** w bloku **znakowania** Azure Portal dla zarejestrowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45dbc-121">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="45dbc-122">W obszarze **Uwidacznianie interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="45dbc-122">In **Expose an API**:</span></span>

1. <span data-ttu-id="45dbc-123">Wybierz polecenie **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="45dbc-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="45dbc-124">Wybierz przycisk **Zapisz i kontynuuj**.</span><span class="sxs-lookup"><span data-stu-id="45dbc-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="45dbc-125">Podaj **nazwę zakresu** (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="45dbc-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="45dbc-126">Podaj **nazwę wyświetlaną zgody administratora** (na przykład `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="45dbc-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="45dbc-127">Podaj **Opis zgody administratora** (na przykład `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="45dbc-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="45dbc-128">Upewnij się, że **stan** jest ustawiony na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="45dbc-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="45dbc-129">Wybierz pozycję **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="45dbc-129">Select **Add scope**.</span></span>

<span data-ttu-id="45dbc-130">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="45dbc-130">Record the following information:</span></span>

* <span data-ttu-id="45dbc-131">Identyfikator URI identyfikatora aplikacji (na przykład `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` , `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` lub podana wartość niestandardowa)</span><span class="sxs-lookup"><span data-stu-id="45dbc-131">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="45dbc-132">Nazwa zakresu (na przykład `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="45dbc-132">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="45dbc-133">Rejestrowanie aplikacji klienckiej</span><span class="sxs-lookup"><span data-stu-id="45dbc-133">Register a client app</span></span>

<span data-ttu-id="45dbc-134">Zarejestruj aplikację AAD B2C dla *aplikacji klienckiej*:</span><span class="sxs-lookup"><span data-stu-id="45dbc-134">Register an AAD B2C app for the *Client app*:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="45dbc-135">W **Azure Active Directory** > **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="45dbc-135">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="45dbc-136">Podaj **nazwę** aplikacji (na przykład **Blazor AAD B2C klienta**).</span><span class="sxs-lookup"><span data-stu-id="45dbc-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="45dbc-137">W przypadku **obsługiwanych typów kont** wybierz opcję wiele dzierżawców: **konta w dowolnym dostawcy tożsamości lub katalogu organizacyjnego (do uwierzytelniania użytkowników za pomocą przepływów użytkowników)**</span><span class="sxs-lookup"><span data-stu-id="45dbc-137">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="45dbc-138">Ustaw listę rozwijaną **URI przekierowania** na **aplikację jednostronicową (Spa)** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="45dbc-138">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="45dbc-139">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="45dbc-139">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="45dbc-140">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45dbc-140">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="45dbc-141">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we *`Server`* właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="45dbc-141">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="45dbc-142">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="45dbc-142">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="45dbc-143">W sekcji [Tworzenie aplikacji](#create-the-app) zostanie wyświetlona informacja przypominająca IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="45dbc-143">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="45dbc-144">Upewnij się, że **uprawnienia** > **udzielają zgody administratora na OpenID Connect, a uprawnienia offline_access** są zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="45dbc-144">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="45dbc-145">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="45dbc-145">Select **Register**.</span></span>

1. <span data-ttu-id="45dbc-146">Zapisz identyfikator aplikacji (klienta) (na przykład `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="45dbc-146">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="45dbc-147">W  obszarze > **Konfiguracja platformy** uwierzytelniania > **aplikacja jednostronicowa (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="45dbc-147">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="45dbc-148">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="45dbc-148">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="45dbc-149">W przypadku **niejawnego udzielenia** upewnij się, że **nie** wybrano pól wyboru dla **tokenów dostępu** i **tokenów identyfikatorów** .</span><span class="sxs-lookup"><span data-stu-id="45dbc-149">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="45dbc-150">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="45dbc-150">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="45dbc-151">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="45dbc-151">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="45dbc-152">W **Azure Active Directory** > **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="45dbc-152">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="45dbc-153">Podaj **nazwę** aplikacji (na przykład **Blazor AAD B2C klienta**).</span><span class="sxs-lookup"><span data-stu-id="45dbc-153">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="45dbc-154">W przypadku **obsługiwanych typów kont** wybierz opcję wiele dzierżawców: **konta w dowolnym dostawcy tożsamości lub katalogu organizacyjnego (do uwierzytelniania użytkowników za pomocą przepływów użytkowników)**</span><span class="sxs-lookup"><span data-stu-id="45dbc-154">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="45dbc-155">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="45dbc-155">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="45dbc-156">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="45dbc-156">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="45dbc-157">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45dbc-157">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="45dbc-158">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we *`Server`* właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="45dbc-158">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="45dbc-159">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="45dbc-159">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="45dbc-160">W sekcji [Tworzenie aplikacji](#create-the-app) zostanie wyświetlona informacja przypominająca IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="45dbc-160">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="45dbc-161">Upewnij się, że **uprawnienia** > **udzielają zgody administratora na OpenID Connect, a uprawnienia offline_access** są zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="45dbc-161">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="45dbc-162">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="45dbc-162">Select **Register**.</span></span>

<span data-ttu-id="45dbc-163">Zapisz identyfikator aplikacji (klienta) (na przykład `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="45dbc-163">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="45dbc-164">W  obszarze > **konfiguracje platformy** uwierzytelniania w > **sieci Web**:</span><span class="sxs-lookup"><span data-stu-id="45dbc-164">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="45dbc-165">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="45dbc-165">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="45dbc-166">W przypadku **niejawnego przydzielenia** zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="45dbc-166">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="45dbc-167">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="45dbc-167">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="45dbc-168">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="45dbc-168">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="45dbc-169">W **uprawnienia interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="45dbc-169">In **API permissions**:</span></span>

1. <span data-ttu-id="45dbc-170">Wybierz pozycję **Dodaj uprawnienia** , a następnie **Moje interfejsy API**.</span><span class="sxs-lookup"><span data-stu-id="45dbc-170">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="45dbc-171">Wybierz *aplikację interfejsu API serwera* z kolumny **Nazwa** (na przykład **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="45dbc-171">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="45dbc-172">Otwórz listę **interfejsów API** .</span><span class="sxs-lookup"><span data-stu-id="45dbc-172">Open the **API** list.</span></span>
1. <span data-ttu-id="45dbc-173">Włącz dostęp do interfejsu API (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="45dbc-173">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="45dbc-174">Wybierz pozycję **Dodaj uprawnienia**.</span><span class="sxs-lookup"><span data-stu-id="45dbc-174">Select **Add permissions**.</span></span>
1. <span data-ttu-id="45dbc-175">Wybierz przycisk **Udziel zgody administratora na {nazwa dzierżawy}** .</span><span class="sxs-lookup"><span data-stu-id="45dbc-175">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="45dbc-176">Wybierz pozycję **Tak**, aby potwierdzić.</span><span class="sxs-lookup"><span data-stu-id="45dbc-176">Select **Yes** to confirm.</span></span>

<span data-ttu-id="45dbc-177">W obszarze  >    >  **przepływy użytkowników** w Azure AD B2C domowej:</span><span class="sxs-lookup"><span data-stu-id="45dbc-177">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="45dbc-178">Tworzenie przepływu użytkownika dotyczącego rejestracji i logowania</span><span class="sxs-lookup"><span data-stu-id="45dbc-178">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="45dbc-179">Wybierz co najmniej   >  atrybut użytkownika **Nazwa wyświetlana** oświadczenia aplikacji, aby wypełnić `context.User.Identity.Name` `LoginDisplay` składnik ( `Shared/LoginDisplay.razor` ).</span><span class="sxs-lookup"><span data-stu-id="45dbc-179">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="45dbc-180">Zapisz nazwę nowego przepływu logowania i logowania utworzonego dla aplikacji (na przykład `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="45dbc-180">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="45dbc-181">Tworzenie aplikacji</span><span class="sxs-lookup"><span data-stu-id="45dbc-181">Create the app</span></span>

<span data-ttu-id="45dbc-182">Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="45dbc-182">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="45dbc-183">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="45dbc-183">Placeholder</span></span>                   | <span data-ttu-id="45dbc-184">Nazwa Azure Portal</span><span class="sxs-lookup"><span data-stu-id="45dbc-184">Azure portal name</span></span>                                     | <span data-ttu-id="45dbc-185">Przykład</span><span class="sxs-lookup"><span data-stu-id="45dbc-185">Example</span></span>                                        |
| ----------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="45dbc-186">Wystąpienie</span><span class="sxs-lookup"><span data-stu-id="45dbc-186">Instance</span></span>                                              | `https://contoso.b2clogin.com/`                |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="45dbc-187">Identyfikator aplikacji (klienta) dla *`Client`* aplikacji</span><span class="sxs-lookup"><span data-stu-id="45dbc-187">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="45dbc-188">Nazwa zakresu</span><span class="sxs-lookup"><span data-stu-id="45dbc-188">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="45dbc-189">Identyfikator aplikacji (klienta) dla *aplikacji interfejsu API serwera*</span><span class="sxs-lookup"><span data-stu-id="45dbc-189">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="45dbc-190">Identyfikator URI identyfikatora aplikacji&dagger;</span><span class="sxs-lookup"><span data-stu-id="45dbc-190">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="45dbc-191">Przepływ użytkownika rejestracji/logowania</span><span class="sxs-lookup"><span data-stu-id="45dbc-191">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                          |
| `{TENANT DOMAIN}`             | <span data-ttu-id="45dbc-192">Domena podstawowa/Wydawca/dzierżawa</span><span class="sxs-lookup"><span data-stu-id="45dbc-192">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |

<span data-ttu-id="45dbc-193">&dagger;Blazor WebAssemblySzablon automatycznie dodaje schemat `api://` do argumentu identyfikatora aplikacji, który został przesłany w `dotnet new` poleceniu.</span><span class="sxs-lookup"><span data-stu-id="45dbc-193">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="45dbc-194">W przypadku podania identyfikatora URI aplikacji dla `{SERVER API APP ID URI}` symbolu zastępczego i jeśli schemat jest `api://` , Usuń schemat ( `api://` ) z argumentu, jak przykładowa wartość w powyższej tabeli pokazuje.</span><span class="sxs-lookup"><span data-stu-id="45dbc-194">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="45dbc-195">Jeśli identyfikator URI aplikacji jest wartością niestandardową lub ma inny schemat (na przykład `https://` w przypadku niezaufanej domeny wydawcy podobnej do `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ), należy ręcznie zaktualizować domyślny identyfikator URI zakresu i usunąć `api://` schemat po *`Client`* utworzeniu aplikacji przez szablon.</span><span class="sxs-lookup"><span data-stu-id="45dbc-195">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="45dbc-196">Aby uzyskać więcej informacji, zobacz Uwaga w sekcji [zakresy tokenu dostępu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="45dbc-196">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="45dbc-197">Blazor WebAssemblySzablon można zmienić w przyszłych wydaniach ASP.NET Core, aby rozwiązać te scenariusze.</span><span class="sxs-lookup"><span data-stu-id="45dbc-197">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="45dbc-198">Aby uzyskać więcej informacji, zobacz [podwójny schemat identyfikatora URI aplikacji z Blazor szablonem WASM (hostowany, Single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="45dbc-198">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="45dbc-199">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45dbc-199">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="45dbc-200">Zakres skonfigurowany przez Blazor szablon hostowany może być powtórzony na HOŚCIE identyfikatora URI aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45dbc-200">The scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="45dbc-201">Upewnij się, że zakres skonfigurowany dla `DefaultAccessTokenScopes` kolekcji jest poprawny w `Program.Main` ( `Program.cs` ) *`Client`* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45dbc-201">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *`Client`* app.</span></span>

> [!NOTE]
> <span data-ttu-id="45dbc-202">W Azure Portal *`Client`* **Identyfikator URI przekierowania** konfiguracji platformy aplikacji jest skonfigurowany dla portu 5001 dla aplikacji, które działają na serwerze Kestrel z ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="45dbc-202">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="45dbc-203">Jeśli *`Client`* aplikacja jest uruchamiana na losowo IIS Express porcie, port aplikacji można znaleźć we właściwościach *aplikacji interfejsu API serwera* w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="45dbc-203">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="45dbc-204">Jeśli port nie został wcześniej skonfigurowany przy użyciu *`Client`* znanego portu aplikacji, Wróć do *`Client`* rejestracji aplikacji w Azure Portal i zaktualizuj identyfikator URI przekierowania z prawidłowym portem.</span><span class="sxs-lookup"><span data-stu-id="45dbc-204">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="45dbc-205">*`Server`* Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="45dbc-205">*`Server`* app configuration</span></span>

<span data-ttu-id="45dbc-206">*Ta sekcja dotyczy **`Server`** aplikacji rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="45dbc-206">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="45dbc-207">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="45dbc-207">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="45dbc-208">Obsługa uwierzytelniania i autoryzacji wywołań ASP.NET Core interfejsów API sieci Web za pomocą platformy firmy Microsoft Identity jest zapewniana przez następujące pakiety:</span><span class="sxs-lookup"><span data-stu-id="45dbc-208">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="45dbc-209">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem NuGet.org.</span><span class="sxs-lookup"><span data-stu-id="45dbc-209">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="45dbc-210">Pakiet zawiera wsparcie w zakresie uwierzytelniania i autoryzowania wywołań ASP.NET Core interfejsów API sieci Web [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) .</span><span class="sxs-lookup"><span data-stu-id="45dbc-210">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="45dbc-211">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="45dbc-211">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="45dbc-212">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="45dbc-212">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="45dbc-213">Metoda konfiguruje `AddAuthentication` usługi uwierzytelniania w ramach aplikacji i konfiguruje procedurę obsługi okaziciela JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="45dbc-213">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="45dbc-214"><xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A>Metoda konfiguruje usługi do ochrony internetowego interfejsu API za pomocą Identity platformy Microsoft Platform v 2.0.</span><span class="sxs-lookup"><span data-stu-id="45dbc-214">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="45dbc-215">Ta metoda oczekuje `AzureAdB2C` sekcji w konfiguracji aplikacji z ustawieniami niezbędnymi do zainicjowania opcji uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="45dbc-215">This method expects an `AzureAdB2C` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAdB2C"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="45dbc-216">Metoda konfiguruje `AddAuthentication` usługi uwierzytelniania w ramach aplikacji i konfiguruje procedurę obsługi okaziciela JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="45dbc-216">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="45dbc-217"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A>Metoda ustawia określone parametry w obsłudze okaziciela JWT wymagane do weryfikacji tokenów emitowanych przez Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="45dbc-217">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

::: moniker-end

<span data-ttu-id="45dbc-218"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> i <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> upewnij się, że:</span><span class="sxs-lookup"><span data-stu-id="45dbc-218"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="45dbc-219">Aplikacja próbuje analizować tokeny i sprawdzać ich poprawność w żądaniach przychodzących.</span><span class="sxs-lookup"><span data-stu-id="45dbc-219">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="45dbc-220">Wszystkie żądania próbujące uzyskać dostęp do chronionego zasobu bez poprawnego poświadczenia nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="45dbc-220">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="45dbc-221">Użytkownik. Identity . Nazwij</span><span class="sxs-lookup"><span data-stu-id="45dbc-221">User.Identity.Name</span></span>

<span data-ttu-id="45dbc-222">Domyślnie wartość `User.Identity.Name` nie jest wypełniona.</span><span class="sxs-lookup"><span data-stu-id="45dbc-222">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="45dbc-223">Aby skonfigurować aplikację do odbierania wartości z `name` typu zgłoszenia:</span><span class="sxs-lookup"><span data-stu-id="45dbc-223">To configure the app to receive the value from the `name` claim type:</span></span>

* <span data-ttu-id="45dbc-224">Dodaj przestrzeń nazw dla <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> do `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="45dbc-224">Add a namespace for <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> to `Startup.cs`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="45dbc-225">Skonfiguruj <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="45dbc-225">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="45dbc-226">Skonfiguruj <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="45dbc-226">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a><span data-ttu-id="45dbc-227">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="45dbc-227">App settings</span></span>

<span data-ttu-id="45dbc-228">`appsettings.json`Plik zawiera opcje konfigurowania procedury obsługi okaziciela JWT używanej do sprawdzania poprawności tokenów dostępu.</span><span class="sxs-lookup"><span data-stu-id="45dbc-228">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="45dbc-229">Przykład:</span><span class="sxs-lookup"><span data-stu-id="45dbc-229">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="45dbc-230">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="45dbc-230">WeatherForecast controller</span></span>

<span data-ttu-id="45dbc-231">Kontroler WeatherForecast (*controllers/WeatherForecastController. cs*) ujawnia chroniony interfejs API z [ `[Authorize]` atrybutem](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) zastosowanym do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="45dbc-231">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) applied to the controller.</span></span> <span data-ttu-id="45dbc-232">**Ważne** jest, aby zrozumieć, że:</span><span class="sxs-lookup"><span data-stu-id="45dbc-232">It's **important** to understand that:</span></span>

* <span data-ttu-id="45dbc-233">[ `[Authorize]` Atrybut](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) w tym kontrolerze interfejsu API jest jedynym warunkiem, że ten interfejs API jest chroniony przed nieautoryzowanym dostępem.</span><span class="sxs-lookup"><span data-stu-id="45dbc-233">The [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="45dbc-234">[ `[Authorize]` Atrybut](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) używany w Blazor WebAssembly aplikacji służy tylko jako Wskazówka dla aplikacji, którą użytkownik powinien mieć autoryzację, aby aplikacja działała poprawnie.</span><span class="sxs-lookup"><span data-stu-id="45dbc-234">The [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="45dbc-235">*`Client`* Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="45dbc-235">*`Client`* app configuration</span></span>

<span data-ttu-id="45dbc-236">*Ta sekcja dotyczy **`Client`** aplikacji rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="45dbc-236">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="45dbc-237">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="45dbc-237">Authentication package</span></span>

<span data-ttu-id="45dbc-238">Gdy aplikacja zostanie utworzona w celu korzystania z pojedynczego konta B2C ( `IndividualB2C` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="45dbc-238">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="45dbc-239">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="45dbc-239">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="45dbc-240">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="45dbc-240">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="45dbc-241">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="45dbc-241">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="45dbc-242">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Pakiet zawiera przechodnie Dodawanie [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45dbc-242">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="45dbc-243">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="45dbc-243">Authentication service support</span></span>

<span data-ttu-id="45dbc-244">Dodano obsługę <xref:System.Net.Http.HttpClient> wystąpień, które obejmują tokeny dostępu podczas wykonywania żądań do projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="45dbc-244">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="45dbc-245">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="45dbc-245">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="45dbc-246">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="45dbc-246">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="45dbc-247">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pakiet.</span><span class="sxs-lookup"><span data-stu-id="45dbc-247">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="45dbc-248">Ta metoda konfiguruje usługi wymagane przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="45dbc-248">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="45dbc-249">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="45dbc-249">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="45dbc-250"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45dbc-250">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="45dbc-251">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD w witrynie Azure Portal podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45dbc-251">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="45dbc-252">Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="45dbc-252">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="45dbc-253">Przykład:</span><span class="sxs-lookup"><span data-stu-id="45dbc-253">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="45dbc-254">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="45dbc-254">Access token scopes</span></span>

<span data-ttu-id="45dbc-255">Domyślne zakresy tokenów dostępu reprezentują listę zakresów tokenów dostępu, które są następujące:</span><span class="sxs-lookup"><span data-stu-id="45dbc-255">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="45dbc-256">Uwzględnione domyślnie w żądaniu logowania.</span><span class="sxs-lookup"><span data-stu-id="45dbc-256">Included by default in the sign in request.</span></span>
* <span data-ttu-id="45dbc-257">Służy do aprowizacji tokenu dostępu zaraz po uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="45dbc-257">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="45dbc-258">Wszystkie zakresy muszą należeć do tej samej aplikacji na Azure Active Directory reguł.</span><span class="sxs-lookup"><span data-stu-id="45dbc-258">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="45dbc-259">Dodatkowe zakresy można dodać do dodatkowych aplikacji interfejsu API w razie potrzeby:</span><span class="sxs-lookup"><span data-stu-id="45dbc-259">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="45dbc-260">Blazor WebAssemblySzablon automatycznie dodaje schemat `api://` do argumentu identyfikatora aplikacji, który został przesłany w `dotnet new` poleceniu.</span><span class="sxs-lookup"><span data-stu-id="45dbc-260">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="45dbc-261">Podczas generowania aplikacji z Blazor szablonu projektu upewnij się, że wartość domyślnego zakresu tokenu dostępu używa poprawnej wartości identyfikatora URI aplikacji niestandardowej podanej w Azure Portal lub wartości z **jednym** z następujących formatów:</span><span class="sxs-lookup"><span data-stu-id="45dbc-261">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="45dbc-262">Gdy domena wydawcy katalogu jest **zaufana**, domyślnym zakresem tokenu dostępu jest zazwyczaj wartość podobna do poniższego przykładu, gdzie `API.Access` jest domyślną nazwą zakresu:</span><span class="sxs-lookup"><span data-stu-id="45dbc-262">When the publisher domain of the directory is **trusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="45dbc-263">Sprawdź wartość dla podwójnego schematu ( `api://api://...` ).</span><span class="sxs-lookup"><span data-stu-id="45dbc-263">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="45dbc-264">Jeśli jest obecny schemat podwójny, usuń pierwszy `api://` schemat z wartości.</span><span class="sxs-lookup"><span data-stu-id="45dbc-264">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="45dbc-265">Gdy domena wydawcy katalogu nie jest **zaufana**, domyślnym zakresem tokenu dostępu jest zwykle wartość podobna do poniższego przykładu, gdzie `API.Access` jest domyślną nazwą zakresu:</span><span class="sxs-lookup"><span data-stu-id="45dbc-265">When the publisher domain of the directory is **untrusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="45dbc-266">Sprawdź wartość dodatkowego `api://` schematu ( `api://https://contoso.onmicrosoft.com/...` ).</span><span class="sxs-lookup"><span data-stu-id="45dbc-266">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="45dbc-267">Jeśli istnieje dodatkowy `api://` schemat, Usuń `api://` schemat z wartości.</span><span class="sxs-lookup"><span data-stu-id="45dbc-267">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="45dbc-268">Blazor WebAssemblySzablon można zmienić w przyszłych wydaniach ASP.NET Core, aby rozwiązać te scenariusze.</span><span class="sxs-lookup"><span data-stu-id="45dbc-268">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="45dbc-269">Aby uzyskać więcej informacji, zobacz [podwójny schemat identyfikatora URI aplikacji z Blazor szablonem WASM (hostowany, Single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="45dbc-269">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="45dbc-270">Określ dodatkowe zakresy z `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="45dbc-270">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="45dbc-271">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="45dbc-271">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="45dbc-272">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="45dbc-272">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="45dbc-273">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="45dbc-273">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="45dbc-274">Tryb logowania</span><span class="sxs-lookup"><span data-stu-id="45dbc-274">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="45dbc-275">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="45dbc-275">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="45dbc-276">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="45dbc-276">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="45dbc-277">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="45dbc-277">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="45dbc-278">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="45dbc-278">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="45dbc-279">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="45dbc-279">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="45dbc-280">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="45dbc-280">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="45dbc-281">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="45dbc-281">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="45dbc-282">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="45dbc-282">Run the app</span></span>

<span data-ttu-id="45dbc-283">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="45dbc-283">Run the app from the Server project.</span></span> <span data-ttu-id="45dbc-284">W przypadku korzystania z programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="45dbc-284">When using Visual Studio, either:</span></span>

* <span data-ttu-id="45dbc-285">Ustaw listę rozwijaną **projekty startowe** na pasku narzędzi do *aplikacji interfejsu API serwera* i wybierz przycisk **Uruchom** .</span><span class="sxs-lookup"><span data-stu-id="45dbc-285">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="45dbc-286">Wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .</span><span class="sxs-lookup"><span data-stu-id="45dbc-286">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="45dbc-287">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="45dbc-287">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="45dbc-288">Utwórz niestandardową wersję biblioteki MSAL JavaScript.</span><span class="sxs-lookup"><span data-stu-id="45dbc-288">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="45dbc-289">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="45dbc-289">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="45dbc-290">Samouczek: Tworzenie dzierżawy usługi Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="45dbc-290">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="45dbc-291">Samouczek: rejestrowanie aplikacji w Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="45dbc-291">Tutorial: Register an application in Azure Active Directory B2C</span></span>](/azure/active-directory-b2c/tutorial-register-applications)
* [<span data-ttu-id="45dbc-292">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="45dbc-292">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
