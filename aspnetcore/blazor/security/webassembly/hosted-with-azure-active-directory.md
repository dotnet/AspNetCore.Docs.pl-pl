---
title: Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core przy użyciu Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 82916c06413300bbefa85c619239c23a8e40468a
ms.sourcegitcommit: f7873c02c1505c99106cbc708f37e18fc0a496d1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147758"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="a8aba-102">Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core przy użyciu Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="a8aba-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="a8aba-103">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a8aba-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a8aba-104">W tym artykule opisano sposób tworzenia [ Blazor WebAssembly hostowanej aplikacji](xref:blazor/hosting-models#blazor-webassembly) korzystającej z usługi [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="a8aba-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="a8aba-105">Rejestrowanie aplikacji w usłudze AAD i tworzenie rozwiązania</span><span class="sxs-lookup"><span data-stu-id="a8aba-105">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="a8aba-106">Tworzenie dzierżawy</span><span class="sxs-lookup"><span data-stu-id="a8aba-106">Create a tenant</span></span>

<span data-ttu-id="a8aba-107">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Konfigurowanie dzierżawy](/azure/active-directory/develop/quickstart-create-new-tenant) w celu utworzenia dzierżawy w usłudze AAD.</span><span class="sxs-lookup"><span data-stu-id="a8aba-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="a8aba-108">Rejestrowanie aplikacji interfejsu API serwera</span><span class="sxs-lookup"><span data-stu-id="a8aba-108">Register a server API app</span></span>

<span data-ttu-id="a8aba-109">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Zarejestruj aplikację przy użyciu platformy tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app) i kolejnych tematów usługi Azure AAD, aby zarejestrować aplikację w usłudze AAD dla *aplikacji interfejsu API serwera* , a następnie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="a8aba-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="a8aba-110">W **Azure Active Directory**  >  **rejestracje aplikacji**wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="a8aba-110">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="a8aba-111">Podaj **nazwę** aplikacji (na przykład \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="a8aba-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="a8aba-112">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="a8aba-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="a8aba-113">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="a8aba-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a8aba-114">*Aplikacja interfejsu API serwera* nie wymaga **identyfikatora URI przekierowania** w tym scenariuszu, więc pozostaw listę rozwijaną w **sieci Web** i nie wprowadzaj identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="a8aba-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="a8aba-115">Wyłącz **Permissions**  >  pole wyboru**Przyznaj zgodę na uprawnienia administratora OpenID Connect i offline_access** .</span><span class="sxs-lookup"><span data-stu-id="a8aba-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a8aba-116">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="a8aba-116">Select **Register**.</span></span>

<span data-ttu-id="a8aba-117">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="a8aba-117">Record the following information:</span></span>

* <span data-ttu-id="a8aba-118">*Aplikacja interfejsu API serwera* Identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="a8aba-118">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="a8aba-119">Identyfikator katalogu (dzierżawy) (na przykład `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="a8aba-119">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="a8aba-120">Domena podstawowa/Wydawca/dzierżawa usługi AAD (na przykład `contoso.onmicrosoft.com` ): domena jest dostępna jako **domena wydawcy** w bloku **znakowania** Azure Portal dla zarejestrowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a8aba-120">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="a8aba-121">W obszarze **uprawnienia interfejsu API**usuń uprawnienie **Microsoft Graph**  >  **User. Read** , ponieważ aplikacja nie wymaga dostępu do profilu logowania lub użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a8aba-121">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="a8aba-122">W obszarze **Uwidacznianie interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="a8aba-122">In **Expose an API**:</span></span>

1. <span data-ttu-id="a8aba-123">Wybierz polecenie **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="a8aba-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="a8aba-124">Wybierz przycisk **Zapisz i kontynuuj**.</span><span class="sxs-lookup"><span data-stu-id="a8aba-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="a8aba-125">Podaj **nazwę zakresu** (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="a8aba-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a8aba-126">Podaj **nazwę wyświetlaną zgody administratora** (na przykład `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="a8aba-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="a8aba-127">Podaj **Opis zgody administratora** (na przykład `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="a8aba-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="a8aba-128">Upewnij się, że **stan** jest ustawiony na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="a8aba-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="a8aba-129">Wybierz pozycję **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="a8aba-129">Select **Add scope**.</span></span>

<span data-ttu-id="a8aba-130">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="a8aba-130">Record the following information:</span></span>

* <span data-ttu-id="a8aba-131">Identyfikator URI identyfikatora aplikacji (na przykład `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` , `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` lub podana wartość niestandardowa)</span><span class="sxs-lookup"><span data-stu-id="a8aba-131">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="a8aba-132">Zakres domyślny (na przykład `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="a8aba-132">Default scope (for example, `API.Access`)</span></span>

<span data-ttu-id="a8aba-133">Identyfikator URI aplikacji może wymagać specjalnej konfiguracji w aplikacji klienckiej, która jest opisana w sekcji [zakresy tokenu dostępu](#access-token-scopes) w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="a8aba-133">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="a8aba-134">Rejestrowanie aplikacji klienckiej</span><span class="sxs-lookup"><span data-stu-id="a8aba-134">Register a client app</span></span>

<span data-ttu-id="a8aba-135">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Zarejestruj aplikację przy użyciu platformy tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app) i kolejnych tematów usługi Azure AAD, aby zarejestrować aplikację w usłudze AAD dla *aplikacji klienckiej* , a następnie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="a8aba-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="a8aba-136">W **Azure Active Directory**  >  **rejestracje aplikacji**wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="a8aba-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="a8aba-137">Podaj **nazwę** aplikacji (na przykład \*\* Blazor klienta AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="a8aba-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="a8aba-138">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="a8aba-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="a8aba-139">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="a8aba-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a8aba-140">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="a8aba-140">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a8aba-141">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="a8aba-141">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a8aba-142">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a8aba-142">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a8aba-143">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we właściwościach aplikacji serwera w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="a8aba-143">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a8aba-144">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="a8aba-144">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a8aba-145">W sekcji [Tworzenie aplikacji](#create-the-app) zostanie wyświetlona informacja przypominająca IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="a8aba-145">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a8aba-146">Wyłącz **Permissions**  >  pole wyboru**Przyznaj zgodę na uprawnienia administratora OpenID Connect i offline_access** .</span><span class="sxs-lookup"><span data-stu-id="a8aba-146">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a8aba-147">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="a8aba-147">Select **Register**.</span></span>

<span data-ttu-id="a8aba-148">Zapisz identyfikator aplikacji *klienta* aplikacji (na przykład `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="a8aba-148">Record the *Client app* Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="a8aba-149">W **Authentication**obszarze  >  **konfiguracje platformy**uwierzytelniania w  >  **sieci Web**:</span><span class="sxs-lookup"><span data-stu-id="a8aba-149">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="a8aba-150">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="a8aba-150">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a8aba-151">W przypadku **niejawnego przydzielenia**zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="a8aba-151">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a8aba-152">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="a8aba-152">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a8aba-153">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="a8aba-153">Select the **Save** button.</span></span>

<span data-ttu-id="a8aba-154">W **uprawnienia interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="a8aba-154">In **API permissions**:</span></span>

1. <span data-ttu-id="a8aba-155">Upewnij się, że aplikacja ma **Microsoft Graph**  >  uprawnienie**użytkownika. odczyt** .</span><span class="sxs-lookup"><span data-stu-id="a8aba-155">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="a8aba-156">Wybierz pozycję **Dodaj uprawnienia** , a następnie **Moje interfejsy API**.</span><span class="sxs-lookup"><span data-stu-id="a8aba-156">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="a8aba-157">Wybierz *aplikację interfejsu API serwera* z kolumny **Nazwa** (na przykład \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="a8aba-157">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="a8aba-158">Otwórz listę **interfejsów API** .</span><span class="sxs-lookup"><span data-stu-id="a8aba-158">Open the **API** list.</span></span>
1. <span data-ttu-id="a8aba-159">Włącz dostęp do interfejsu API (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="a8aba-159">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a8aba-160">Wybierz pozycję **Dodaj uprawnienia**.</span><span class="sxs-lookup"><span data-stu-id="a8aba-160">Select **Add permissions**.</span></span>
1. <span data-ttu-id="a8aba-161">Wybierz przycisk **Udziel zgody administratora na {nazwa dzierżawy}** .</span><span class="sxs-lookup"><span data-stu-id="a8aba-161">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="a8aba-162">Kliknij przycisk **Tak**, aby potwierdzić.</span><span class="sxs-lookup"><span data-stu-id="a8aba-162">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="a8aba-163">Tworzymy aplikację.</span><span class="sxs-lookup"><span data-stu-id="a8aba-163">Create the app</span></span>

<span data-ttu-id="a8aba-164">W pustym folderze Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="a8aba-164">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="a8aba-165">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="a8aba-165">Placeholder</span></span>                  | <span data-ttu-id="a8aba-166">Nazwa Azure Portal</span><span class="sxs-lookup"><span data-stu-id="a8aba-166">Azure portal name</span></span>                                     | <span data-ttu-id="a8aba-167">Przykład</span><span class="sxs-lookup"><span data-stu-id="a8aba-167">Example</span></span>                                |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="a8aba-168">Identyfikator aplikacji (klienta) dla *aplikacji klienckiej*</span><span class="sxs-lookup"><span data-stu-id="a8aba-168">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="a8aba-169">Nazwa zakresu</span><span class="sxs-lookup"><span data-stu-id="a8aba-169">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="a8aba-170">Identyfikator aplikacji (klienta) dla *aplikacji interfejsu API serwera*</span><span class="sxs-lookup"><span data-stu-id="a8aba-170">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="a8aba-171">Identyfikator URI identyfikatora aplikacji ([patrz Uwaga](#access-token-scopes))</span><span class="sxs-lookup"><span data-stu-id="a8aba-171">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="a8aba-172">Domena podstawowa/Wydawca/dzierżawa</span><span class="sxs-lookup"><span data-stu-id="a8aba-172">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |
| `{TENANT ID}`                | <span data-ttu-id="a8aba-173">Identyfikator katalogu (dzierżawcy)</span><span class="sxs-lookup"><span data-stu-id="a8aba-173">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="a8aba-174">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a8aba-174">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="a8aba-175">Przekaż identyfikator URI aplikacji do `app-id-uri` opcji, ale Uwaga w aplikacji klienckiej może być wymagana zmiana konfiguracji, która jest opisana w sekcji [zakresy tokenu dostępu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="a8aba-175">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="a8aba-176">W Azure Portal konfiguracja platformy **uwierzytelniania**w sieci Web dla *aplikacji klienta*  >  **Platform configurations**  >  **Web**  >  **Redirect URI** jest skonfigurowana dla portu 5001 dla aplikacji, które działają na serwerze Kestrel z ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="a8aba-176">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="a8aba-177">Jeśli *aplikacja kliencka* jest uruchamiana na losowo IIS Express porcie, port aplikacji można znaleźć we właściwościach *aplikacji interfejsu API serwera* w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="a8aba-177">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="a8aba-178">Jeśli port nie został wcześniej skonfigurowany przy użyciu znanego portu *aplikacji klienta* , Wróć do rejestracji *aplikacji klienckiej* w Azure Portal i zaktualizuj identyfikator URI przekierowania z prawidłowym portem.</span><span class="sxs-lookup"><span data-stu-id="a8aba-178">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="a8aba-179">Konfiguracja aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="a8aba-179">Server app configuration</span></span>

<span data-ttu-id="a8aba-180">*Ta sekcja dotyczy **`Server`** aplikacji rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="a8aba-180">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a8aba-181">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="a8aba-181">Authentication package</span></span>

<span data-ttu-id="a8aba-182">Pakiet zawiera wsparcie w zakresie uwierzytelniania i autoryzowania wywołań ASP.NET Core interfejsów API sieci Web [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) .</span><span class="sxs-lookup"><span data-stu-id="a8aba-182">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="a8aba-183">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="a8aba-183">Authentication service support</span></span>

<span data-ttu-id="a8aba-184">Metoda konfiguruje `AddAuthentication` usługi uwierzytelniania w ramach aplikacji i konfiguruje procedurę obsługi okaziciela JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="a8aba-184">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="a8aba-185"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>Metoda ustawia określone parametry w obsłudze okaziciela JWT wymagane do weryfikacji tokenów emitowanych przez Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="a8aba-185">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="a8aba-186"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>i <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> upewnij się, że:</span><span class="sxs-lookup"><span data-stu-id="a8aba-186"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="a8aba-187">Aplikacja próbuje analizować tokeny i sprawdzać ich poprawność w żądaniach przychodzących.</span><span class="sxs-lookup"><span data-stu-id="a8aba-187">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="a8aba-188">Wszystkie żądania próbujące uzyskać dostęp do chronionego zasobu bez poprawnego poświadczenia nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="a8aba-188">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="a8aba-189">Użytkownik. Identity . Nazwij</span><span class="sxs-lookup"><span data-stu-id="a8aba-189">User.Identity.Name</span></span>

<span data-ttu-id="a8aba-190">Domyślnie interfejs API aplikacji serwera wypełnia `User.Identity.Name` wartość z `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` typu "typ" (na przykład `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="a8aba-190">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="a8aba-191">Aby skonfigurować aplikację do odbierania wartości z `name` typu, należy skonfigurować <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a8aba-191">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="a8aba-192">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="a8aba-192">App settings</span></span>

<span data-ttu-id="a8aba-193">`appsettings.json`Plik zawiera opcje konfigurowania procedury obsługi okaziciela JWT używanej do sprawdzania poprawności tokenów dostępu:</span><span class="sxs-lookup"><span data-stu-id="a8aba-193">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="a8aba-194">Przykład:</span><span class="sxs-lookup"><span data-stu-id="a8aba-194">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="a8aba-195">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="a8aba-195">WeatherForecast controller</span></span>

<span data-ttu-id="a8aba-196">Kontroler WeatherForecast (*controllers/WeatherForecastController. cs*) ujawnia chroniony interfejs API z [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybutem zastosowanym do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="a8aba-196">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="a8aba-197">**Ważne** jest, aby zrozumieć, że:</span><span class="sxs-lookup"><span data-stu-id="a8aba-197">It's **important** to understand that:</span></span>

* <span data-ttu-id="a8aba-198">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atrybut w tym kontrolerze interfejsu API jest jedynym warunkiem, że ten interfejs API jest chroniony przed nieautoryzowanym dostępem.</span><span class="sxs-lookup"><span data-stu-id="a8aba-198">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="a8aba-199">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atrybut używany w Blazor WebAssembly aplikacji służy tylko jako Wskazówka dla aplikacji, którą użytkownik powinien mieć autoryzację, aby aplikacja działała poprawnie.</span><span class="sxs-lookup"><span data-stu-id="a8aba-199">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="a8aba-200">Konfiguracja aplikacji klienta</span><span class="sxs-lookup"><span data-stu-id="a8aba-200">Client app configuration</span></span>

<span data-ttu-id="a8aba-201">*Ta sekcja dotyczy **`Client`** aplikacji rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="a8aba-201">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a8aba-202">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="a8aba-202">Authentication package</span></span>

<span data-ttu-id="a8aba-203">Gdy aplikacja zostanie utworzona w celu korzystania z kont służbowych ( `SingleOrg` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) ).</span><span class="sxs-lookup"><span data-stu-id="a8aba-203">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="a8aba-204">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="a8aba-204">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a8aba-205">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a8aba-205">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="a8aba-206">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)Pakiet zawiera przechodnie Dodawanie [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a8aba-206">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="a8aba-207">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="a8aba-207">Authentication service support</span></span>

<span data-ttu-id="a8aba-208">Dodano obsługę <xref:System.Net.Http.HttpClient> wystąpień, które obejmują tokeny dostępu podczas wykonywania żądań do projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="a8aba-208">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="a8aba-209">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a8aba-209">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="a8aba-210">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="a8aba-210">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="a8aba-211">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) pakiet.</span><span class="sxs-lookup"><span data-stu-id="a8aba-211">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="a8aba-212">Ta metoda konfiguruje usługi wymagane przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="a8aba-212">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a8aba-213">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a8aba-213">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="a8aba-214"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a8aba-214">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a8aba-215">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD w witrynie Azure Portal podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a8aba-215">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="a8aba-216">Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="a8aba-216">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="a8aba-217">Przykład:</span><span class="sxs-lookup"><span data-stu-id="a8aba-217">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="a8aba-218">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="a8aba-218">Access token scopes</span></span>

<span data-ttu-id="a8aba-219">Domyślne zakresy tokenów dostępu reprezentują listę zakresów tokenów dostępu, które są następujące:</span><span class="sxs-lookup"><span data-stu-id="a8aba-219">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="a8aba-220">Uwzględnione domyślnie w żądaniu logowania.</span><span class="sxs-lookup"><span data-stu-id="a8aba-220">Included by default in the sign in request.</span></span>
* <span data-ttu-id="a8aba-221">Służy do aprowizacji tokenu dostępu zaraz po uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="a8aba-221">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="a8aba-222">Wszystkie zakresy muszą należeć do tej samej aplikacji na Azure Active Directory reguł.</span><span class="sxs-lookup"><span data-stu-id="a8aba-222">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="a8aba-223">Dodatkowe zakresy można dodać do dodatkowych aplikacji interfejsu API w razie potrzeby:</span><span class="sxs-lookup"><span data-stu-id="a8aba-223">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="a8aba-224">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="a8aba-224">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a8aba-225">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="a8aba-225">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a8aba-226">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="a8aba-226">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="a8aba-227">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="a8aba-227">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="a8aba-228">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="a8aba-228">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="a8aba-229">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="a8aba-229">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="a8aba-230">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="a8aba-230">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="a8aba-231">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="a8aba-231">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="a8aba-232">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="a8aba-232">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="a8aba-233">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="a8aba-233">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="a8aba-234">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="a8aba-234">Run the app</span></span>

<span data-ttu-id="a8aba-235">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="a8aba-235">Run the app from the Server project.</span></span> <span data-ttu-id="a8aba-236">W przypadku korzystania z programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a8aba-236">When using Visual Studio, either:</span></span>

* <span data-ttu-id="a8aba-237">Ustaw listę rozwijaną **projekty startowe** na pasku narzędzi do *aplikacji interfejsu API serwera* i wybierz przycisk **Uruchom** .</span><span class="sxs-lookup"><span data-stu-id="a8aba-237">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="a8aba-238">Wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .</span><span class="sxs-lookup"><span data-stu-id="a8aba-238">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a8aba-239">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a8aba-239">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="a8aba-240">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="a8aba-240">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="a8aba-241">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="a8aba-241">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
