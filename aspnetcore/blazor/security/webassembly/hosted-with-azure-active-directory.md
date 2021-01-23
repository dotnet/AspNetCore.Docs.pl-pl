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
ms.openlocfilehash: f0c7723e8de7167b4fe5021ea1ca26e7cf198581
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710571"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="17323-103">Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core przy użyciu Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="17323-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="17323-104">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="17323-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="17323-105">W tym artykule opisano sposób tworzenia [hostowanej Blazor WebAssembly aplikacji](xref:blazor/hosting-models#blazor-webassembly) korzystającej z usługi [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="17323-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="17323-106">W przypadku Blazor WebAssembly aplikacji utworzonych w programie Visual Studio, które są skonfigurowane do obsługi kont w katalogu organizacji usługi AAD, program Visual Studio nie obecnie konfiguruje projekty rozwiązania lub Azure Portal rejestracje aplikacji na potrzeby generowania projektu.</span><span class="sxs-lookup"><span data-stu-id="17323-106">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't currently configure the solution's projects or the Azure portal app registrations correctly on project generation.</span></span> <span data-ttu-id="17323-107">Zostanie to rozkierowane w przyszłej wersji programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="17323-107">This will be addressed in a future release of Visual Studio.</span></span>
>
> <span data-ttu-id="17323-108">W tym artykule pokazano, jak utworzyć rozwiązanie i rejestracje portalu aplikacji platformy Azure za pomocą polecenia platformy .NET CLI `dotnet new` oraz ręcznie tworząc rejestracje aplikacji w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="17323-108">This article shows how to create the solution and Azure app portal registrations with the .NET CLI `dotnet new` command and by manually creating the app registrations in the Azure portal.</span></span>
>
> <span data-ttu-id="17323-109">Jeśli wolisz utworzyć rozwiązanie i rejestracje aplikacji platformy Azure w programie Visual Studio przed zaktualizowaniem środowiska IDE, zapoznaj się z **_każdą sekcją tego artykułu_** i Potwierdź lub zaktualizuj konfiguracje aplikacji oraz rejestracje aplikacji po utworzeniu rozwiązania przez program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="17323-109">If you prefer to create the solution and Azure app registrations with Visual Studio before the IDE is updated, refer to **_each section of this article_** and confirm or update the apps' configurations and the apps' registrations after Visual Studio creates the solution.</span></span>

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="17323-110">Rejestrowanie aplikacji w usłudze AAD i tworzenie rozwiązania</span><span class="sxs-lookup"><span data-stu-id="17323-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="17323-111">Tworzenie dzierżawy</span><span class="sxs-lookup"><span data-stu-id="17323-111">Create a tenant</span></span>

<span data-ttu-id="17323-112">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Konfigurowanie dzierżawy](/azure/active-directory/develop/quickstart-create-new-tenant) w celu utworzenia dzierżawy w usłudze AAD.</span><span class="sxs-lookup"><span data-stu-id="17323-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="17323-113">Rejestrowanie aplikacji interfejsu API serwera</span><span class="sxs-lookup"><span data-stu-id="17323-113">Register a server API app</span></span>

<span data-ttu-id="17323-114">Zarejestruj aplikację usługi AAD dla *aplikacji interfejsu API serwera*:</span><span class="sxs-lookup"><span data-stu-id="17323-114">Register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="17323-115">W **Azure Active Directory**  >  **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="17323-115">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="17323-116">Podaj **nazwę** aplikacji (na przykład **Blazor Server AAD**).</span><span class="sxs-lookup"><span data-stu-id="17323-116">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="17323-117">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="17323-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="17323-118">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="17323-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="17323-119">*Aplikacja interfejsu API serwera* nie wymaga **identyfikatora URI przekierowania** w tym scenariuszu, więc pozostaw listę rozwijaną w **sieci Web** i nie wprowadzaj identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="17323-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="17323-120">Wyczyść pole wyboru **uprawnienia**  >  **Udziel uprawnień administrator do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="17323-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="17323-121">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="17323-121">Select **Register**.</span></span>

<span data-ttu-id="17323-122">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="17323-122">Record the following information:</span></span>

* <span data-ttu-id="17323-123">*Aplikacja interfejsu API serwera* Identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="17323-123">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="17323-124">Identyfikator katalogu (dzierżawy) (na przykład `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="17323-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="17323-125">Domena podstawowa/Wydawca/dzierżawa usługi AAD (na przykład `contoso.onmicrosoft.com` ): domena jest dostępna jako **domena wydawcy** w bloku **znakowania** Azure Portal dla zarejestrowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="17323-125">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="17323-126">W obszarze **uprawnienia interfejsu API** usuń uprawnienie **Microsoft Graph**  >  **User. Read** , ponieważ aplikacja nie wymaga dostępu do profilu logowania lub użytkownika.</span><span class="sxs-lookup"><span data-stu-id="17323-126">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="17323-127">W obszarze **Uwidacznianie interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="17323-127">In **Expose an API**:</span></span>

1. <span data-ttu-id="17323-128">Wybierz polecenie **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="17323-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="17323-129">Wybierz przycisk **Zapisz i kontynuuj**.</span><span class="sxs-lookup"><span data-stu-id="17323-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="17323-130">Podaj **nazwę zakresu** (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="17323-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="17323-131">Podaj **nazwę wyświetlaną zgody administratora** (na przykład `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="17323-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="17323-132">Podaj **Opis zgody administratora** (na przykład `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="17323-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="17323-133">Upewnij się, że **stan** jest ustawiony na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="17323-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="17323-134">Wybierz pozycję **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="17323-134">Select **Add scope**.</span></span>

<span data-ttu-id="17323-135">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="17323-135">Record the following information:</span></span>

* <span data-ttu-id="17323-136">Identyfikator URI identyfikatora aplikacji (na przykład `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` , `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` lub wartość niestandardowa, którą podano)</span><span class="sxs-lookup"><span data-stu-id="17323-136">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="17323-137">Nazwa zakresu (na przykład `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="17323-137">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="17323-138">Rejestrowanie aplikacji klienckiej</span><span class="sxs-lookup"><span data-stu-id="17323-138">Register a client app</span></span>

<span data-ttu-id="17323-139">Zarejestruj aplikację usługi AAD dla *aplikacji klienckiej*:</span><span class="sxs-lookup"><span data-stu-id="17323-139">Register an AAD app for the *Client app*:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="17323-140">W **Azure Active Directory** > **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="17323-140">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="17323-141">Podaj **nazwę** aplikacji (na przykład **Blazor klienta AAD**).</span><span class="sxs-lookup"><span data-stu-id="17323-141">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="17323-142">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="17323-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="17323-143">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="17323-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="17323-144">Ustaw listę rozwijaną **URI przekierowania** na **aplikację jednostronicową (Spa)** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="17323-144">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="17323-145">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="17323-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="17323-146">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="17323-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="17323-147">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we *`Server`* właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="17323-147">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="17323-148">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="17323-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="17323-149">W sekcji [Tworzenie aplikacji](#create-the-app) zostanie wyświetlona informacja przypominająca IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="17323-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="17323-150">Wyczyść pole wyboru **uprawnienia** > **Udziel uprawnień administrator do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="17323-150">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="17323-151">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="17323-151">Select **Register**.</span></span>

<span data-ttu-id="17323-152">Zapisz *`Client`* Identyfikator aplikacji aplikacji (na przykład `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="17323-152">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="17323-153">W  obszarze > **Konfiguracja platformy** uwierzytelniania > **aplikacja jednostronicowa (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="17323-153">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="17323-154">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="17323-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="17323-155">W przypadku **niejawnego udzielenia** upewnij się, że **nie** wybrano pól wyboru dla **tokenów dostępu** i **tokenów identyfikatorów** .</span><span class="sxs-lookup"><span data-stu-id="17323-155">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="17323-156">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="17323-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="17323-157">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="17323-157">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="17323-158">W **Azure Active Directory** > **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="17323-158">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="17323-159">Podaj **nazwę** aplikacji (na przykład **Blazor klienta AAD**).</span><span class="sxs-lookup"><span data-stu-id="17323-159">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="17323-160">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="17323-160">Choose a **Supported account types**.</span></span> <span data-ttu-id="17323-161">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="17323-161">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="17323-162">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="17323-162">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="17323-163">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="17323-163">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="17323-164">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="17323-164">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="17323-165">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we *`Server`* właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="17323-165">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="17323-166">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="17323-166">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="17323-167">W sekcji [Tworzenie aplikacji](#create-the-app) zostanie wyświetlona informacja przypominająca IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="17323-167">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="17323-168">Wyczyść pole wyboru **uprawnienia** > **Udziel uprawnień administrator do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="17323-168">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="17323-169">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="17323-169">Select **Register**.</span></span>

<span data-ttu-id="17323-170">Zapisz *`Client`* Identyfikator aplikacji aplikacji (na przykład `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="17323-170">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="17323-171">W  obszarze > **konfiguracje platformy** uwierzytelniania w > **sieci Web**:</span><span class="sxs-lookup"><span data-stu-id="17323-171">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="17323-172">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="17323-172">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="17323-173">W przypadku **niejawnego przydzielenia** zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="17323-173">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="17323-174">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="17323-174">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="17323-175">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="17323-175">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="17323-176">W **uprawnienia interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="17323-176">In **API permissions**:</span></span>

1. <span data-ttu-id="17323-177">Upewnij się, że aplikacja ma **Microsoft Graph**  >  uprawnienie **użytkownika. odczyt** .</span><span class="sxs-lookup"><span data-stu-id="17323-177">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="17323-178">Wybierz pozycję **Dodaj uprawnienia** , a następnie **Moje interfejsy API**.</span><span class="sxs-lookup"><span data-stu-id="17323-178">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="17323-179">Wybierz *aplikację interfejsu API serwera* z kolumny **Nazwa** (na przykład **Blazor Server AAD**).</span><span class="sxs-lookup"><span data-stu-id="17323-179">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="17323-180">Otwórz listę **interfejsów API** .</span><span class="sxs-lookup"><span data-stu-id="17323-180">Open the **API** list.</span></span>
1. <span data-ttu-id="17323-181">Włącz dostęp do interfejsu API (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="17323-181">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="17323-182">Wybierz pozycję **Dodaj uprawnienia**.</span><span class="sxs-lookup"><span data-stu-id="17323-182">Select **Add permissions**.</span></span>
1. <span data-ttu-id="17323-183">Wybierz przycisk **Udziel zgody administratora na {nazwa dzierżawy}** .</span><span class="sxs-lookup"><span data-stu-id="17323-183">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="17323-184">Wybierz pozycję **Tak**, aby potwierdzić.</span><span class="sxs-lookup"><span data-stu-id="17323-184">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="17323-185">Tworzenie aplikacji</span><span class="sxs-lookup"><span data-stu-id="17323-185">Create the app</span></span>

<span data-ttu-id="17323-186">W pustym folderze Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="17323-186">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="17323-187">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="17323-187">Placeholder</span></span>                  | <span data-ttu-id="17323-188">Nazwa Azure Portal</span><span class="sxs-lookup"><span data-stu-id="17323-188">Azure portal name</span></span>                                     | <span data-ttu-id="17323-189">Przykład</span><span class="sxs-lookup"><span data-stu-id="17323-189">Example</span></span>                                        |
| ---------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="17323-190">Identyfikator aplikacji (klienta) dla *`Client`* aplikacji</span><span class="sxs-lookup"><span data-stu-id="17323-190">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="17323-191">Nazwa zakresu</span><span class="sxs-lookup"><span data-stu-id="17323-191">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="17323-192">Identyfikator aplikacji (klienta) dla *aplikacji interfejsu API serwera*</span><span class="sxs-lookup"><span data-stu-id="17323-192">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="17323-193">Identyfikator URI identyfikatora aplikacji&dagger;</span><span class="sxs-lookup"><span data-stu-id="17323-193">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{TENANT DOMAIN}`            | <span data-ttu-id="17323-194">Domena podstawowa/Wydawca/dzierżawa</span><span class="sxs-lookup"><span data-stu-id="17323-194">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |
| `{TENANT ID}`                | <span data-ttu-id="17323-195">Identyfikator katalogu (dzierżawcy)</span><span class="sxs-lookup"><span data-stu-id="17323-195">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`         |

<span data-ttu-id="17323-196">&dagger;Blazor WebAssemblySzablon automatycznie dodaje schemat `api://` do argumentu identyfikatora aplikacji, który został przesłany w `dotnet new` poleceniu.</span><span class="sxs-lookup"><span data-stu-id="17323-196">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="17323-197">W przypadku podania identyfikatora URI aplikacji dla `{SERVER API APP ID URI}` symbolu zastępczego i jeśli schemat jest `api://` , Usuń schemat ( `api://` ) z argumentu, jak przykładowa wartość w powyższej tabeli pokazuje.</span><span class="sxs-lookup"><span data-stu-id="17323-197">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="17323-198">Jeśli identyfikator URI aplikacji jest wartością niestandardową lub ma inny schemat (na przykład `https://` w przypadku niezaufanej domeny wydawcy podobnej do `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ), należy ręcznie zaktualizować domyślny identyfikator URI zakresu i usunąć `api://` schemat po *`Client`* utworzeniu aplikacji przez szablon.</span><span class="sxs-lookup"><span data-stu-id="17323-198">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="17323-199">Aby uzyskać więcej informacji, zobacz Uwaga w sekcji [zakresy tokenu dostępu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="17323-199">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="17323-200">Blazor WebAssemblySzablon można zmienić w przyszłych wydaniach ASP.NET Core, aby rozwiązać te scenariusze.</span><span class="sxs-lookup"><span data-stu-id="17323-200">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="17323-201">Aby uzyskać więcej informacji, zobacz [podwójny schemat identyfikatora URI aplikacji z Blazor szablonem WASM (hostowany, Single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="17323-201">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="17323-202">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="17323-202">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="17323-203">Zmiana konfiguracji może być wymagana w przypadku korzystania z dzierżawy platformy Azure z niezweryfikowaną domeną wydawcy, która została opisana w sekcji [Ustawienia aplikacji](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="17323-203">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="17323-204">Zmiana konfiguracji może być wymagana w przypadku korzystania z dzierżawy platformy Azure z niezweryfikowaną domeną wydawcy, która jest opisana w sekcji [zakresy tokenu dostępu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="17323-204">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="17323-205">W Azure Portal *`Client`* **Identyfikator URI przekierowania** konfiguracji platformy aplikacji jest skonfigurowany dla portu 5001 dla aplikacji, które działają na serwerze Kestrel z ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="17323-205">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="17323-206">Jeśli *`Client`* aplikacja jest uruchamiana na losowo IIS Express porcie, port aplikacji można znaleźć we właściwościach *aplikacji interfejsu API serwera* w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="17323-206">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="17323-207">Jeśli port nie został wcześniej skonfigurowany przy użyciu *`Client`* znanego portu aplikacji, Wróć do *`Client`* rejestracji aplikacji w Azure Portal i zaktualizuj identyfikator URI przekierowania z prawidłowym portem.</span><span class="sxs-lookup"><span data-stu-id="17323-207">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="17323-208">*`Server`* Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="17323-208">*`Server`* app configuration</span></span>

<span data-ttu-id="17323-209">*Ta sekcja dotyczy **`Server`** aplikacji rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="17323-209">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="17323-210">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="17323-210">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="17323-211">Obsługa uwierzytelniania i autoryzacji wywołań ASP.NET Core interfejsów API sieci Web za pomocą platformy firmy Microsoft Identity jest zapewniana przez następujące pakiety:</span><span class="sxs-lookup"><span data-stu-id="17323-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="17323-212">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem NuGet.org.</span><span class="sxs-lookup"><span data-stu-id="17323-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="17323-213">Pakiet zawiera wsparcie w zakresie uwierzytelniania i autoryzowania wywołań ASP.NET Core interfejsów API sieci Web [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) .</span><span class="sxs-lookup"><span data-stu-id="17323-213">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="17323-214">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="17323-214">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="17323-215">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="17323-215">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="17323-216">Metoda konfiguruje `AddAuthentication` usługi uwierzytelniania w ramach aplikacji i konfiguruje procedurę obsługi okaziciela JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="17323-216">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="17323-217"><xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A>Metoda konfiguruje usługi do ochrony internetowego interfejsu API za pomocą Identity platformy Microsoft Platform v 2.0.</span><span class="sxs-lookup"><span data-stu-id="17323-217">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="17323-218">Ta metoda oczekuje `AzureAd` sekcji w konfiguracji aplikacji z ustawieniami niezbędnymi do zainicjowania opcji uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="17323-218">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="17323-219">Metoda konfiguruje `AddAuthentication` usługi uwierzytelniania w ramach aplikacji i konfiguruje procedurę obsługi okaziciela JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="17323-219">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="17323-220"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>Metoda ustawia określone parametry w obsłudze okaziciela JWT wymagane do weryfikacji tokenów emitowanych przez Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="17323-220">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="17323-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> i <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> upewnij się, że:</span><span class="sxs-lookup"><span data-stu-id="17323-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="17323-222">Aplikacja próbuje analizować tokeny i sprawdzać ich poprawność w żądaniach przychodzących.</span><span class="sxs-lookup"><span data-stu-id="17323-222">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="17323-223">Wszystkie żądania próbujące uzyskać dostęp do chronionego zasobu bez poprawnego poświadczenia nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="17323-223">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="17323-224">Użytkownik. Identity . Nazwij</span><span class="sxs-lookup"><span data-stu-id="17323-224">User.Identity.Name</span></span>

<span data-ttu-id="17323-225">Domyślnie *`Server`* interfejs API aplikacji wypełnia `User.Identity.Name` wartość z `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` typu "typ" (na przykład `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="17323-225">By default, the *`Server`* app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="17323-226">Aby skonfigurować aplikację do odbierania wartości z `name` typu zgłoszenia:</span><span class="sxs-lookup"><span data-stu-id="17323-226">To configure the app to receive the value from the `name` claim type:</span></span>

* <span data-ttu-id="17323-227">Dodaj przestrzeń nazw dla <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> do `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="17323-227">Add a namespace for <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> to `Startup.cs`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="17323-228">Skonfiguruj <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="17323-228">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="17323-229">Skonfiguruj <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="17323-229">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a><span data-ttu-id="17323-230">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="17323-230">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="17323-231">`appsettings.json`Plik zawiera opcje konfigurowania procedury obsługi okaziciela JWT używanej do sprawdzania poprawności tokenów dostępu:</span><span class="sxs-lookup"><span data-stu-id="17323-231">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="17323-232">Przykład:</span><span class="sxs-lookup"><span data-stu-id="17323-232">Example:</span></span>

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

<span data-ttu-id="17323-233">`appsettings.json`Plik zawiera opcje konfigurowania procedury obsługi okaziciela JWT używanej do sprawdzania poprawności tokenów dostępu:</span><span class="sxs-lookup"><span data-stu-id="17323-233">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="17323-234">Przykład:</span><span class="sxs-lookup"><span data-stu-id="17323-234">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="17323-235">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="17323-235">WeatherForecast controller</span></span>

<span data-ttu-id="17323-236">Kontroler WeatherForecast (*controllers/WeatherForecastController. cs*) ujawnia chroniony interfejs API z [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybutem zastosowanym do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="17323-236">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="17323-237">**Ważne** jest, aby zrozumieć, że:</span><span class="sxs-lookup"><span data-stu-id="17323-237">It's **important** to understand that:</span></span>

* <span data-ttu-id="17323-238">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atrybut w tym kontrolerze interfejsu API jest jedynym warunkiem, że ten interfejs API jest chroniony przed nieautoryzowanym dostępem.</span><span class="sxs-lookup"><span data-stu-id="17323-238">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="17323-239">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atrybut używany w Blazor WebAssembly aplikacji służy tylko jako Wskazówka dla aplikacji, którą użytkownik powinien mieć autoryzację, aby aplikacja działała poprawnie.</span><span class="sxs-lookup"><span data-stu-id="17323-239">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="17323-240">*`Client`* Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="17323-240">*`Client`* app configuration</span></span>

<span data-ttu-id="17323-241">*Ta sekcja dotyczy **`Client`** aplikacji rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="17323-241">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="17323-242">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="17323-242">Authentication package</span></span>

<span data-ttu-id="17323-243">Gdy aplikacja zostanie utworzona w celu korzystania z kont służbowych ( `SingleOrg` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="17323-243">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="17323-244">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="17323-244">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="17323-245">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="17323-245">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="17323-246">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="17323-246">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="17323-247">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Pakiet zawiera przechodnie Dodawanie [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="17323-247">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="17323-248">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="17323-248">Authentication service support</span></span>

<span data-ttu-id="17323-249">Dodano obsługę <xref:System.Net.Http.HttpClient> wystąpień, które obejmują tokeny dostępu podczas wykonywania żądań do projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="17323-249">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="17323-250">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="17323-250">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="17323-251">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="17323-251">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="17323-252">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pakiet.</span><span class="sxs-lookup"><span data-stu-id="17323-252">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="17323-253">Ta metoda konfiguruje usługi wymagane przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="17323-253">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="17323-254">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="17323-254">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="17323-255"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="17323-255">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="17323-256">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD w witrynie Azure Portal podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="17323-256">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="17323-257">Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="17323-257">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="17323-258">Przykład:</span><span class="sxs-lookup"><span data-stu-id="17323-258">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="17323-259">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="17323-259">Access token scopes</span></span>

<span data-ttu-id="17323-260">Domyślne zakresy tokenów dostępu reprezentują listę zakresów tokenów dostępu, które są następujące:</span><span class="sxs-lookup"><span data-stu-id="17323-260">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="17323-261">Uwzględnione domyślnie w żądaniu logowania.</span><span class="sxs-lookup"><span data-stu-id="17323-261">Included by default in the sign in request.</span></span>
* <span data-ttu-id="17323-262">Służy do aprowizacji tokenu dostępu zaraz po uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="17323-262">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="17323-263">Wszystkie zakresy muszą należeć do tej samej aplikacji na Azure Active Directory reguł.</span><span class="sxs-lookup"><span data-stu-id="17323-263">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="17323-264">Dodatkowe zakresy można dodać do dodatkowych aplikacji interfejsu API w razie potrzeby:</span><span class="sxs-lookup"><span data-stu-id="17323-264">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="17323-265">Blazor WebAssemblySzablon automatycznie dodaje schemat `api://` do argumentu identyfikatora aplikacji, który został przesłany w `dotnet new` poleceniu.</span><span class="sxs-lookup"><span data-stu-id="17323-265">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="17323-266">Podczas generowania aplikacji z Blazor szablonu projektu upewnij się, że wartość domyślnego zakresu tokenu dostępu używa poprawnej wartości identyfikatora URI aplikacji niestandardowej podanej w Azure Portal lub wartości z **jednym** z następujących formatów:</span><span class="sxs-lookup"><span data-stu-id="17323-266">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="17323-267">Gdy domena wydawcy katalogu jest **zaufana**, domyślnym zakresem tokenu dostępu jest zazwyczaj wartość podobna do poniższego przykładu, gdzie `API.Access` jest domyślną nazwą zakresu:</span><span class="sxs-lookup"><span data-stu-id="17323-267">When the publisher domain of the directory is **trusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="17323-268">Sprawdź wartość dla podwójnego schematu ( `api://api://...` ).</span><span class="sxs-lookup"><span data-stu-id="17323-268">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="17323-269">Jeśli jest obecny schemat podwójny, usuń pierwszy `api://` schemat z wartości.</span><span class="sxs-lookup"><span data-stu-id="17323-269">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="17323-270">Gdy domena wydawcy katalogu nie jest **zaufana**, domyślnym zakresem tokenu dostępu jest zwykle wartość podobna do poniższego przykładu, gdzie `API.Access` jest domyślną nazwą zakresu:</span><span class="sxs-lookup"><span data-stu-id="17323-270">When the publisher domain of the directory is **untrusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="17323-271">Sprawdź wartość dodatkowego `api://` schematu ( `api://https://contoso.onmicrosoft.com/...` ).</span><span class="sxs-lookup"><span data-stu-id="17323-271">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="17323-272">Jeśli istnieje dodatkowy `api://` schemat, Usuń `api://` schemat z wartości.</span><span class="sxs-lookup"><span data-stu-id="17323-272">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="17323-273">Blazor WebAssemblySzablon można zmienić w przyszłych wydaniach ASP.NET Core, aby rozwiązać te scenariusze.</span><span class="sxs-lookup"><span data-stu-id="17323-273">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="17323-274">Aby uzyskać więcej informacji, zobacz [podwójny schemat identyfikatora URI aplikacji z Blazor szablonem WASM (hostowany, Single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="17323-274">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="17323-275">Określ dodatkowe zakresy z `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="17323-275">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="17323-276">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="17323-276">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="17323-277">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="17323-277">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="17323-278">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="17323-278">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="17323-279">Tryb logowania</span><span class="sxs-lookup"><span data-stu-id="17323-279">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="17323-280">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="17323-280">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="17323-281">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="17323-281">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="17323-282">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="17323-282">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="17323-283">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="17323-283">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="17323-284">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="17323-284">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="17323-285">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="17323-285">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="17323-286">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="17323-286">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="17323-287">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="17323-287">Run the app</span></span>

<span data-ttu-id="17323-288">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="17323-288">Run the app from the Server project.</span></span> <span data-ttu-id="17323-289">W przypadku korzystania z programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="17323-289">When using Visual Studio, either:</span></span>

* <span data-ttu-id="17323-290">Ustaw listę rozwijaną **projekty startowe** na pasku narzędzi do *aplikacji interfejsu API serwera* i wybierz przycisk **Uruchom** .</span><span class="sxs-lookup"><span data-stu-id="17323-290">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="17323-291">Wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .</span><span class="sxs-lookup"><span data-stu-id="17323-291">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="17323-292">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="17323-292">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="17323-293">Utwórz niestandardową wersję biblioteki MSAL JavaScript.</span><span class="sxs-lookup"><span data-stu-id="17323-293">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="17323-294">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="17323-294">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="17323-295">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="17323-295">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
* [<span data-ttu-id="17323-296">Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="17323-296">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app)
