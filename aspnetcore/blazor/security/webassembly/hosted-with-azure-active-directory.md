---
title: Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core przy użyciu Azure Active Directory
author: guardrex
description: Dowiedz się, jak zabezpieczyć Blazor WebAssembly aplikację hostowaną ASP.NET Core przy użyciu Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 11/02/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: e38838930dba70abfcfe4db9c204132e67866041
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280935"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="37379-103">Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core przy użyciu Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="37379-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="37379-104">W tym artykule opisano sposób tworzenia [hostowanej Blazor WebAssembly aplikacji](xref:blazor/hosting-models#blazor-webassembly) korzystającej z usługi [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="37379-104">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="37379-105">W przypadku Blazor WebAssembly aplikacji utworzonych w programie Visual Studio, które są skonfigurowane do obsługi kont w katalogu organizacji usługi AAD, program Visual Studio nie obecnie konfiguruje projekty rozwiązania lub Azure Portal rejestracje aplikacji na potrzeby generowania projektu.</span><span class="sxs-lookup"><span data-stu-id="37379-105">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't currently configure the solution's projects or the Azure portal app registrations correctly on project generation.</span></span> <span data-ttu-id="37379-106">Zostanie to rozkierowane w przyszłej wersji programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="37379-106">This will be addressed in a future release of Visual Studio.</span></span>
>
> <span data-ttu-id="37379-107">W tym artykule pokazano, jak utworzyć rozwiązanie i rejestracje portalu aplikacji platformy Azure za pomocą polecenia platformy .NET CLI `dotnet new` oraz ręcznie tworząc rejestracje aplikacji w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="37379-107">This article shows how to create the solution and Azure app portal registrations with the .NET CLI `dotnet new` command and by manually creating the app registrations in the Azure portal.</span></span>
>
> <span data-ttu-id="37379-108">Jeśli wolisz utworzyć rozwiązanie i rejestracje aplikacji platformy Azure w programie Visual Studio przed zaktualizowaniem środowiska IDE, zapoznaj się z **_każdą sekcją tego artykułu_** i Potwierdź lub zaktualizuj konfiguracje aplikacji oraz rejestracje aplikacji po utworzeniu rozwiązania przez program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="37379-108">If you prefer to create the solution and Azure app registrations with Visual Studio before the IDE is updated, refer to **_each section of this article_** and confirm or update the apps' configurations and the apps' registrations after Visual Studio creates the solution.</span></span>

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="37379-109">Rejestrowanie aplikacji w usłudze AAD i tworzenie rozwiązania</span><span class="sxs-lookup"><span data-stu-id="37379-109">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="37379-110">Tworzenie dzierżawy</span><span class="sxs-lookup"><span data-stu-id="37379-110">Create a tenant</span></span>

<span data-ttu-id="37379-111">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Konfigurowanie dzierżawy](/azure/active-directory/develop/quickstart-create-new-tenant) w celu utworzenia dzierżawy w usłudze AAD.</span><span class="sxs-lookup"><span data-stu-id="37379-111">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="37379-112">Rejestrowanie aplikacji interfejsu API serwera</span><span class="sxs-lookup"><span data-stu-id="37379-112">Register a server API app</span></span>

<span data-ttu-id="37379-113">Zarejestruj aplikację usługi AAD dla *aplikacji interfejsu API serwera*:</span><span class="sxs-lookup"><span data-stu-id="37379-113">Register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="37379-114">W **Azure Active Directory**  >  **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="37379-114">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="37379-115">Podaj **nazwę** aplikacji (na przykład **Blazor Server AAD**).</span><span class="sxs-lookup"><span data-stu-id="37379-115">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="37379-116">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="37379-116">Choose a **Supported account types**.</span></span> <span data-ttu-id="37379-117">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="37379-117">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="37379-118">*Aplikacja interfejsu API serwera* nie wymaga **identyfikatora URI przekierowania** w tym scenariuszu, więc pozostaw listę rozwijaną w **sieci Web** i nie wprowadzaj identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="37379-118">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="37379-119">Wyczyść pole wyboru **uprawnienia**  >  **Udziel uprawnień administrator do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="37379-119">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="37379-120">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="37379-120">Select **Register**.</span></span>

<span data-ttu-id="37379-121">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="37379-121">Record the following information:</span></span>

* <span data-ttu-id="37379-122">*Aplikacja interfejsu API serwera* Identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="37379-122">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="37379-123">Identyfikator katalogu (dzierżawy) (na przykład `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="37379-123">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="37379-124">Domena podstawowa/Wydawca/dzierżawa usługi AAD (na przykład `contoso.onmicrosoft.com` ): domena jest dostępna jako **domena wydawcy** w bloku **znakowania** Azure Portal dla zarejestrowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="37379-124">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="37379-125">W obszarze **uprawnienia interfejsu API** usuń uprawnienie **Microsoft Graph**  >  **User. Read** , ponieważ aplikacja nie wymaga dostępu do profilu logowania lub użytkownika.</span><span class="sxs-lookup"><span data-stu-id="37379-125">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="37379-126">W obszarze **Uwidacznianie interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="37379-126">In **Expose an API**:</span></span>

1. <span data-ttu-id="37379-127">Wybierz polecenie **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="37379-127">Select **Add a scope**.</span></span>
1. <span data-ttu-id="37379-128">Wybierz przycisk **Zapisz i kontynuuj**.</span><span class="sxs-lookup"><span data-stu-id="37379-128">Select **Save and continue**.</span></span>
1. <span data-ttu-id="37379-129">Podaj **nazwę zakresu** (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="37379-129">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="37379-130">Podaj **nazwę wyświetlaną zgody administratora** (na przykład `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="37379-130">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="37379-131">Podaj **Opis zgody administratora** (na przykład `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="37379-131">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="37379-132">Upewnij się, że **stan** jest ustawiony na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="37379-132">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="37379-133">Wybierz pozycję **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="37379-133">Select **Add scope**.</span></span>

<span data-ttu-id="37379-134">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="37379-134">Record the following information:</span></span>

* <span data-ttu-id="37379-135">Identyfikator URI identyfikatora aplikacji (na przykład `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` , `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` lub wartość niestandardowa, którą podano)</span><span class="sxs-lookup"><span data-stu-id="37379-135">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="37379-136">Nazwa zakresu (na przykład `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="37379-136">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="37379-137">Rejestrowanie aplikacji klienckiej</span><span class="sxs-lookup"><span data-stu-id="37379-137">Register a client app</span></span>

<span data-ttu-id="37379-138">Zarejestruj aplikację usługi AAD dla *aplikacji klienckiej*:</span><span class="sxs-lookup"><span data-stu-id="37379-138">Register an AAD app for the *Client app*:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="37379-139">W **Azure Active Directory** > **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="37379-139">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="37379-140">Podaj **nazwę** aplikacji (na przykład **Blazor klienta AAD**).</span><span class="sxs-lookup"><span data-stu-id="37379-140">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="37379-141">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="37379-141">Choose a **Supported account types**.</span></span> <span data-ttu-id="37379-142">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="37379-142">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="37379-143">Ustaw listę rozwijaną **URI przekierowania** na **aplikację jednostronicową (Spa)** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="37379-143">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="37379-144">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="37379-144">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="37379-145">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="37379-145">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="37379-146">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we *`Server`* właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="37379-146">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="37379-147">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="37379-147">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="37379-148">W sekcji [Tworzenie aplikacji](#create-the-app) zostanie wyświetlona informacja przypominająca IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="37379-148">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="37379-149">Wyczyść pole wyboru **uprawnienia** > **Udziel uprawnień administrator do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="37379-149">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="37379-150">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="37379-150">Select **Register**.</span></span>

<span data-ttu-id="37379-151">Zapisz *`Client`* Identyfikator aplikacji aplikacji (na przykład `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="37379-151">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="37379-152">W  obszarze > **Konfiguracja platformy** uwierzytelniania > **aplikacja jednostronicowa (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="37379-152">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="37379-153">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="37379-153">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="37379-154">W przypadku **niejawnego udzielenia** upewnij się, że **nie** wybrano pól wyboru dla **tokenów dostępu** i **tokenów identyfikatorów** .</span><span class="sxs-lookup"><span data-stu-id="37379-154">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="37379-155">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="37379-155">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="37379-156">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="37379-156">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="37379-157">W **Azure Active Directory** > **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="37379-157">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="37379-158">Podaj **nazwę** aplikacji (na przykład **Blazor klienta AAD**).</span><span class="sxs-lookup"><span data-stu-id="37379-158">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="37379-159">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="37379-159">Choose a **Supported account types**.</span></span> <span data-ttu-id="37379-160">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="37379-160">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="37379-161">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="37379-161">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="37379-162">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="37379-162">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="37379-163">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="37379-163">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="37379-164">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we *`Server`* właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="37379-164">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="37379-165">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="37379-165">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="37379-166">W sekcji [Tworzenie aplikacji](#create-the-app) zostanie wyświetlona informacja przypominająca IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="37379-166">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="37379-167">Wyczyść pole wyboru **uprawnienia** > **Udziel uprawnień administrator do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="37379-167">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="37379-168">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="37379-168">Select **Register**.</span></span>

<span data-ttu-id="37379-169">Zapisz *`Client`* Identyfikator aplikacji aplikacji (na przykład `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="37379-169">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="37379-170">W  obszarze > **konfiguracje platformy** uwierzytelniania w > **sieci Web**:</span><span class="sxs-lookup"><span data-stu-id="37379-170">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="37379-171">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="37379-171">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="37379-172">W przypadku **niejawnego przydzielenia** zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="37379-172">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="37379-173">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="37379-173">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="37379-174">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="37379-174">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="37379-175">W **uprawnienia interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="37379-175">In **API permissions**:</span></span>

1. <span data-ttu-id="37379-176">Upewnij się, że aplikacja ma **Microsoft Graph**  >  uprawnienie **użytkownika. odczyt** .</span><span class="sxs-lookup"><span data-stu-id="37379-176">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="37379-177">Wybierz pozycję **Dodaj uprawnienia** , a następnie **Moje interfejsy API**.</span><span class="sxs-lookup"><span data-stu-id="37379-177">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="37379-178">Wybierz *aplikację interfejsu API serwera* z kolumny **Nazwa** (na przykład **Blazor Server AAD**).</span><span class="sxs-lookup"><span data-stu-id="37379-178">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="37379-179">Otwórz listę **interfejsów API** .</span><span class="sxs-lookup"><span data-stu-id="37379-179">Open the **API** list.</span></span>
1. <span data-ttu-id="37379-180">Włącz dostęp do interfejsu API (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="37379-180">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="37379-181">Wybierz pozycję **Dodaj uprawnienia**.</span><span class="sxs-lookup"><span data-stu-id="37379-181">Select **Add permissions**.</span></span>
1. <span data-ttu-id="37379-182">Wybierz przycisk **Udziel zgody administratora na {nazwa dzierżawy}** .</span><span class="sxs-lookup"><span data-stu-id="37379-182">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="37379-183">Wybierz pozycję **Tak**, aby potwierdzić.</span><span class="sxs-lookup"><span data-stu-id="37379-183">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="37379-184">Tworzenie aplikacji</span><span class="sxs-lookup"><span data-stu-id="37379-184">Create the app</span></span>

<span data-ttu-id="37379-185">W pustym folderze Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="37379-185">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="37379-186">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="37379-186">Placeholder</span></span>                  | <span data-ttu-id="37379-187">Nazwa Azure Portal</span><span class="sxs-lookup"><span data-stu-id="37379-187">Azure portal name</span></span>                                     | <span data-ttu-id="37379-188">Przykład</span><span class="sxs-lookup"><span data-stu-id="37379-188">Example</span></span>                                        |
| ---------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="37379-189">Identyfikator aplikacji (klienta) dla *`Client`* aplikacji</span><span class="sxs-lookup"><span data-stu-id="37379-189">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="37379-190">Nazwa zakresu</span><span class="sxs-lookup"><span data-stu-id="37379-190">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="37379-191">Identyfikator aplikacji (klienta) dla *aplikacji interfejsu API serwera*</span><span class="sxs-lookup"><span data-stu-id="37379-191">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="37379-192">Identyfikator URI identyfikatora aplikacji&dagger;</span><span class="sxs-lookup"><span data-stu-id="37379-192">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{TENANT DOMAIN}`            | <span data-ttu-id="37379-193">Domena podstawowa/Wydawca/dzierżawa</span><span class="sxs-lookup"><span data-stu-id="37379-193">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |
| `{TENANT ID}`                | <span data-ttu-id="37379-194">Identyfikator katalogu (dzierżawcy)</span><span class="sxs-lookup"><span data-stu-id="37379-194">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`         |

<span data-ttu-id="37379-195">&dagger;Blazor WebAssemblySzablon automatycznie dodaje schemat `api://` do argumentu identyfikatora aplikacji, który został przesłany w `dotnet new` poleceniu.</span><span class="sxs-lookup"><span data-stu-id="37379-195">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="37379-196">W przypadku podania identyfikatora URI aplikacji dla `{SERVER API APP ID URI}` symbolu zastępczego i jeśli schemat jest `api://` , Usuń schemat ( `api://` ) z argumentu, jak przykładowa wartość w powyższej tabeli pokazuje.</span><span class="sxs-lookup"><span data-stu-id="37379-196">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="37379-197">Jeśli identyfikator URI aplikacji jest wartością niestandardową lub ma inny schemat (na przykład `https://` w przypadku niezaufanej domeny wydawcy podobnej do `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ), należy ręcznie zaktualizować domyślny identyfikator URI zakresu i usunąć `api://` schemat po *`Client`* utworzeniu aplikacji przez szablon.</span><span class="sxs-lookup"><span data-stu-id="37379-197">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="37379-198">Aby uzyskać więcej informacji, zobacz Uwaga w sekcji [zakresy tokenu dostępu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="37379-198">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="37379-199">Blazor WebAssemblySzablon można zmienić w przyszłych wydaniach ASP.NET Core, aby rozwiązać te scenariusze.</span><span class="sxs-lookup"><span data-stu-id="37379-199">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="37379-200">Aby uzyskać więcej informacji, zobacz [podwójny schemat identyfikatora URI aplikacji z Blazor szablonem WASM (hostowany, Single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="37379-200">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="37379-201">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="37379-201">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="37379-202">Zmiana konfiguracji może być wymagana w przypadku korzystania z dzierżawy platformy Azure z niezweryfikowaną domeną wydawcy, która została opisana w sekcji [Ustawienia aplikacji](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="37379-202">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="37379-203">Zmiana konfiguracji może być wymagana w przypadku korzystania z dzierżawy platformy Azure z niezweryfikowaną domeną wydawcy, która jest opisana w sekcji [zakresy tokenu dostępu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="37379-203">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="37379-204">W Azure Portal *`Client`* **Identyfikator URI przekierowania** konfiguracji platformy aplikacji jest skonfigurowany dla portu 5001 dla aplikacji, które działają na serwerze Kestrel z ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="37379-204">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="37379-205">Jeśli *`Client`* aplikacja jest uruchamiana na losowo IIS Express porcie, port aplikacji można znaleźć we właściwościach *aplikacji interfejsu API serwera* w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="37379-205">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="37379-206">Jeśli port nie został wcześniej skonfigurowany przy użyciu *`Client`* znanego portu aplikacji, Wróć do *`Client`* rejestracji aplikacji w Azure Portal i zaktualizuj identyfikator URI przekierowania z prawidłowym portem.</span><span class="sxs-lookup"><span data-stu-id="37379-206">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="37379-207">*`Server`* Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="37379-207">*`Server`* app configuration</span></span>

<span data-ttu-id="37379-208">*Ta sekcja dotyczy **`Server`** aplikacji rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="37379-208">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="37379-209">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="37379-209">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="37379-210">Obsługa uwierzytelniania i autoryzacji wywołań ASP.NET Core interfejsów API sieci Web za pomocą platformy firmy Microsoft Identity jest zapewniana przez następujące pakiety:</span><span class="sxs-lookup"><span data-stu-id="37379-210">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="37379-211">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem NuGet.org.</span><span class="sxs-lookup"><span data-stu-id="37379-211">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="37379-212">Pakiet zawiera wsparcie w zakresie uwierzytelniania i autoryzowania wywołań ASP.NET Core interfejsów API sieci Web [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) .</span><span class="sxs-lookup"><span data-stu-id="37379-212">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="37379-213">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="37379-213">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="37379-214">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="37379-214">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="37379-215">Metoda konfiguruje `AddAuthentication` usługi uwierzytelniania w ramach aplikacji i konfiguruje procedurę obsługi okaziciela JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="37379-215">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="37379-216"><xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A>Metoda konfiguruje usługi do ochrony internetowego interfejsu API za pomocą Identity platformy Microsoft Platform v 2.0.</span><span class="sxs-lookup"><span data-stu-id="37379-216">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="37379-217">Ta metoda oczekuje `AzureAd` sekcji w konfiguracji aplikacji z ustawieniami niezbędnymi do zainicjowania opcji uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="37379-217">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="37379-218">Metoda konfiguruje `AddAuthentication` usługi uwierzytelniania w ramach aplikacji i konfiguruje procedurę obsługi okaziciela JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="37379-218">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="37379-219"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>Metoda ustawia określone parametry w obsłudze okaziciela JWT wymagane do weryfikacji tokenów emitowanych przez Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="37379-219">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="37379-220"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> i <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> upewnij się, że:</span><span class="sxs-lookup"><span data-stu-id="37379-220"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="37379-221">Aplikacja próbuje analizować tokeny i sprawdzać ich poprawność w żądaniach przychodzących.</span><span class="sxs-lookup"><span data-stu-id="37379-221">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="37379-222">Wszystkie żądania próbujące uzyskać dostęp do chronionego zasobu bez poprawnego poświadczenia nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="37379-222">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="37379-223">Użytkownik. Identity . Nazwij</span><span class="sxs-lookup"><span data-stu-id="37379-223">User.Identity.Name</span></span>

<span data-ttu-id="37379-224">Domyślnie *`Server`* interfejs API aplikacji wypełnia `User.Identity.Name` wartość z `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` typu "typ" (na przykład `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="37379-224">By default, the *`Server`* app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="37379-225">Aby skonfigurować aplikację do odbierania wartości z `name` typu zgłoszenia:</span><span class="sxs-lookup"><span data-stu-id="37379-225">To configure the app to receive the value from the `name` claim type:</span></span>

* <span data-ttu-id="37379-226">Dodaj przestrzeń nazw dla <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> do `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="37379-226">Add a namespace for <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> to `Startup.cs`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="37379-227">Skonfiguruj <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="37379-227">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="37379-228">Skonfiguruj <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="37379-228">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a><span data-ttu-id="37379-229">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="37379-229">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="37379-230">`appsettings.json`Plik zawiera opcje konfigurowania procedury obsługi okaziciela JWT używanej do sprawdzania poprawności tokenów dostępu:</span><span class="sxs-lookup"><span data-stu-id="37379-230">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

<span data-ttu-id="37379-231">Przykład:</span><span class="sxs-lookup"><span data-stu-id="37379-231">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/blazor/includes/security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="37379-232">`appsettings.json`Plik zawiera opcje konfigurowania procedury obsługi okaziciela JWT używanej do sprawdzania poprawności tokenów dostępu:</span><span class="sxs-lookup"><span data-stu-id="37379-232">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="37379-233">Przykład:</span><span class="sxs-lookup"><span data-stu-id="37379-233">Example:</span></span>

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

::: moniker-end

### <a name="weatherforecast-controller"></a><span data-ttu-id="37379-234">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="37379-234">WeatherForecast controller</span></span>

<span data-ttu-id="37379-235">Kontroler WeatherForecast (*controllers/WeatherForecastController. cs*) ujawnia chroniony interfejs API z [ `[Authorize]` atrybutem](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) zastosowanym do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="37379-235">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) applied to the controller.</span></span> <span data-ttu-id="37379-236">**Ważne** jest, aby zrozumieć, że:</span><span class="sxs-lookup"><span data-stu-id="37379-236">It's **important** to understand that:</span></span>

* <span data-ttu-id="37379-237">[ `[Authorize]` Atrybut](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) w tym kontrolerze interfejsu API jest jedynym warunkiem, że ten interfejs API jest chroniony przed nieautoryzowanym dostępem.</span><span class="sxs-lookup"><span data-stu-id="37379-237">The [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="37379-238">[ `[Authorize]` Atrybut](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) używany w Blazor WebAssembly aplikacji służy tylko jako Wskazówka dla aplikacji, którą użytkownik powinien mieć autoryzację, aby aplikacja działała poprawnie.</span><span class="sxs-lookup"><span data-stu-id="37379-238">The [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="37379-239">*`Client`* Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="37379-239">*`Client`* app configuration</span></span>

<span data-ttu-id="37379-240">*Ta sekcja dotyczy **`Client`** aplikacji rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="37379-240">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="37379-241">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="37379-241">Authentication package</span></span>

<span data-ttu-id="37379-242">Gdy aplikacja zostanie utworzona w celu korzystania z kont służbowych ( `SingleOrg` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="37379-242">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="37379-243">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="37379-243">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="37379-244">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="37379-244">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="37379-245">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="37379-245">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="37379-246">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Pakiet zawiera przechodnie Dodawanie [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="37379-246">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="37379-247">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="37379-247">Authentication service support</span></span>

<span data-ttu-id="37379-248">Dodano obsługę <xref:System.Net.Http.HttpClient> wystąpień, które obejmują tokeny dostępu podczas wykonywania żądań do projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="37379-248">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="37379-249">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="37379-249">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="37379-250">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="37379-250">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="37379-251">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pakiet.</span><span class="sxs-lookup"><span data-stu-id="37379-251">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="37379-252">Ta metoda konfiguruje usługi wymagane przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="37379-252">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="37379-253">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="37379-253">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="37379-254"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="37379-254">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="37379-255">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD w witrynie Azure Portal podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="37379-255">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="37379-256">Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="37379-256">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="37379-257">Przykład:</span><span class="sxs-lookup"><span data-stu-id="37379-257">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="37379-258">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="37379-258">Access token scopes</span></span>

<span data-ttu-id="37379-259">Domyślne zakresy tokenów dostępu reprezentują listę zakresów tokenów dostępu, które są następujące:</span><span class="sxs-lookup"><span data-stu-id="37379-259">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="37379-260">Uwzględnione domyślnie w żądaniu logowania.</span><span class="sxs-lookup"><span data-stu-id="37379-260">Included by default in the sign in request.</span></span>
* <span data-ttu-id="37379-261">Służy do aprowizacji tokenu dostępu zaraz po uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="37379-261">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="37379-262">Wszystkie zakresy muszą należeć do tej samej aplikacji na Azure Active Directory reguł.</span><span class="sxs-lookup"><span data-stu-id="37379-262">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="37379-263">Dodatkowe zakresy można dodać do dodatkowych aplikacji interfejsu API w razie potrzeby:</span><span class="sxs-lookup"><span data-stu-id="37379-263">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="37379-264">Blazor WebAssemblySzablon automatycznie dodaje schemat `api://` do argumentu identyfikatora aplikacji, który został przesłany w `dotnet new` poleceniu.</span><span class="sxs-lookup"><span data-stu-id="37379-264">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="37379-265">Podczas generowania aplikacji z Blazor szablonu projektu upewnij się, że wartość domyślnego zakresu tokenu dostępu używa poprawnej wartości identyfikatora URI aplikacji niestandardowej podanej w Azure Portal lub wartości z **jednym** z następujących formatów:</span><span class="sxs-lookup"><span data-stu-id="37379-265">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="37379-266">Gdy domena wydawcy katalogu jest **zaufana**, domyślnym zakresem tokenu dostępu jest zazwyczaj wartość podobna do poniższego przykładu, gdzie `API.Access` jest domyślną nazwą zakresu:</span><span class="sxs-lookup"><span data-stu-id="37379-266">When the publisher domain of the directory is **trusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="37379-267">Sprawdź wartość dla podwójnego schematu ( `api://api://...` ).</span><span class="sxs-lookup"><span data-stu-id="37379-267">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="37379-268">Jeśli jest obecny schemat podwójny, usuń pierwszy `api://` schemat z wartości.</span><span class="sxs-lookup"><span data-stu-id="37379-268">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="37379-269">Gdy domena wydawcy katalogu nie jest **zaufana**, domyślnym zakresem tokenu dostępu jest zwykle wartość podobna do poniższego przykładu, gdzie `API.Access` jest domyślną nazwą zakresu:</span><span class="sxs-lookup"><span data-stu-id="37379-269">When the publisher domain of the directory is **untrusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="37379-270">Sprawdź wartość dodatkowego `api://` schematu ( `api://https://contoso.onmicrosoft.com/...` ).</span><span class="sxs-lookup"><span data-stu-id="37379-270">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="37379-271">Jeśli istnieje dodatkowy `api://` schemat, Usuń `api://` schemat z wartości.</span><span class="sxs-lookup"><span data-stu-id="37379-271">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="37379-272">Blazor WebAssemblySzablon można zmienić w przyszłych wydaniach ASP.NET Core, aby rozwiązać te scenariusze.</span><span class="sxs-lookup"><span data-stu-id="37379-272">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="37379-273">Aby uzyskać więcej informacji, zobacz [podwójny schemat identyfikatora URI aplikacji z Blazor szablonem WASM (hostowany, Single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="37379-273">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="37379-274">Określ dodatkowe zakresy z `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="37379-274">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="37379-275">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="37379-275">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="37379-276">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="37379-276">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="37379-277">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="37379-277">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="37379-278">Tryb logowania</span><span class="sxs-lookup"><span data-stu-id="37379-278">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="37379-279">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="37379-279">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="37379-280">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="37379-280">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="37379-281">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="37379-281">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="37379-282">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="37379-282">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="37379-283">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="37379-283">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="37379-284">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="37379-284">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="37379-285">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="37379-285">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="37379-286">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="37379-286">Run the app</span></span>

<span data-ttu-id="37379-287">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="37379-287">Run the app from the Server project.</span></span> <span data-ttu-id="37379-288">W przypadku korzystania z programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="37379-288">When using Visual Studio, either:</span></span>

* <span data-ttu-id="37379-289">Ustaw listę rozwijaną **projekty startowe** na pasku narzędzi do *aplikacji interfejsu API serwera* i wybierz przycisk **Uruchom** .</span><span class="sxs-lookup"><span data-stu-id="37379-289">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="37379-290">Wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .</span><span class="sxs-lookup"><span data-stu-id="37379-290">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="37379-291">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="37379-291">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="37379-292">Utwórz niestandardową wersję biblioteki MSAL JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37379-292">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="37379-293">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="37379-293">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="37379-294">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="37379-294">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
* [<span data-ttu-id="37379-295">Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="37379-295">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app)
