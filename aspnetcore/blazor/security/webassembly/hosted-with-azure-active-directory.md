---
title: Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core przy użyciu Azure Active Directory
author: guardrex
description: Dowiedz się, jak zabezpieczyć Blazor WebAssembly aplikację hostowaną ASP.NET Core przy użyciu Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 12a2509998bb9b4d56e250518b2db91f73dd0e67
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712418"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="d6f74-103">Zabezpieczanie Blazor WebAssembly hostowanej aplikacji ASP.NET Core przy użyciu Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="d6f74-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="d6f74-104">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d6f74-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d6f74-105">W tym artykule opisano sposób tworzenia [hostowanej Blazor WebAssembly aplikacji](xref:blazor/hosting-models#blazor-webassembly) korzystającej z usługi [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="d6f74-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="d6f74-106">Rejestrowanie aplikacji w usłudze AAD i tworzenie rozwiązania</span><span class="sxs-lookup"><span data-stu-id="d6f74-106">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="d6f74-107">Tworzenie dzierżawy</span><span class="sxs-lookup"><span data-stu-id="d6f74-107">Create a tenant</span></span>

<span data-ttu-id="d6f74-108">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Konfigurowanie dzierżawy](/azure/active-directory/develop/quickstart-create-new-tenant) w celu utworzenia dzierżawy w usłudze AAD.</span><span class="sxs-lookup"><span data-stu-id="d6f74-108">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="d6f74-109">Rejestrowanie aplikacji interfejsu API serwera</span><span class="sxs-lookup"><span data-stu-id="d6f74-109">Register a server API app</span></span>

<span data-ttu-id="d6f74-110">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Zarejestruj aplikację przy użyciu platformy tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app) i kolejnych tematów usługi Azure AAD, aby zarejestrować aplikację w usłudze AAD dla *aplikacji interfejsu API serwera* , a następnie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="d6f74-110">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="d6f74-111">W **Azure Active Directory**  >  **rejestracje aplikacji**wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="d6f74-111">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="d6f74-112">Podaj **nazwę** aplikacji (na przykład \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="d6f74-112">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="d6f74-113">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="d6f74-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="d6f74-114">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="d6f74-114">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="d6f74-115">*Aplikacja interfejsu API serwera* nie wymaga **identyfikatora URI przekierowania** w tym scenariuszu, więc pozostaw listę rozwijaną w **sieci Web** i nie wprowadzaj identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="d6f74-115">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="d6f74-116">Wyłącz **Permissions**  >  pole wyboru**Przyznaj zgodę na uprawnienia administratora OpenID Connect i offline_access** .</span><span class="sxs-lookup"><span data-stu-id="d6f74-116">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="d6f74-117">Wybierz pozycję **Rejestruj**.</span><span class="sxs-lookup"><span data-stu-id="d6f74-117">Select **Register**.</span></span>

<span data-ttu-id="d6f74-118">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="d6f74-118">Record the following information:</span></span>

* <span data-ttu-id="d6f74-119">*Aplikacja interfejsu API serwera* Identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="d6f74-119">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="d6f74-120">Identyfikator katalogu (dzierżawy) (na przykład `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="d6f74-120">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="d6f74-121">Domena podstawowa/Wydawca/dzierżawa usługi AAD (na przykład `contoso.onmicrosoft.com` ): domena jest dostępna jako **domena wydawcy** w bloku **znakowania** Azure Portal dla zarejestrowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d6f74-121">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="d6f74-122">W obszarze **uprawnienia interfejsu API**usuń uprawnienie **Microsoft Graph**  >  **User. Read** , ponieważ aplikacja nie wymaga dostępu do profilu logowania lub użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d6f74-122">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="d6f74-123">W obszarze **Uwidacznianie interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="d6f74-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="d6f74-124">Wybierz polecenie **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="d6f74-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="d6f74-125">Wybierz przycisk **Zapisz i kontynuuj**.</span><span class="sxs-lookup"><span data-stu-id="d6f74-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="d6f74-126">Podaj **nazwę zakresu** (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="d6f74-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="d6f74-127">Podaj **nazwę wyświetlaną zgody administratora** (na przykład `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="d6f74-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="d6f74-128">Podaj **Opis zgody administratora** (na przykład `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="d6f74-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="d6f74-129">Upewnij się, że **stan** jest ustawiony na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="d6f74-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="d6f74-130">Wybierz pozycję **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="d6f74-130">Select **Add scope**.</span></span>

<span data-ttu-id="d6f74-131">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="d6f74-131">Record the following information:</span></span>

* <span data-ttu-id="d6f74-132">Identyfikator URI identyfikatora aplikacji (na przykład `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` , `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` lub podana wartość niestandardowa)</span><span class="sxs-lookup"><span data-stu-id="d6f74-132">App ID URI (for example, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="d6f74-133">Nazwa zakresu (na przykład `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="d6f74-133">Scope name (for example, `API.Access`)</span></span>

<span data-ttu-id="d6f74-134">Identyfikator URI aplikacji może wymagać specjalnej konfiguracji w aplikacji klienckiej, która jest opisana w sekcji [zakresy tokenu dostępu](#access-token-scopes) w dalszej części tego tematu.</span><span class="sxs-lookup"><span data-stu-id="d6f74-134">The App ID URI might require a special configuration in the client app, which is described in the [Access token scopes](#access-token-scopes) section later in this topic.</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="d6f74-135">Rejestrowanie aplikacji klienckiej</span><span class="sxs-lookup"><span data-stu-id="d6f74-135">Register a client app</span></span>

<span data-ttu-id="d6f74-136">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Zarejestruj aplikację przy użyciu platformy tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app) i kolejnych tematów usługi Azure AAD, aby zarejestrować aplikację w usłudze AAD dla *aplikacji klienckiej* , a następnie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="d6f74-136">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="d6f74-137">W **Azure Active Directory**  >  **rejestracje aplikacji**wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="d6f74-137">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="d6f74-138">Podaj **nazwę** aplikacji (na przykład \*\* Blazor klienta AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="d6f74-138">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="d6f74-139">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="d6f74-139">Choose a **Supported account types**.</span></span> <span data-ttu-id="d6f74-140">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="d6f74-140">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="d6f74-141">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="d6f74-141">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="d6f74-142">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="d6f74-142">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="d6f74-143">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d6f74-143">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="d6f74-144">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we właściwościach aplikacji serwera w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="d6f74-144">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="d6f74-145">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="d6f74-145">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="d6f74-146">W sekcji [Tworzenie aplikacji](#create-the-app) zostanie wyświetlona informacja przypominająca IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="d6f74-146">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="d6f74-147">Wyłącz **Permissions**  >  pole wyboru**Przyznaj zgodę na uprawnienia administratora OpenID Connect i offline_access** .</span><span class="sxs-lookup"><span data-stu-id="d6f74-147">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="d6f74-148">Wybierz pozycję **Rejestruj**.</span><span class="sxs-lookup"><span data-stu-id="d6f74-148">Select **Register**.</span></span>

<span data-ttu-id="d6f74-149">Zapisz identyfikator aplikacji *klienta* aplikacji (na przykład `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="d6f74-149">Record the *Client app* Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="d6f74-150">W **Authentication**obszarze  >  **konfiguracje platformy**uwierzytelniania w  >  **sieci Web**:</span><span class="sxs-lookup"><span data-stu-id="d6f74-150">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="d6f74-151">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="d6f74-151">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="d6f74-152">W przypadku **niejawnego przydzielenia**zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="d6f74-152">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="d6f74-153">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="d6f74-153">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="d6f74-154">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="d6f74-154">Select the **Save** button.</span></span>

<span data-ttu-id="d6f74-155">W **uprawnienia interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="d6f74-155">In **API permissions**:</span></span>

1. <span data-ttu-id="d6f74-156">Upewnij się, że aplikacja ma **Microsoft Graph**  >  uprawnienie**użytkownika. odczyt** .</span><span class="sxs-lookup"><span data-stu-id="d6f74-156">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="d6f74-157">Wybierz pozycję **Dodaj uprawnienia** , a następnie **Moje interfejsy API**.</span><span class="sxs-lookup"><span data-stu-id="d6f74-157">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="d6f74-158">Wybierz *aplikację interfejsu API serwera* z kolumny **Nazwa** (na przykład \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="d6f74-158">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="d6f74-159">Otwórz listę **interfejsów API** .</span><span class="sxs-lookup"><span data-stu-id="d6f74-159">Open the **API** list.</span></span>
1. <span data-ttu-id="d6f74-160">Włącz dostęp do interfejsu API (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="d6f74-160">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="d6f74-161">Wybierz pozycję **Dodaj uprawnienia**.</span><span class="sxs-lookup"><span data-stu-id="d6f74-161">Select **Add permissions**.</span></span>
1. <span data-ttu-id="d6f74-162">Wybierz przycisk **Udziel zgody administratora na {nazwa dzierżawy}** .</span><span class="sxs-lookup"><span data-stu-id="d6f74-162">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="d6f74-163">Wybierz pozycję **Tak**, aby potwierdzić.</span><span class="sxs-lookup"><span data-stu-id="d6f74-163">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="d6f74-164">Tworzenie aplikacji</span><span class="sxs-lookup"><span data-stu-id="d6f74-164">Create the app</span></span>

<span data-ttu-id="d6f74-165">W pustym folderze Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="d6f74-165">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="d6f74-166">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="d6f74-166">Placeholder</span></span>                  | <span data-ttu-id="d6f74-167">Nazwa Azure Portal</span><span class="sxs-lookup"><span data-stu-id="d6f74-167">Azure portal name</span></span>                                     | <span data-ttu-id="d6f74-168">Przykład</span><span class="sxs-lookup"><span data-stu-id="d6f74-168">Example</span></span>                                |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                         |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="d6f74-169">Identyfikator aplikacji (klienta) dla *aplikacji klienckiej*</span><span class="sxs-lookup"><span data-stu-id="d6f74-169">Application (client) ID for the *Client app*</span></span>          | `4369008b-21fa-427c-abaa-9b53bf58e538` |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="d6f74-170">Nazwa zakresu</span><span class="sxs-lookup"><span data-stu-id="d6f74-170">Scope name</span></span>                                            | `API.Access`                           |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="d6f74-171">Identyfikator aplikacji (klienta) dla *aplikacji interfejsu API serwera*</span><span class="sxs-lookup"><span data-stu-id="d6f74-171">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="d6f74-172">Identyfikator URI identyfikatora aplikacji ([patrz Uwaga](#access-token-scopes))</span><span class="sxs-lookup"><span data-stu-id="d6f74-172">Application ID URI ([see note](#access-token-scopes))</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="d6f74-173">Domena podstawowa/Wydawca/dzierżawa</span><span class="sxs-lookup"><span data-stu-id="d6f74-173">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`              |
| `{TENANT ID}`                | <span data-ttu-id="d6f74-174">Identyfikator katalogu (dzierżawcy)</span><span class="sxs-lookup"><span data-stu-id="d6f74-174">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="d6f74-175">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d6f74-175">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="d6f74-176">Przekaż identyfikator URI aplikacji do `app-id-uri` opcji, ale Uwaga w aplikacji klienckiej może być wymagana zmiana konfiguracji, która jest opisana w sekcji [zakresy tokenu dostępu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="d6f74-176">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="d6f74-177">W Azure Portal konfiguracja platformy **uwierzytelniania**w sieci Web dla *aplikacji klienta*  >  **Platform configurations**  >  **Web**  >  **Redirect URI** jest skonfigurowana dla portu 5001 dla aplikacji, które działają na serwerze Kestrel z ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="d6f74-177">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="d6f74-178">Jeśli *aplikacja kliencka* jest uruchamiana na losowo IIS Express porcie, port aplikacji można znaleźć we właściwościach *aplikacji interfejsu API serwera* w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="d6f74-178">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="d6f74-179">Jeśli port nie został wcześniej skonfigurowany przy użyciu znanego portu *aplikacji klienta* , Wróć do rejestracji *aplikacji klienckiej* w Azure Portal i zaktualizuj identyfikator URI przekierowania z prawidłowym portem.</span><span class="sxs-lookup"><span data-stu-id="d6f74-179">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="d6f74-180">Konfiguracja aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="d6f74-180">Server app configuration</span></span>

<span data-ttu-id="d6f74-181">*Ta sekcja dotyczy **`Server`** aplikacji rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="d6f74-181">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="d6f74-182">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="d6f74-182">Authentication package</span></span>

<span data-ttu-id="d6f74-183">Pakiet zawiera wsparcie w zakresie uwierzytelniania i autoryzowania wywołań ASP.NET Core interfejsów API sieci Web [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) .</span><span class="sxs-lookup"><span data-stu-id="d6f74-183">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="d6f74-184">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="d6f74-184">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="d6f74-185">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="d6f74-185">Authentication service support</span></span>

<span data-ttu-id="d6f74-186">Metoda konfiguruje `AddAuthentication` usługi uwierzytelniania w ramach aplikacji i konfiguruje procedurę obsługi okaziciela JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="d6f74-186">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="d6f74-187"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>Metoda ustawia określone parametry w obsłudze okaziciela JWT wymagane do weryfikacji tokenów emitowanych przez Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="d6f74-187">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="d6f74-188"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> i <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> upewnij się, że:</span><span class="sxs-lookup"><span data-stu-id="d6f74-188"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="d6f74-189">Aplikacja próbuje analizować tokeny i sprawdzać ich poprawność w żądaniach przychodzących.</span><span class="sxs-lookup"><span data-stu-id="d6f74-189">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="d6f74-190">Wszystkie żądania próbujące uzyskać dostęp do chronionego zasobu bez poprawnego poświadczenia nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="d6f74-190">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="d6f74-191">Użytkownik. Identity . Nazwij</span><span class="sxs-lookup"><span data-stu-id="d6f74-191">User.Identity.Name</span></span>

<span data-ttu-id="d6f74-192">Domyślnie interfejs API aplikacji serwera wypełnia `User.Identity.Name` wartość z `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` typu "typ" (na przykład `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="d6f74-192">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="d6f74-193">Aby skonfigurować aplikację do odbierania wartości z `name` typu, należy skonfigurować <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d6f74-193">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="d6f74-194">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="d6f74-194">App settings</span></span>

<span data-ttu-id="d6f74-195">`appsettings.json`Plik zawiera opcje konfigurowania procedury obsługi okaziciela JWT używanej do sprawdzania poprawności tokenów dostępu:</span><span class="sxs-lookup"><span data-stu-id="d6f74-195">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="d6f74-196">Przykład:</span><span class="sxs-lookup"><span data-stu-id="d6f74-196">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="d6f74-197">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="d6f74-197">WeatherForecast controller</span></span>

<span data-ttu-id="d6f74-198">Kontroler WeatherForecast (*controllers/WeatherForecastController. cs*) ujawnia chroniony interfejs API z [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybutem zastosowanym do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="d6f74-198">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="d6f74-199">**Ważne** jest, aby zrozumieć, że:</span><span class="sxs-lookup"><span data-stu-id="d6f74-199">It's **important** to understand that:</span></span>

* <span data-ttu-id="d6f74-200">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atrybut w tym kontrolerze interfejsu API jest jedynym warunkiem, że ten interfejs API jest chroniony przed nieautoryzowanym dostępem.</span><span class="sxs-lookup"><span data-stu-id="d6f74-200">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="d6f74-201">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Atrybut używany w Blazor WebAssembly aplikacji służy tylko jako Wskazówka dla aplikacji, którą użytkownik powinien mieć autoryzację, aby aplikacja działała poprawnie.</span><span class="sxs-lookup"><span data-stu-id="d6f74-201">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="d6f74-202">Konfiguracja aplikacji klienta</span><span class="sxs-lookup"><span data-stu-id="d6f74-202">Client app configuration</span></span>

<span data-ttu-id="d6f74-203">*Ta sekcja dotyczy **`Client`** aplikacji rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="d6f74-203">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="d6f74-204">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="d6f74-204">Authentication package</span></span>

<span data-ttu-id="d6f74-205">Gdy aplikacja zostanie utworzona w celu korzystania z kont służbowych ( `SingleOrg` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="d6f74-205">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="d6f74-206">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="d6f74-206">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="d6f74-207">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="d6f74-207">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="d6f74-208">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="d6f74-208">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="d6f74-209">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Pakiet zawiera przechodnie Dodawanie [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d6f74-209">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="d6f74-210">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="d6f74-210">Authentication service support</span></span>

<span data-ttu-id="d6f74-211">Dodano obsługę <xref:System.Net.Http.HttpClient> wystąpień, które obejmują tokeny dostępu podczas wykonywania żądań do projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="d6f74-211">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="d6f74-212">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="d6f74-212">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="d6f74-213">Symbol zastępczy `{APP ASSEMBLY}` jest nazwą zestawu aplikacji (na przykład `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="d6f74-213">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="d6f74-214">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pakiet.</span><span class="sxs-lookup"><span data-stu-id="d6f74-214">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="d6f74-215">Ta metoda konfiguruje usługi wymagane przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="d6f74-215">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="d6f74-216">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="d6f74-216">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="d6f74-217"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d6f74-217">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="d6f74-218">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD w witrynie Azure Portal podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d6f74-218">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="d6f74-219">Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="d6f74-219">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="d6f74-220">Przykład:</span><span class="sxs-lookup"><span data-stu-id="d6f74-220">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="d6f74-221">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="d6f74-221">Access token scopes</span></span>

<span data-ttu-id="d6f74-222">Domyślne zakresy tokenów dostępu reprezentują listę zakresów tokenów dostępu, które są następujące:</span><span class="sxs-lookup"><span data-stu-id="d6f74-222">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="d6f74-223">Uwzględnione domyślnie w żądaniu logowania.</span><span class="sxs-lookup"><span data-stu-id="d6f74-223">Included by default in the sign in request.</span></span>
* <span data-ttu-id="d6f74-224">Służy do aprowizacji tokenu dostępu zaraz po uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="d6f74-224">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="d6f74-225">Wszystkie zakresy muszą należeć do tej samej aplikacji na Azure Active Directory reguł.</span><span class="sxs-lookup"><span data-stu-id="d6f74-225">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="d6f74-226">Dodatkowe zakresy można dodać do dodatkowych aplikacji interfejsu API w razie potrzeby:</span><span class="sxs-lookup"><span data-stu-id="d6f74-226">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="d6f74-227">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="d6f74-227">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="d6f74-228">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="d6f74-228">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="d6f74-229">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="d6f74-229">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="d6f74-230">Tryb logowania</span><span class="sxs-lookup"><span data-stu-id="d6f74-230">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="d6f74-231">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="d6f74-231">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="d6f74-232">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="d6f74-232">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="d6f74-233">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="d6f74-233">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="d6f74-234">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="d6f74-234">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="d6f74-235">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="d6f74-235">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="d6f74-236">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="d6f74-236">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="d6f74-237">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="d6f74-237">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="d6f74-238">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="d6f74-238">Run the app</span></span>

<span data-ttu-id="d6f74-239">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="d6f74-239">Run the app from the Server project.</span></span> <span data-ttu-id="d6f74-240">W przypadku korzystania z programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d6f74-240">When using Visual Studio, either:</span></span>

* <span data-ttu-id="d6f74-241">Ustaw listę rozwijaną **projekty startowe** na pasku narzędzi do *aplikacji interfejsu API serwera* i wybierz przycisk **Uruchom** .</span><span class="sxs-lookup"><span data-stu-id="d6f74-241">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="d6f74-242">Wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .</span><span class="sxs-lookup"><span data-stu-id="d6f74-242">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="d6f74-243">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="d6f74-243">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="d6f74-244">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="d6f74-244">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="d6f74-245">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="d6f74-245">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
