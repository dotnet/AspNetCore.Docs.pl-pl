---
title: 'Zabezpieczanie :::no-loc(Blazor WebAssembly)::: hostowanej aplikacji ASP.NET Core przy użyciu Azure Active Directory'
author: guardrex
description: 'Dowiedz się, jak zabezpieczyć :::no-loc(Blazor WebAssembly)::: aplikację hostowaną ASP.NET Core przy użyciu Azure Active Directory.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: cb1deb71723660964954c2faae4512b7df9b2ed4
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690542"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="f8e0e-103">Zabezpieczanie :::no-loc(Blazor WebAssembly)::: hostowanej aplikacji ASP.NET Core przy użyciu Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="f8e0e-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: hosted app with Azure Active Directory</span></span>

<span data-ttu-id="f8e0e-104">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f8e0e-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f8e0e-105">W tym artykule opisano sposób tworzenia [hostowanej :::no-loc(Blazor WebAssembly)::: aplikacji](xref:blazor/hosting-models#blazor-webassembly) korzystającej z usługi [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-105">This article describes how to create a [hosted :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f8e0e-106">W przypadku :::no-loc(Blazor WebAssembly)::: aplikacji utworzonych w programie Visual Studio, które są skonfigurowane do obsługi kont w katalogu organizacji usługi AAD, program Visual Studio nie skonfiguruje aplikacji poprawnie na potrzeby generowania projektu.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-106">For :::no-loc(Blazor WebAssembly)::: apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't configure the app correctly on project generation.</span></span> <span data-ttu-id="f8e0e-107">Zostanie to rozkierowane w przyszłej wersji programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-107">This will be addressed in a future release of Visual Studio.</span></span> <span data-ttu-id="f8e0e-108">W tym artykule pokazano, jak utworzyć aplikację za pomocą polecenia interfejs wiersza polecenia platformy .NET Core `dotnet new` .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-108">This article shows how to create the app with the .NET Core CLI's `dotnet new` command.</span></span> <span data-ttu-id="f8e0e-109">Jeśli wolisz utworzyć aplikację za pomocą programu Visual Studio przed zaktualizowaniem IDE dla najnowszych :::no-loc(Blazor)::: szablonów w ASP.NET Core 5,0, zapoznaj się z każdą sekcją tego artykułu i Potwierdź lub zaktualizuj konfigurację aplikacji po utworzeniu aplikacji przez program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-109">If you prefer to create the app with Visual Studio before the IDE is updated for the latest :::no-loc(Blazor)::: templates in ASP.NET Core 5.0, refer to each section of this article and confirm or update the app's configuration after Visual Studio creates the app.</span></span>

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="f8e0e-110">Rejestrowanie aplikacji w usłudze AAD i tworzenie rozwiązania</span><span class="sxs-lookup"><span data-stu-id="f8e0e-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="f8e0e-111">Tworzenie dzierżawy</span><span class="sxs-lookup"><span data-stu-id="f8e0e-111">Create a tenant</span></span>

<span data-ttu-id="f8e0e-112">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Konfigurowanie dzierżawy](/azure/active-directory/develop/quickstart-create-new-tenant) w celu utworzenia dzierżawy w usłudze AAD.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="f8e0e-113">Rejestrowanie aplikacji interfejsu API serwera</span><span class="sxs-lookup"><span data-stu-id="f8e0e-113">Register a server API app</span></span>

<span data-ttu-id="f8e0e-114">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Zarejestruj aplikację przy użyciu platformy tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app) i kolejnych tematów usługi Azure AAD, aby zarejestrować aplikację w usłudze AAD dla *aplikacji interfejsu API serwera* , a następnie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="f8e0e-115">W **Azure Active Directory**  >  **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-115">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="f8e0e-116">Podaj **nazwę** aplikacji (na przykład **:::no-loc(Blazor Server)::: AAD** ).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-116">Provide a **Name** for the app (for example, **:::no-loc(Blazor Server)::: AAD** ).</span></span>
1. <span data-ttu-id="f8e0e-117">Wybierz **obsługiwane typy kont** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-117">Choose a **Supported account types** .</span></span> <span data-ttu-id="f8e0e-118">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="f8e0e-119">*Aplikacja interfejsu API serwera* nie wymaga **identyfikatora URI przekierowania** w tym scenariuszu, więc pozostaw listę rozwijaną w **sieci Web** i nie wprowadzaj identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="f8e0e-120">Wyczyść pole wyboru **uprawnienia**  >  **Udziel uprawnień administrator do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="f8e0e-121">Wybierz pozycję **Zarejestruj** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-121">Select **Register** .</span></span>

<span data-ttu-id="f8e0e-122">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-122">Record the following information:</span></span>

* <span data-ttu-id="f8e0e-123">*Aplikacja interfejsu API serwera* Identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="f8e0e-123">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="f8e0e-124">Identyfikator katalogu (dzierżawy) (na przykład `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="f8e0e-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="f8e0e-125">Domena podstawowa/Wydawca/dzierżawa usługi AAD (na przykład `contoso.onmicrosoft.com` ): domena jest dostępna jako **domena wydawcy** w bloku **znakowania** Azure Portal dla zarejestrowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-125">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="f8e0e-126">W obszarze **uprawnienia interfejsu API** usuń uprawnienie **Microsoft Graph**  >  **User. Read** , ponieważ aplikacja nie wymaga dostępu do profilu logowania lub użytkownika.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-126">In **API permissions** , remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="f8e0e-127">W obszarze **Uwidacznianie interfejsu API** :</span><span class="sxs-lookup"><span data-stu-id="f8e0e-127">In **Expose an API** :</span></span>

1. <span data-ttu-id="f8e0e-128">Wybierz polecenie **Dodaj zakres** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-128">Select **Add a scope** .</span></span>
1. <span data-ttu-id="f8e0e-129">Wybierz przycisk **Zapisz i kontynuuj** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-129">Select **Save and continue** .</span></span>
1. <span data-ttu-id="f8e0e-130">Podaj **nazwę zakresu** (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="f8e0e-131">Podaj **nazwę wyświetlaną zgody administratora** (na przykład `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="f8e0e-132">Podaj **Opis zgody administratora** (na przykład `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="f8e0e-133">Upewnij się, że **stan** jest ustawiony na **włączone** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-133">Confirm that the **State** is set to **Enabled** .</span></span>
1. <span data-ttu-id="f8e0e-134">Wybierz pozycję **Dodaj zakres** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-134">Select **Add scope** .</span></span>

<span data-ttu-id="f8e0e-135">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-135">Record the following information:</span></span>

* <span data-ttu-id="f8e0e-136">Identyfikator URI identyfikatora aplikacji (na przykład `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` , `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` lub wartość niestandardowa, którą podano)</span><span class="sxs-lookup"><span data-stu-id="f8e0e-136">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="f8e0e-137">Nazwa zakresu (na przykład `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="f8e0e-137">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="f8e0e-138">Rejestrowanie aplikacji klienckiej</span><span class="sxs-lookup"><span data-stu-id="f8e0e-138">Register a client app</span></span>

<span data-ttu-id="f8e0e-139">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Zarejestruj aplikację przy użyciu platformy tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app) i kolejnych tematów usługi Azure AAD, aby zarejestrować aplikację w usłudze AAD dla *`Client`* aplikacji, a następnie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="f8e0e-140">W **Azure Active Directory** > **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-140">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="f8e0e-141">Podaj **nazwę** aplikacji (na przykład **:::no-loc(Blazor)::: klienta AAD** ).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-141">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Client AAD** ).</span></span>
1. <span data-ttu-id="f8e0e-142">Wybierz **obsługiwane typy kont** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-142">Choose a **Supported account types** .</span></span> <span data-ttu-id="f8e0e-143">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="f8e0e-144">Ustaw listę rozwijaną **URI przekierowania** na **aplikację jednostronicową (Spa)** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-144">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="f8e0e-145">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="f8e0e-146">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="f8e0e-147">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we *`Server`* właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-147">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="f8e0e-148">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="f8e0e-149">W sekcji [Tworzenie aplikacji](#create-the-app) zostanie wyświetlona informacja przypominająca IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="f8e0e-150">Wyczyść pole wyboru **uprawnienia** > **Udziel uprawnień administrator do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-150">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="f8e0e-151">Wybierz pozycję **Zarejestruj** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-151">Select **Register** .</span></span>

<span data-ttu-id="f8e0e-152">Zapisz *`Client`* Identyfikator aplikacji aplikacji (na przykład `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-152">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="f8e0e-153">W **Authentication** obszarze > **Konfiguracja platformy** uwierzytelniania > **aplikacja jednostronicowa (Spa)** :</span><span class="sxs-lookup"><span data-stu-id="f8e0e-153">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="f8e0e-154">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="f8e0e-155">W przypadku **niejawnego udzielenia** upewnij się, że **nie** wybrano pól wyboru dla **tokenów dostępu** i **tokenów identyfikatorów** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-155">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="f8e0e-156">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="f8e0e-157">Wybierz ikonę **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-157">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="f8e0e-158">W **Azure Active Directory** > **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-158">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="f8e0e-159">Podaj **nazwę** aplikacji (na przykład **:::no-loc(Blazor)::: klienta AAD** ).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-159">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Client AAD** ).</span></span>
1. <span data-ttu-id="f8e0e-160">Wybierz **obsługiwane typy kont** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-160">Choose a **Supported account types** .</span></span> <span data-ttu-id="f8e0e-161">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-161">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="f8e0e-162">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-162">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="f8e0e-163">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-163">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="f8e0e-164">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-164">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="f8e0e-165">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we *`Server`* właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-165">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="f8e0e-166">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-166">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="f8e0e-167">W sekcji [Tworzenie aplikacji](#create-the-app) zostanie wyświetlona informacja przypominająca IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-167">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="f8e0e-168">Wyczyść pole wyboru **uprawnienia** > **Udziel uprawnień administrator do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-168">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="f8e0e-169">Wybierz pozycję **Zarejestruj** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-169">Select **Register** .</span></span>

<span data-ttu-id="f8e0e-170">Zapisz *`Client`* Identyfikator aplikacji aplikacji (na przykład `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-170">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="f8e0e-171">W **Authentication** obszarze > **konfiguracje platformy** uwierzytelniania w > **sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="f8e0e-171">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="f8e0e-172">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-172">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="f8e0e-173">W przypadku **niejawnego przydzielenia** zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-173">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens** .</span></span>
1. <span data-ttu-id="f8e0e-174">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-174">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="f8e0e-175">Wybierz ikonę **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-175">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="f8e0e-176">W **uprawnienia interfejsu API** :</span><span class="sxs-lookup"><span data-stu-id="f8e0e-176">In **API permissions** :</span></span>

1. <span data-ttu-id="f8e0e-177">Upewnij się, że aplikacja ma **Microsoft Graph**  >  uprawnienie **użytkownika. odczyt** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-177">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="f8e0e-178">Wybierz pozycję **Dodaj uprawnienia** , a następnie **Moje interfejsy API** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-178">Select **Add a permission** followed by **My APIs** .</span></span>
1. <span data-ttu-id="f8e0e-179">Wybierz *aplikację interfejsu API serwera* z kolumny **Nazwa** (na przykład **:::no-loc(Blazor Server)::: AAD** ).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-179">Select the *Server API app* from the **Name** column (for example, **:::no-loc(Blazor Server)::: AAD** ).</span></span>
1. <span data-ttu-id="f8e0e-180">Otwórz listę **interfejsów API** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-180">Open the **API** list.</span></span>
1. <span data-ttu-id="f8e0e-181">Włącz dostęp do interfejsu API (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-181">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="f8e0e-182">Wybierz pozycję **Dodaj uprawnienia** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-182">Select **Add permissions** .</span></span>
1. <span data-ttu-id="f8e0e-183">Wybierz przycisk **Udziel zgody administratora na {nazwa dzierżawy}** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-183">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="f8e0e-184">Wybierz pozycję **Tak** , aby potwierdzić.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-184">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="f8e0e-185">Tworzenie aplikacji</span><span class="sxs-lookup"><span data-stu-id="f8e0e-185">Create the app</span></span>

<span data-ttu-id="f8e0e-186">W pustym folderze Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-186">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="f8e0e-187">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="f8e0e-187">Placeholder</span></span>                  | <span data-ttu-id="f8e0e-188">Nazwa Azure Portal</span><span class="sxs-lookup"><span data-stu-id="f8e0e-188">Azure portal name</span></span>                                     | <span data-ttu-id="f8e0e-189">Przykład</span><span class="sxs-lookup"><span data-stu-id="f8e0e-189">Example</span></span>                                      |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `:::no-loc(Blazor):::Sample`                               |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="f8e0e-190">Identyfikator aplikacji (klienta) dla *`Client`* aplikacji</span><span class="sxs-lookup"><span data-stu-id="f8e0e-190">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`       |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="f8e0e-191">Nazwa zakresu</span><span class="sxs-lookup"><span data-stu-id="f8e0e-191">Scope name</span></span>                                            | `API.Access`                                 |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="f8e0e-192">Identyfikator aplikacji (klienta) dla *aplikacji interfejsu API serwera*</span><span class="sxs-lookup"><span data-stu-id="f8e0e-192">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`       |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="f8e0e-193">Identyfikator URI identyfikatora aplikacji</span><span class="sxs-lookup"><span data-stu-id="f8e0e-193">Application ID URI</span></span>                                    | `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="f8e0e-194">Domena podstawowa/Wydawca/dzierżawa</span><span class="sxs-lookup"><span data-stu-id="f8e0e-194">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                    |
| `{TENANT ID}`                | <span data-ttu-id="f8e0e-195">Identyfikator katalogu (dzierżawcy)</span><span class="sxs-lookup"><span data-stu-id="f8e0e-195">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`       |

<span data-ttu-id="f8e0e-196">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-196">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f8e0e-197">Zmiana konfiguracji może być wymagana w przypadku korzystania z dzierżawy platformy Azure z niezweryfikowaną domeną wydawcy, która została opisana w sekcji [Ustawienia aplikacji](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-197">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="f8e0e-198">Zmiana konfiguracji może być wymagana w przypadku korzystania z dzierżawy platformy Azure z niezweryfikowaną domeną wydawcy, która jest opisana w sekcji [zakresy tokenu dostępu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-198">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="f8e0e-199">W Azure Portal *`Client`* **Identyfikator URI przekierowania** konfiguracji platformy aplikacji jest skonfigurowany dla portu 5001 dla aplikacji, które działają na serwerze Kestrel z ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-199">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="f8e0e-200">Jeśli *`Client`* aplikacja jest uruchamiana na losowo IIS Express porcie, port aplikacji można znaleźć we właściwościach *aplikacji interfejsu API serwera* w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-200">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="f8e0e-201">Jeśli port nie został wcześniej skonfigurowany przy użyciu *`Client`* znanego portu aplikacji, Wróć do *`Client`* rejestracji aplikacji w Azure Portal i zaktualizuj identyfikator URI przekierowania z prawidłowym portem.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-201">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="f8e0e-202">*`Server`* Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="f8e0e-202">*`Server`* app configuration</span></span>

<span data-ttu-id="f8e0e-203">*Ta sekcja dotyczy **`Server`** aplikacji rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="f8e0e-203">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="f8e0e-204">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="f8e0e-204">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f8e0e-205">Obsługa uwierzytelniania i autoryzacji wywołań ASP.NET Core interfejsów API sieci Web za pomocą platformy firmy Microsoft :::no-loc(Identity)::: jest zapewniana przez następujące pakiety:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-205">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft :::no-loc(Identity)::: Platform is provided by the following packages:</span></span>

* [`Microsoft.:::no-loc(Identity):::.Web`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web)
* [`Microsoft.:::no-loc(Identity):::.Web.UI`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web.UI)

```xml
<PackageReference Include="Microsoft.:::no-loc(Identity):::.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.:::no-loc(Identity):::.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="f8e0e-206">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem NuGet.org.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-206">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f8e0e-207">Pakiet zawiera wsparcie w zakresie uwierzytelniania i autoryzowania wywołań ASP.NET Core interfejsów API sieci Web [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-207">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="f8e0e-208">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-208">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="f8e0e-209">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="f8e0e-209">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f8e0e-210">Metoda konfiguruje `AddAuthentication` usługi uwierzytelniania w ramach aplikacji i konfiguruje procedurę obsługi okaziciela JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-210">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="f8e0e-211"><xref:Microsoft.:::no-loc(Identity):::.Web.Microsoft:::no-loc(Identity):::WebApiAuthenticationBuilderExtensions.AddMicrosoft:::no-loc(Identity):::WebApi%2A>Metoda konfiguruje usługi do ochrony internetowego interfejsu API za pomocą :::no-loc(Identity)::: platformy Microsoft Platform v 2.0.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-211">The <xref:Microsoft.:::no-loc(Identity):::.Web.Microsoft:::no-loc(Identity):::WebApiAuthenticationBuilderExtensions.AddMicrosoft:::no-loc(Identity):::WebApi%2A> method configures services to protect the web API with Microsoft :::no-loc(Identity)::: Platform v2.0.</span></span> <span data-ttu-id="f8e0e-212">Ta metoda oczekuje `AzureAd` sekcji w konfiguracji aplikacji z ustawieniami niezbędnymi do zainicjowania opcji uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-212">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoft:::no-loc(Identity):::WebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f8e0e-213">Metoda konfiguruje `AddAuthentication` usługi uwierzytelniania w ramach aplikacji i konfiguruje procedurę obsługi okaziciela JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-213">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="f8e0e-214"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>Metoda ustawia określone parametry w obsłudze okaziciela JWT wymagane do weryfikacji tokenów emitowanych przez Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-214">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="f8e0e-215"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> i <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> upewnij się, że:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-215"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="f8e0e-216">Aplikacja próbuje analizować tokeny i sprawdzać ich poprawność w żądaniach przychodzących.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-216">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="f8e0e-217">Wszystkie żądania próbujące uzyskać dostęp do chronionego zasobu bez poprawnego poświadczenia nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-217">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="f8e0e-218">Użytkownik. :::no-loc(Identity)::: . Nazwij</span><span class="sxs-lookup"><span data-stu-id="f8e0e-218">User.:::no-loc(Identity):::.Name</span></span>

<span data-ttu-id="f8e0e-219">Domyślnie *`Server`* interfejs API aplikacji wypełnia `User.:::no-loc(Identity):::.Name` wartość z `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` typu "typ" (na przykład `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-219">By default, the *`Server`* app API populates `User.:::no-loc(Identity):::.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="f8e0e-220">Aby skonfigurować aplikację do odbierania wartości z `name` typu, należy skonfigurować <xref:Microsoft.:::no-loc(Identity):::Model.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f8e0e-220">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.:::no-loc(Identity):::Model.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="f8e0e-221">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="f8e0e-221">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f8e0e-222">`appsettings.json`Plik zawiera opcje konfigurowania procedury obsługi okaziciela JWT używanej do sprawdzania poprawności tokenów dostępu:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-222">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="f8e0e-223">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-223">Example:</span></span>

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

[!INCLUDE[](~/includes/blazor-security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f8e0e-224">`appsettings.json`Plik zawiera opcje konfigurowania procedury obsługi okaziciela JWT używanej do sprawdzania poprawności tokenów dostępu:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-224">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="f8e0e-225">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-225">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="f8e0e-226">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="f8e0e-226">WeatherForecast controller</span></span>

<span data-ttu-id="f8e0e-227">Kontroler WeatherForecast ( *controllers/WeatherForecastController. cs* ) ujawnia chroniony interfejs API z [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybutem zastosowanym do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-227">The WeatherForecast controller ( *Controllers/WeatherForecastController.cs* ) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="f8e0e-228">**Ważne** jest, aby zrozumieć, że:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-228">It's **important** to understand that:</span></span>

* <span data-ttu-id="f8e0e-229">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atrybut w tym kontrolerze interfejsu API jest jedynym warunkiem, że ten interfejs API jest chroniony przed nieautoryzowanym dostępem.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-229">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="f8e0e-230">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atrybut używany w :::no-loc(Blazor WebAssembly)::: aplikacji służy tylko jako Wskazówka dla aplikacji, którą użytkownik powinien mieć autoryzację, aby aplikacja działała poprawnie.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-230">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the :::no-loc(Blazor WebAssembly)::: app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="f8e0e-231">*`Client`* Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="f8e0e-231">*`Client`* app configuration</span></span>

<span data-ttu-id="f8e0e-232">*Ta sekcja dotyczy **`Client`** aplikacji rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="f8e0e-232">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="f8e0e-233">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="f8e0e-233">Authentication package</span></span>

<span data-ttu-id="f8e0e-234">Gdy aplikacja zostanie utworzona w celu korzystania z kont służbowych ( `SingleOrg` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-234">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="f8e0e-235">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-235">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="f8e0e-236">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-236">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="f8e0e-237">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-237">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="f8e0e-238">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Pakiet zawiera przechodnie Dodawanie [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-238">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="f8e0e-239">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="f8e0e-239">Authentication service support</span></span>

<span data-ttu-id="f8e0e-240">Dodano obsługę <xref:System.Net.Http.HttpClient> wystąpień, które obejmują tokeny dostępu podczas wykonywania żądań do projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-240">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="f8e0e-241">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-241">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="f8e0e-242">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `:::no-loc(Blazor):::Sample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-242">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample.ServerAPI`).</span></span>

<span data-ttu-id="f8e0e-243">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pakiet.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-243">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="f8e0e-244">Ta metoda konfiguruje usługi wymagane przez aplikację do współpracy z :::no-loc(Identity)::: dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="f8e0e-244">This method sets up the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="f8e0e-245">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-245">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="f8e0e-246"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-246">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="f8e0e-247">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD w witrynie Azure Portal podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-247">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="f8e0e-248">Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="f8e0e-248">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="f8e0e-249">Przykład:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-249">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="f8e0e-250">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="f8e0e-250">Access token scopes</span></span>

<span data-ttu-id="f8e0e-251">Domyślne zakresy tokenów dostępu reprezentują listę zakresów tokenów dostępu, które są następujące:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-251">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="f8e0e-252">Uwzględnione domyślnie w żądaniu logowania.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-252">Included by default in the sign in request.</span></span>
* <span data-ttu-id="f8e0e-253">Służy do aprowizacji tokenu dostępu zaraz po uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-253">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="f8e0e-254">Wszystkie zakresy muszą należeć do tej samej aplikacji na Azure Active Directory reguł.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-254">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="f8e0e-255">Dodatkowe zakresy można dodać do dodatkowych aplikacji interfejsu API w razie potrzeby:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-255">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="f8e0e-256">Określ dodatkowe zakresy z `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="f8e0e-256">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="f8e0e-257">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="f8e0e-257">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="f8e0e-258">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="f8e0e-258">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="f8e0e-259">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="f8e0e-259">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="f8e0e-260">Tryb logowania</span><span class="sxs-lookup"><span data-stu-id="f8e0e-260">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="f8e0e-261">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="f8e0e-261">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="f8e0e-262">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="f8e0e-262">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="f8e0e-263">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="f8e0e-263">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="f8e0e-264">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="f8e0e-264">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="f8e0e-265">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="f8e0e-265">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="f8e0e-266">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="f8e0e-266">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="f8e0e-267">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="f8e0e-267">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="f8e0e-268">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="f8e0e-268">Run the app</span></span>

<span data-ttu-id="f8e0e-269">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="f8e0e-269">Run the app from the Server project.</span></span> <span data-ttu-id="f8e0e-270">W przypadku korzystania z programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="f8e0e-270">When using Visual Studio, either:</span></span>

* <span data-ttu-id="f8e0e-271">Ustaw listę rozwijaną **projekty startowe** na pasku narzędzi do *aplikacji interfejsu API serwera* i wybierz przycisk **Uruchom** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-271">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="f8e0e-272">Wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .</span><span class="sxs-lookup"><span data-stu-id="f8e0e-272">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="f8e0e-273">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="f8e0e-273">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="f8e0e-274">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="f8e0e-274">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="f8e0e-275">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="f8e0e-275">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
