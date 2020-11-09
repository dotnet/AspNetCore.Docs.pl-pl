---
title: 'Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core przy użyciu Azure Active Directory B2C'
author: guardrex
description: 'Dowiedz się, jak zabezpieczyć Blazor WebAssembly aplikację hostowaną ASP.NET Core przy użyciu Azure Active Directory B2C.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/02/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 8727fa52acbcf59549c326bd5106e5dfe23c36be
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234494"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="16aae-103">Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core przy użyciu Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="16aae-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="16aae-104">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="16aae-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="16aae-105">W tym artykule opisano sposób tworzenia [hostowanej Blazor WebAssembly aplikacji](xref:blazor/hosting-models#blazor-webassembly) korzystającej z usługi [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) na potrzeby uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="16aae-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="16aae-106">Rejestrowanie aplikacji w AAD B2C i tworzenie rozwiązania</span><span class="sxs-lookup"><span data-stu-id="16aae-106">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="16aae-107">Tworzenie dzierżawy</span><span class="sxs-lookup"><span data-stu-id="16aae-107">Create a tenant</span></span>

<span data-ttu-id="16aae-108">Postępuj zgodnie ze wskazówkami w [samouczku: Tworzenie dzierżawy Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) , aby utworzyć dzierżawę AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="16aae-108">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="16aae-109">Zapisz wystąpienie AAD B2C (na przykład, w `https://contoso.b2clogin.com/` którym znajduje się ukośnik końcowy).</span><span class="sxs-lookup"><span data-stu-id="16aae-109">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="16aae-110">Wystąpienie jest schematem i hostem rejestracji aplikacji Azure B2C, którą można znaleźć, otwierając okno **punkty końcowe** ze strony **rejestracje aplikacji** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="16aae-110">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="16aae-111">Rejestrowanie aplikacji interfejsu API serwera</span><span class="sxs-lookup"><span data-stu-id="16aae-111">Register a server API app</span></span>

<span data-ttu-id="16aae-112">Postępuj zgodnie ze wskazówkami w [samouczku: Zarejestruj aplikację w Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) , aby zarejestrować aplikację usługi AAD dla *aplikacji interfejsu API serwera* , a następnie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="16aae-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="16aae-113">W **Azure Active Directory**  >  **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja** .</span><span class="sxs-lookup"><span data-stu-id="16aae-113">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="16aae-114">Podaj **nazwę** aplikacji (na przykład **Blazor Server AAD B2C** ).</span><span class="sxs-lookup"><span data-stu-id="16aae-114">Provide a **Name** for the app (for example, **Blazor Server AAD B2C** ).</span></span>
1. <span data-ttu-id="16aae-115">W przypadku **obsługiwanych typów kont** wybierz opcję wiele dzierżawców: **konta w dowolnym dostawcy tożsamości lub katalogu organizacyjnego (do uwierzytelniania użytkowników za pomocą przepływów użytkowników)**</span><span class="sxs-lookup"><span data-stu-id="16aae-115">For **Supported account types** , select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="16aae-116">*Aplikacja interfejsu API serwera* nie wymaga **identyfikatora URI przekierowania** w tym scenariuszu, więc pozostaw listę rozwijaną w **sieci Web** i nie wprowadzaj identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="16aae-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="16aae-117">Upewnij się, że **uprawnienia**  >  **udzielają zgody administratora na OpenID Connect, a uprawnienia offline_access** są zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="16aae-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="16aae-118">Wybierz pozycję **Zarejestruj** .</span><span class="sxs-lookup"><span data-stu-id="16aae-118">Select **Register** .</span></span>

<span data-ttu-id="16aae-119">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="16aae-119">Record the following information:</span></span>

* <span data-ttu-id="16aae-120">*Aplikacja interfejsu API serwera* Identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="16aae-120">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="16aae-121">Domena podstawowa/Wydawca/dzierżawa usługi AAD (na przykład `contoso.onmicrosoft.com` ): domena jest dostępna jako **domena wydawcy** w bloku **znakowania** Azure Portal dla zarejestrowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16aae-121">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="16aae-122">W obszarze **Uwidacznianie interfejsu API** :</span><span class="sxs-lookup"><span data-stu-id="16aae-122">In **Expose an API** :</span></span>

1. <span data-ttu-id="16aae-123">Wybierz polecenie **Dodaj zakres** .</span><span class="sxs-lookup"><span data-stu-id="16aae-123">Select **Add a scope** .</span></span>
1. <span data-ttu-id="16aae-124">Wybierz przycisk **Zapisz i kontynuuj** .</span><span class="sxs-lookup"><span data-stu-id="16aae-124">Select **Save and continue** .</span></span>
1. <span data-ttu-id="16aae-125">Podaj **nazwę zakresu** (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="16aae-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="16aae-126">Podaj **nazwę wyświetlaną zgody administratora** (na przykład `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="16aae-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="16aae-127">Podaj **Opis zgody administratora** (na przykład `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="16aae-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="16aae-128">Upewnij się, że **stan** jest ustawiony na **włączone** .</span><span class="sxs-lookup"><span data-stu-id="16aae-128">Confirm that the **State** is set to **Enabled** .</span></span>
1. <span data-ttu-id="16aae-129">Wybierz pozycję **Dodaj zakres** .</span><span class="sxs-lookup"><span data-stu-id="16aae-129">Select **Add scope** .</span></span>

<span data-ttu-id="16aae-130">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="16aae-130">Record the following information:</span></span>

* <span data-ttu-id="16aae-131">Identyfikator URI identyfikatora aplikacji (na przykład `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` , `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` lub podana wartość niestandardowa)</span><span class="sxs-lookup"><span data-stu-id="16aae-131">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="16aae-132">Nazwa zakresu (na przykład `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="16aae-132">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="16aae-133">Rejestrowanie aplikacji klienckiej</span><span class="sxs-lookup"><span data-stu-id="16aae-133">Register a client app</span></span>

<span data-ttu-id="16aae-134">Postępuj zgodnie ze wskazówkami w [samouczku: Zarejestruj aplikację w Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) ponownie, aby zarejestrować aplikację usługi AAD dla *`Client`* aplikacji, a następnie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="16aae-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="16aae-135">W **Azure Active Directory** > **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja** .</span><span class="sxs-lookup"><span data-stu-id="16aae-135">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="16aae-136">Podaj **nazwę** aplikacji (na przykład **Blazor AAD B2C klienta** ).</span><span class="sxs-lookup"><span data-stu-id="16aae-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C** ).</span></span>
1. <span data-ttu-id="16aae-137">W przypadku **obsługiwanych typów kont** wybierz opcję wiele dzierżawców: **konta w dowolnym dostawcy tożsamości lub katalogu organizacyjnego (do uwierzytelniania użytkowników za pomocą przepływów użytkowników)**</span><span class="sxs-lookup"><span data-stu-id="16aae-137">For **Supported account types** , select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="16aae-138">Ustaw listę rozwijaną **URI przekierowania** na **aplikację jednostronicową (Spa)** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="16aae-138">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="16aae-139">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="16aae-139">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="16aae-140">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16aae-140">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="16aae-141">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we *`Server`* właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="16aae-141">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="16aae-142">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="16aae-142">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="16aae-143">W sekcji [Tworzenie aplikacji](#create-the-app) zostanie wyświetlona informacja przypominająca IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="16aae-143">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="16aae-144">Upewnij się, że **uprawnienia** > **udzielają zgody administratora na OpenID Connect, a uprawnienia offline_access** są zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="16aae-144">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="16aae-145">Wybierz pozycję **Zarejestruj** .</span><span class="sxs-lookup"><span data-stu-id="16aae-145">Select **Register** .</span></span>

1. <span data-ttu-id="16aae-146">Zapisz identyfikator aplikacji (klienta) (na przykład `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="16aae-146">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="16aae-147">W **Authentication** obszarze > **Konfiguracja platformy** uwierzytelniania > **aplikacja jednostronicowa (Spa)** :</span><span class="sxs-lookup"><span data-stu-id="16aae-147">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="16aae-148">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="16aae-148">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="16aae-149">W przypadku **niejawnego udzielenia** upewnij się, że **nie** wybrano pól wyboru dla **tokenów dostępu** i **tokenów identyfikatorów** .</span><span class="sxs-lookup"><span data-stu-id="16aae-149">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="16aae-150">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="16aae-150">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="16aae-151">Wybierz ikonę **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="16aae-151">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="16aae-152">W **Azure Active Directory** > **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja** .</span><span class="sxs-lookup"><span data-stu-id="16aae-152">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="16aae-153">Podaj **nazwę** aplikacji (na przykład **Blazor AAD B2C klienta** ).</span><span class="sxs-lookup"><span data-stu-id="16aae-153">Provide a **Name** for the app (for example, **Blazor Client AAD B2C** ).</span></span>
1. <span data-ttu-id="16aae-154">W przypadku **obsługiwanych typów kont** wybierz opcję wiele dzierżawców: **konta w dowolnym dostawcy tożsamości lub katalogu organizacyjnego (do uwierzytelniania użytkowników za pomocą przepływów użytkowników)**</span><span class="sxs-lookup"><span data-stu-id="16aae-154">For **Supported account types** , select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="16aae-155">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="16aae-155">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="16aae-156">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="16aae-156">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="16aae-157">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16aae-157">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="16aae-158">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we *`Server`* właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="16aae-158">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="16aae-159">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="16aae-159">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="16aae-160">W sekcji [Tworzenie aplikacji](#create-the-app) zostanie wyświetlona informacja przypominająca IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="16aae-160">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="16aae-161">Upewnij się, że **uprawnienia** > **udzielają zgody administratora na OpenID Connect, a uprawnienia offline_access** są zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="16aae-161">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="16aae-162">Wybierz pozycję **Zarejestruj** .</span><span class="sxs-lookup"><span data-stu-id="16aae-162">Select **Register** .</span></span>

<span data-ttu-id="16aae-163">Zapisz identyfikator aplikacji (klienta) (na przykład `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="16aae-163">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="16aae-164">W **Authentication** obszarze > **konfiguracje platformy** uwierzytelniania w > **sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="16aae-164">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="16aae-165">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="16aae-165">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="16aae-166">W przypadku **niejawnego przydzielenia** zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów** .</span><span class="sxs-lookup"><span data-stu-id="16aae-166">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens** .</span></span>
1. <span data-ttu-id="16aae-167">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="16aae-167">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="16aae-168">Wybierz ikonę **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="16aae-168">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="16aae-169">W **uprawnienia interfejsu API** :</span><span class="sxs-lookup"><span data-stu-id="16aae-169">In **API permissions** :</span></span>

1. <span data-ttu-id="16aae-170">Wybierz pozycję **Dodaj uprawnienia** , a następnie **Moje interfejsy API** .</span><span class="sxs-lookup"><span data-stu-id="16aae-170">Select **Add a permission** followed by **My APIs** .</span></span>
1. <span data-ttu-id="16aae-171">Wybierz *aplikację interfejsu API serwera* z kolumny **Nazwa** (na przykład **Blazor Server AAD B2C** ).</span><span class="sxs-lookup"><span data-stu-id="16aae-171">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C** ).</span></span>
1. <span data-ttu-id="16aae-172">Otwórz listę **interfejsów API** .</span><span class="sxs-lookup"><span data-stu-id="16aae-172">Open the **API** list.</span></span>
1. <span data-ttu-id="16aae-173">Włącz dostęp do interfejsu API (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="16aae-173">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="16aae-174">Wybierz pozycję **Dodaj uprawnienia** .</span><span class="sxs-lookup"><span data-stu-id="16aae-174">Select **Add permissions** .</span></span>
1. <span data-ttu-id="16aae-175">Wybierz przycisk **Udziel zgody administratora na {nazwa dzierżawy}** .</span><span class="sxs-lookup"><span data-stu-id="16aae-175">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="16aae-176">Wybierz pozycję **Tak** , aby potwierdzić.</span><span class="sxs-lookup"><span data-stu-id="16aae-176">Select **Yes** to confirm.</span></span>

<span data-ttu-id="16aae-177">W **Home** obszarze  >  **Azure AD B2C**  >  **przepływy użytkowników** w Azure AD B2C domowej:</span><span class="sxs-lookup"><span data-stu-id="16aae-177">In **Home** > **Azure AD B2C** > **User flows** :</span></span>

[<span data-ttu-id="16aae-178">Tworzenie przepływu użytkownika dotyczącego rejestracji i logowania</span><span class="sxs-lookup"><span data-stu-id="16aae-178">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="16aae-179">Wybierz co najmniej **Application claims**  >  atrybut użytkownika **Nazwa wyświetlana** oświadczenia aplikacji, aby wypełnić `context.User.Identity.Name` `LoginDisplay` składnik ( `Shared/LoginDisplay.razor` ).</span><span class="sxs-lookup"><span data-stu-id="16aae-179">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="16aae-180">Zapisz nazwę nowego przepływu logowania i logowania utworzonego dla aplikacji (na przykład `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="16aae-180">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="16aae-181">Tworzenie aplikacji</span><span class="sxs-lookup"><span data-stu-id="16aae-181">Create the app</span></span>

<span data-ttu-id="16aae-182">Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="16aae-182">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="16aae-183">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="16aae-183">Placeholder</span></span>                   | <span data-ttu-id="16aae-184">Nazwa Azure Portal</span><span class="sxs-lookup"><span data-stu-id="16aae-184">Azure portal name</span></span>                                     | <span data-ttu-id="16aae-185">Przykład</span><span class="sxs-lookup"><span data-stu-id="16aae-185">Example</span></span>                                        |
| ----------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="16aae-186">Wystąpienie</span><span class="sxs-lookup"><span data-stu-id="16aae-186">Instance</span></span>                                              | `https://contoso.b2clogin.com/`                |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="16aae-187">Identyfikator aplikacji (klienta) dla *`Client`* aplikacji</span><span class="sxs-lookup"><span data-stu-id="16aae-187">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="16aae-188">Nazwa zakresu</span><span class="sxs-lookup"><span data-stu-id="16aae-188">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="16aae-189">Identyfikator aplikacji (klienta) dla *aplikacji interfejsu API serwera*</span><span class="sxs-lookup"><span data-stu-id="16aae-189">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="16aae-190">Identyfikator URI identyfikatora aplikacji&dagger;</span><span class="sxs-lookup"><span data-stu-id="16aae-190">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="16aae-191">Przepływ użytkownika rejestracji/logowania</span><span class="sxs-lookup"><span data-stu-id="16aae-191">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                          |
| `{TENANT DOMAIN}`             | <span data-ttu-id="16aae-192">Domena podstawowa/Wydawca/dzierżawa</span><span class="sxs-lookup"><span data-stu-id="16aae-192">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |

<span data-ttu-id="16aae-193">&dagger;Blazor WebAssemblySzablon automatycznie dodaje schemat `api://` do argumentu identyfikatora aplikacji, który został przesłany w `dotnet new` poleceniu.</span><span class="sxs-lookup"><span data-stu-id="16aae-193">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="16aae-194">W przypadku podania identyfikatora URI aplikacji dla `{SERVER API APP ID URI}` symbolu zastępczego i jeśli schemat jest `api://` , Usuń schemat ( `api://` ) z argumentu, jak przykładowa wartość w powyższej tabeli pokazuje.</span><span class="sxs-lookup"><span data-stu-id="16aae-194">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="16aae-195">Jeśli identyfikator URI aplikacji jest wartością niestandardową lub ma inny schemat (na przykład `https://` w przypadku niezaufanej domeny wydawcy podobnej do `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ), należy ręcznie zaktualizować domyślny identyfikator URI zakresu i usunąć `api://` schemat po *`Client`* utworzeniu aplikacji przez szablon.</span><span class="sxs-lookup"><span data-stu-id="16aae-195">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="16aae-196">Aby uzyskać więcej informacji, zobacz Uwaga w sekcji [zakresy tokenu dostępu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="16aae-196">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="16aae-197">Blazor WebAssemblySzablon można zmienić w przyszłych wydaniach ASP.NET Core, aby rozwiązać te scenariusze.</span><span class="sxs-lookup"><span data-stu-id="16aae-197">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="16aae-198">Aby uzyskać więcej informacji, zobacz [podwójny schemat identyfikatora URI aplikacji z Blazor szablonem WASM (hostowany, Single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="16aae-198">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="16aae-199">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16aae-199">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="16aae-200">Zakres skonfigurowany przez Blazor szablon hostowany może być powtórzony na HOŚCIE identyfikatora URI aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16aae-200">The scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="16aae-201">Upewnij się, że zakres skonfigurowany dla `DefaultAccessTokenScopes` kolekcji jest poprawny w `Program.Main` ( `Program.cs` ) *`Client`* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16aae-201">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *`Client`* app.</span></span>

> [!NOTE]
> <span data-ttu-id="16aae-202">W Azure Portal *`Client`* **Identyfikator URI przekierowania** konfiguracji platformy aplikacji jest skonfigurowany dla portu 5001 dla aplikacji, które działają na serwerze Kestrel z ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="16aae-202">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="16aae-203">Jeśli *`Client`* aplikacja jest uruchamiana na losowo IIS Express porcie, port aplikacji można znaleźć we właściwościach *aplikacji interfejsu API serwera* w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="16aae-203">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="16aae-204">Jeśli port nie został wcześniej skonfigurowany przy użyciu *`Client`* znanego portu aplikacji, Wróć do *`Client`* rejestracji aplikacji w Azure Portal i zaktualizuj identyfikator URI przekierowania z prawidłowym portem.</span><span class="sxs-lookup"><span data-stu-id="16aae-204">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="16aae-205">*`Server`* Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="16aae-205">*`Server`* app configuration</span></span>

<span data-ttu-id="16aae-206">*Ta sekcja dotyczy **`Server`** aplikacji rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="16aae-206">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="16aae-207">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="16aae-207">Authentication package</span></span>

<span data-ttu-id="16aae-208">Pakiet zawiera wsparcie w zakresie uwierzytelniania i autoryzowania wywołań ASP.NET Core interfejsów API sieci Web [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) .</span><span class="sxs-lookup"><span data-stu-id="16aae-208">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="16aae-209">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="16aae-209">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="16aae-210">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="16aae-210">Authentication service support</span></span>

<span data-ttu-id="16aae-211">Metoda konfiguruje `AddAuthentication` usługi uwierzytelniania w ramach aplikacji i konfiguruje procedurę obsługi okaziciela JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="16aae-211">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="16aae-212"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A>Metoda ustawia określone parametry w obsłudze okaziciela JWT wymagane do weryfikacji tokenów emitowanych przez Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="16aae-212">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="16aae-213"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> i <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> upewnij się, że:</span><span class="sxs-lookup"><span data-stu-id="16aae-213"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="16aae-214">Aplikacja próbuje analizować tokeny i sprawdzać ich poprawność w żądaniach przychodzących.</span><span class="sxs-lookup"><span data-stu-id="16aae-214">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="16aae-215">Wszystkie żądania próbujące uzyskać dostęp do chronionego zasobu bez poprawnego poświadczenia nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="16aae-215">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="16aae-216">Użytkownik. Identity . Nazwij</span><span class="sxs-lookup"><span data-stu-id="16aae-216">User.Identity.Name</span></span>

<span data-ttu-id="16aae-217">Domyślnie wartość `User.Identity.Name` nie jest wypełniona.</span><span class="sxs-lookup"><span data-stu-id="16aae-217">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="16aae-218">Aby skonfigurować aplikację do odbierania wartości z `name` typu, należy skonfigurować <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="16aae-218">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="16aae-219">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="16aae-219">App settings</span></span>

<span data-ttu-id="16aae-220">`appsettings.json`Plik zawiera opcje konfigurowania procedury obsługi okaziciela JWT używanej do sprawdzania poprawności tokenów dostępu.</span><span class="sxs-lookup"><span data-stu-id="16aae-220">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="16aae-221">Przykład:</span><span class="sxs-lookup"><span data-stu-id="16aae-221">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="16aae-222">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="16aae-222">WeatherForecast controller</span></span>

<span data-ttu-id="16aae-223">Kontroler WeatherForecast ( *controllers/WeatherForecastController. cs* ) ujawnia chroniony interfejs API z [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybutem zastosowanym do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="16aae-223">The WeatherForecast controller ( *Controllers/WeatherForecastController.cs* ) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="16aae-224">**Ważne** jest, aby zrozumieć, że:</span><span class="sxs-lookup"><span data-stu-id="16aae-224">It's **important** to understand that:</span></span>

* <span data-ttu-id="16aae-225">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atrybut w tym kontrolerze interfejsu API jest jedynym warunkiem, że ten interfejs API jest chroniony przed nieautoryzowanym dostępem.</span><span class="sxs-lookup"><span data-stu-id="16aae-225">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="16aae-226">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atrybut używany w Blazor WebAssembly aplikacji służy tylko jako Wskazówka dla aplikacji, którą użytkownik powinien mieć autoryzację, aby aplikacja działała poprawnie.</span><span class="sxs-lookup"><span data-stu-id="16aae-226">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="16aae-227">*`Client`* Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="16aae-227">*`Client`* app configuration</span></span>

<span data-ttu-id="16aae-228">*Ta sekcja dotyczy **`Client`** aplikacji rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="16aae-228">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="16aae-229">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="16aae-229">Authentication package</span></span>

<span data-ttu-id="16aae-230">Gdy aplikacja zostanie utworzona w celu korzystania z pojedynczego konta B2C ( `IndividualB2C` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="16aae-230">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="16aae-231">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="16aae-231">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="16aae-232">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="16aae-232">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="16aae-233">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="16aae-233">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="16aae-234">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Pakiet zawiera przechodnie Dodawanie [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16aae-234">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="16aae-235">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="16aae-235">Authentication service support</span></span>

<span data-ttu-id="16aae-236">Dodano obsługę <xref:System.Net.Http.HttpClient> wystąpień, które obejmują tokeny dostępu podczas wykonywania żądań do projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="16aae-236">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="16aae-237">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="16aae-237">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="16aae-238">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="16aae-238">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="16aae-239">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pakiet.</span><span class="sxs-lookup"><span data-stu-id="16aae-239">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="16aae-240">Ta metoda konfiguruje usługi wymagane przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="16aae-240">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="16aae-241">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="16aae-241">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="16aae-242"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16aae-242">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="16aae-243">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD w witrynie Azure Portal podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16aae-243">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="16aae-244">Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="16aae-244">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="16aae-245">Przykład:</span><span class="sxs-lookup"><span data-stu-id="16aae-245">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="16aae-246">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="16aae-246">Access token scopes</span></span>

<span data-ttu-id="16aae-247">Domyślne zakresy tokenów dostępu reprezentują listę zakresów tokenów dostępu, które są następujące:</span><span class="sxs-lookup"><span data-stu-id="16aae-247">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="16aae-248">Uwzględnione domyślnie w żądaniu logowania.</span><span class="sxs-lookup"><span data-stu-id="16aae-248">Included by default in the sign in request.</span></span>
* <span data-ttu-id="16aae-249">Służy do aprowizacji tokenu dostępu zaraz po uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="16aae-249">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="16aae-250">Wszystkie zakresy muszą należeć do tej samej aplikacji na Azure Active Directory reguł.</span><span class="sxs-lookup"><span data-stu-id="16aae-250">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="16aae-251">Dodatkowe zakresy można dodać do dodatkowych aplikacji interfejsu API w razie potrzeby:</span><span class="sxs-lookup"><span data-stu-id="16aae-251">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="16aae-252">Blazor WebAssemblySzablon automatycznie dodaje schemat `api://` do argumentu identyfikatora aplikacji, który został przesłany w `dotnet new` poleceniu.</span><span class="sxs-lookup"><span data-stu-id="16aae-252">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="16aae-253">Podczas generowania aplikacji z Blazor szablonu projektu upewnij się, że wartość domyślnego zakresu tokenu dostępu używa poprawnej wartości identyfikatora URI aplikacji niestandardowej podanej w Azure Portal lub wartości z **jednym** z następujących formatów:</span><span class="sxs-lookup"><span data-stu-id="16aae-253">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="16aae-254">Gdy domena wydawcy katalogu jest **zaufana** , domyślnym zakresem tokenu dostępu jest zazwyczaj wartość podobna do poniższego przykładu, gdzie `API.Access` jest domyślną nazwą zakresu:</span><span class="sxs-lookup"><span data-stu-id="16aae-254">When the publisher domain of the directory is **trusted** , the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="16aae-255">Sprawdź wartość dla podwójnego schematu ( `api://api://...` ).</span><span class="sxs-lookup"><span data-stu-id="16aae-255">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="16aae-256">Jeśli jest obecny schemat podwójny, usuń pierwszy `api://` schemat z wartości.</span><span class="sxs-lookup"><span data-stu-id="16aae-256">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="16aae-257">Gdy domena wydawcy katalogu nie jest **zaufana** , domyślnym zakresem tokenu dostępu jest zwykle wartość podobna do poniższego przykładu, gdzie `API.Access` jest domyślną nazwą zakresu:</span><span class="sxs-lookup"><span data-stu-id="16aae-257">When the publisher domain of the directory is **untrusted** , the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="16aae-258">Sprawdź wartość dodatkowego `api://` schematu ( `api://https://contoso.onmicrosoft.com/...` ).</span><span class="sxs-lookup"><span data-stu-id="16aae-258">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="16aae-259">Jeśli istnieje dodatkowy `api://` schemat, Usuń `api://` schemat z wartości.</span><span class="sxs-lookup"><span data-stu-id="16aae-259">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="16aae-260">Blazor WebAssemblySzablon można zmienić w przyszłych wydaniach ASP.NET Core, aby rozwiązać te scenariusze.</span><span class="sxs-lookup"><span data-stu-id="16aae-260">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="16aae-261">Aby uzyskać więcej informacji, zobacz [podwójny schemat identyfikatora URI aplikacji z Blazor szablonem WASM (hostowany, Single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="16aae-261">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="16aae-262">Określ dodatkowe zakresy z `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="16aae-262">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="16aae-263">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="16aae-263">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="16aae-264">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="16aae-264">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="16aae-265">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="16aae-265">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="16aae-266">Tryb logowania</span><span class="sxs-lookup"><span data-stu-id="16aae-266">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="16aae-267">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="16aae-267">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="16aae-268">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="16aae-268">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="16aae-269">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="16aae-269">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="16aae-270">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="16aae-270">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="16aae-271">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="16aae-271">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="16aae-272">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="16aae-272">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="16aae-273">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="16aae-273">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="16aae-274">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="16aae-274">Run the app</span></span>

<span data-ttu-id="16aae-275">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="16aae-275">Run the app from the Server project.</span></span> <span data-ttu-id="16aae-276">W przypadku korzystania z programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="16aae-276">When using Visual Studio, either:</span></span>

* <span data-ttu-id="16aae-277">Ustaw listę rozwijaną **projekty startowe** na pasku narzędzi do *aplikacji interfejsu API serwera* i wybierz przycisk **Uruchom** .</span><span class="sxs-lookup"><span data-stu-id="16aae-277">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="16aae-278">Wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .</span><span class="sxs-lookup"><span data-stu-id="16aae-278">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="16aae-279">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="16aae-279">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="16aae-280">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="16aae-280">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="16aae-281">Samouczek: Tworzenie dzierżawy usługi Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="16aae-281">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="16aae-282">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="16aae-282">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
