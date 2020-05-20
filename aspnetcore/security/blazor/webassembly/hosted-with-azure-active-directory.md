---
<span data-ttu-id="08dc9-101">title: "Zabezpiecz Blazor aplikację hostowaną ASP.NET Core webassembly z Azure Active Directory" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="08dc9-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="08dc9-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="08dc9-102">'Blazor'</span></span>
- <span data-ttu-id="08dc9-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="08dc9-103">'Identity'</span></span>
- <span data-ttu-id="08dc9-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="08dc9-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="08dc9-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="08dc9-105">'Razor'</span></span>
- <span data-ttu-id="08dc9-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="08dc9-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="08dc9-107">Zabezpiecz Blazor aplikację hostowaną ASP.NET Core webassembly przy użyciu Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="08dc9-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="08dc9-108">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="08dc9-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="08dc9-109">W tym artykule opisano sposób tworzenia [ Blazor aplikacji hostowanej w programie webassembly](xref:blazor/hosting-models#blazor-webassembly) , która używa usługi [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="08dc9-109">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="08dc9-110">Rejestrowanie aplikacji w usłudze AAD i tworzenie rozwiązania</span><span class="sxs-lookup"><span data-stu-id="08dc9-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="08dc9-111">Tworzenie dzierżawy</span><span class="sxs-lookup"><span data-stu-id="08dc9-111">Create a tenant</span></span>

<span data-ttu-id="08dc9-112">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Konfigurowanie dzierżawy](/azure/active-directory/develop/quickstart-create-new-tenant) w celu utworzenia dzierżawy w usłudze AAD.</span><span class="sxs-lookup"><span data-stu-id="08dc9-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="08dc9-113">Rejestrowanie aplikacji interfejsu API serwera</span><span class="sxs-lookup"><span data-stu-id="08dc9-113">Register a server API app</span></span>

<span data-ttu-id="08dc9-114">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Zarejestruj aplikację przy użyciu platformy tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app) i kolejnych tematów usługi Azure AAD, aby zarejestrować aplikację w usłudze AAD dla *aplikacji interfejsu API serwera*:</span><span class="sxs-lookup"><span data-stu-id="08dc9-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="08dc9-115">W **Azure Active Directory**  >  **rejestracje aplikacji**wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="08dc9-115">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="08dc9-116">Podaj **nazwę** aplikacji (na przykład \*\* Blazor serwer AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="08dc9-116">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="08dc9-117">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="08dc9-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="08dc9-118">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="08dc9-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="08dc9-119">*Aplikacja interfejsu API serwera* nie wymaga **identyfikatora URI przekierowania** w tym scenariuszu, więc pozostaw listę rozwijaną w **sieci Web** i nie wprowadzaj identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="08dc9-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="08dc9-120">Wyłącz **Permissions**  >  pole wyboru**Przyznaj administratorowi uprawnienia do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="08dc9-120">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="08dc9-121">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="08dc9-121">Select **Register**.</span></span>

<span data-ttu-id="08dc9-122">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="08dc9-122">Record the following information:</span></span>

* <span data-ttu-id="08dc9-123">*Aplikacja interfejsu API serwera* Identyfikator aplikacji (identyfikator klienta) (na przykład `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="08dc9-123">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="08dc9-124">Identyfikator katalogu (identyfikator dzierżawy) (na przykład `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="08dc9-124">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="08dc9-125">Domena dzierżawy usługi AAD (na przykład `contoso.onmicrosoft.com` ) &ndash; domena jest dostępna jako **domena wydawcy** w bloku **znakowania** Azure Portal dla zarejestrowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08dc9-125">AAD Tenant domain (for example, `contoso.onmicrosoft.com`) &ndash; The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="08dc9-126">W obszarze **uprawnienia interfejsu API**usuń uprawnienie **Microsoft Graph**  >  **User. Read** , ponieważ aplikacja nie wymaga dostępu do profilu logowania lub użytkownika.</span><span class="sxs-lookup"><span data-stu-id="08dc9-126">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="08dc9-127">W obszarze **Uwidacznianie interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="08dc9-127">In **Expose an API**:</span></span>

1. <span data-ttu-id="08dc9-128">Wybierz polecenie **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="08dc9-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="08dc9-129">Wybierz przycisk **Zapisz i kontynuuj**.</span><span class="sxs-lookup"><span data-stu-id="08dc9-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="08dc9-130">Podaj **nazwę zakresu** (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="08dc9-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="08dc9-131">Podaj **nazwę wyświetlaną zgody administratora** (na przykład `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="08dc9-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="08dc9-132">Podaj **Opis zgody administratora** (na przykład `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="08dc9-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="08dc9-133">Upewnij się, że **stan** jest ustawiony na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="08dc9-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="08dc9-134">Wybierz pozycję **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="08dc9-134">Select **Add scope**.</span></span>

<span data-ttu-id="08dc9-135">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="08dc9-135">Record the following information:</span></span>

* <span data-ttu-id="08dc9-136">Identyfikator URI identyfikatora aplikacji (na przykład `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` lub podana wartość niestandardowa)</span><span class="sxs-lookup"><span data-stu-id="08dc9-136">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="08dc9-137">Zakres domyślny (na przykład `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="08dc9-137">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="08dc9-138">Rejestrowanie aplikacji klienckiej</span><span class="sxs-lookup"><span data-stu-id="08dc9-138">Register a client app</span></span>

<span data-ttu-id="08dc9-139">Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Zarejestruj aplikację przy użyciu platformy tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app) i kolejnych tematów usługi Azure AAD, aby zarejestrować aplikację w usłudze AAD dla *aplikacji klienckiej*:</span><span class="sxs-lookup"><span data-stu-id="08dc9-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="08dc9-140">W **Azure Active Directory**  >  **rejestracje aplikacji**wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="08dc9-140">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="08dc9-141">Podaj **nazwę** aplikacji (na przykład \*\* Blazor klienta AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="08dc9-141">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="08dc9-142">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="08dc9-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="08dc9-143">W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).</span><span class="sxs-lookup"><span data-stu-id="08dc9-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="08dc9-144">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** na wartość **Web**i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="08dc9-144">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="08dc9-145">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="08dc9-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="08dc9-146">Aby uzyskać IIS Express, generowany losowo port można znaleźć we właściwościach aplikacji serwera w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="08dc9-146">For IIS Express, the randomly generated port can be found in the Server app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="08dc9-147">Wyłącz **Permissions**  >  pole wyboru**Przyznaj administratorowi uprawnienia do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="08dc9-147">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="08dc9-148">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="08dc9-148">Select **Register**.</span></span>

<span data-ttu-id="08dc9-149">Zapisz identyfikator aplikacji *klienta* (identyfikator klienta) (na przykład `33333333-3333-3333-3333-333333333333` ).</span><span class="sxs-lookup"><span data-stu-id="08dc9-149">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

<span data-ttu-id="08dc9-150">W **Authentication**obszarze  >  **konfiguracje platformy**uwierzytelniania w  >  **sieci Web**:</span><span class="sxs-lookup"><span data-stu-id="08dc9-150">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="08dc9-151">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="08dc9-151">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="08dc9-152">W przypadku **niejawnego przydzielenia**zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="08dc9-152">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="08dc9-153">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="08dc9-153">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="08dc9-154">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="08dc9-154">Select the **Save** button.</span></span>

<span data-ttu-id="08dc9-155">W **uprawnienia interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="08dc9-155">In **API permissions**:</span></span>

1. <span data-ttu-id="08dc9-156">Upewnij się, że aplikacja ma **Microsoft Graph**  >  uprawnienie**użytkownika. odczyt** .</span><span class="sxs-lookup"><span data-stu-id="08dc9-156">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="08dc9-157">Wybierz pozycję **Dodaj uprawnienia** , a następnie **Moje interfejsy API**.</span><span class="sxs-lookup"><span data-stu-id="08dc9-157">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="08dc9-158">Wybierz *aplikację interfejsu API serwera* z kolumny **Nazwa** (na przykład \*\* Blazor serwer AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="08dc9-158">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="08dc9-159">Otwórz listę **interfejsów API** .</span><span class="sxs-lookup"><span data-stu-id="08dc9-159">Open the **API** list.</span></span>
1. <span data-ttu-id="08dc9-160">Włącz dostęp do interfejsu API (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="08dc9-160">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="08dc9-161">Wybierz pozycję **Dodaj uprawnienia**.</span><span class="sxs-lookup"><span data-stu-id="08dc9-161">Select **Add permissions**.</span></span>
1. <span data-ttu-id="08dc9-162">Wybierz przycisk **Udziel zawartości administratora dla {Nazwa dzierżawy}** .</span><span class="sxs-lookup"><span data-stu-id="08dc9-162">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="08dc9-163">Kliknij przycisk **Tak**, aby potwierdzić.</span><span class="sxs-lookup"><span data-stu-id="08dc9-163">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="08dc9-164">Tworzymy aplikację.</span><span class="sxs-lookup"><span data-stu-id="08dc9-164">Create the app</span></span>

<span data-ttu-id="08dc9-165">Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="08dc9-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="08dc9-166">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, Uwzględnij opcję Output w poleceniu z ścieżką (na przykład `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="08dc9-166">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="08dc9-167">Nazwa folderu jest również częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="08dc9-167">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="08dc9-168">Przekaż identyfikator URI aplikacji do `app-id-uri` opcji, ale Uwaga w aplikacji klienckiej może być wymagana zmiana konfiguracji, która jest opisana w sekcji [zakresy tokenu dostępu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="08dc9-168">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="08dc9-169">Konfiguracja aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="08dc9-169">Server app configuration</span></span>

<span data-ttu-id="08dc9-170">*Ta sekcja dotyczy aplikacji **serwerowej** rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="08dc9-170">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="08dc9-171">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="08dc9-171">Authentication package</span></span>

<span data-ttu-id="08dc9-172">Obsługa uwierzytelniania i autoryzowania wywołań ASP.NET Core interfejsów API sieci Web jest zapewniana przez `Microsoft.AspNetCore.Authentication.AzureAD.UI` :</span><span class="sxs-lookup"><span data-stu-id="08dc9-172">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.2.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="08dc9-173">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="08dc9-173">Authentication service support</span></span>

<span data-ttu-id="08dc9-174">Metoda konfiguruje `AddAuthentication` usługi uwierzytelniania w ramach aplikacji i konfiguruje procedurę obsługi okaziciela JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="08dc9-174">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="08dc9-175">`AddAzureADBearer`Metoda ustawia określone parametry w obsłudze okaziciela JWT wymagane do weryfikacji tokenów emitowanych przez Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="08dc9-175">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="08dc9-176">`UseAuthentication`i `UseAuthorization` upewnij się, że:</span><span class="sxs-lookup"><span data-stu-id="08dc9-176">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="08dc9-177">Aplikacja próbuje analizować tokeny i sprawdzać ich poprawność w żądaniach przychodzących.</span><span class="sxs-lookup"><span data-stu-id="08dc9-177">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="08dc9-178">Wszystkie żądania próbujące uzyskać dostęp do chronionego zasobu bez poprawnego poświadczenia nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="08dc9-178">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="08dc9-179">Użytkownik. Identity . Nazwij</span><span class="sxs-lookup"><span data-stu-id="08dc9-179">User.Identity.Name</span></span>

<span data-ttu-id="08dc9-180">Domyślnie interfejs API aplikacji serwera wypełnia `User.Identity.Name` wartość z `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` typu "typ" (na przykład `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="08dc9-180">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="08dc9-181">Aby skonfigurować aplikację do odbierania wartości z `name` typu, należy skonfigurować [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="08dc9-181">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="08dc9-182">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="08dc9-182">App settings</span></span>

<span data-ttu-id="08dc9-183">Plik *appSettings. JSON* zawiera opcje konfigurowania procedury obsługi okaziciela JWT używanej do sprawdzania poprawności tokenów dostępu:</span><span class="sxs-lookup"><span data-stu-id="08dc9-183">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="08dc9-184">Przykład:</span><span class="sxs-lookup"><span data-stu-id="08dc9-184">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="08dc9-185">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="08dc9-185">WeatherForecast controller</span></span>

<span data-ttu-id="08dc9-186">Kontroler WeatherForecast (*controllers/WeatherForecastController. cs*) ujawnia chroniony interfejs API z `[Authorize]` atrybutem zastosowanym do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="08dc9-186">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="08dc9-187">**Ważne** jest, aby zrozumieć, że:</span><span class="sxs-lookup"><span data-stu-id="08dc9-187">It's **important** to understand that:</span></span>

* <span data-ttu-id="08dc9-188">`[Authorize]`Atrybut w tym kontrolerze interfejsu API jest jedynym warunkiem, że ten interfejs API jest chroniony przed nieautoryzowanym dostępem.</span><span class="sxs-lookup"><span data-stu-id="08dc9-188">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="08dc9-189">`[Authorize]`Atrybut używany w Blazor aplikacji webassembly służy tylko jako Wskazówka dla aplikacji, którą użytkownik powinien mieć autoryzację, aby aplikacja działała poprawnie.</span><span class="sxs-lookup"><span data-stu-id="08dc9-189">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="08dc9-190">Konfiguracja aplikacji klienta</span><span class="sxs-lookup"><span data-stu-id="08dc9-190">Client app configuration</span></span>

<span data-ttu-id="08dc9-191">*Ta sekcja dotyczy aplikacji **klienckiej** rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="08dc9-191">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="08dc9-192">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="08dc9-192">Authentication package</span></span>

<span data-ttu-id="08dc9-193">Gdy aplikacja zostanie utworzona w celu korzystania z kont służbowych ( `SingleOrg` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="08dc9-193">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="08dc9-194">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="08dc9-194">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="08dc9-195">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="08dc9-195">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="08dc9-196">`Microsoft.Authentication.WebAssembly.Msal`Pakiet zawiera przechodnie Dodawanie `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08dc9-196">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="08dc9-197">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="08dc9-197">Authentication service support</span></span>

<span data-ttu-id="08dc9-198">Dodano obsługę `HttpClient` wystąpień, które obejmują tokeny dostępu podczas wykonywania żądań do projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="08dc9-198">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="08dc9-199">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="08dc9-199">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="08dc9-200">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu `AddMsalAuthentication` metody rozszerzenia dostarczonej przez `Microsoft.Authentication.WebAssembly.Msal` pakiet.</span><span class="sxs-lookup"><span data-stu-id="08dc9-200">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="08dc9-201">Ta metoda konfiguruje usługi wymagane przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="08dc9-201">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="08dc9-202">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="08dc9-202">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="08dc9-203">`AddMsalAuthentication`Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08dc9-203">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="08dc9-204">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD w witrynie Azure Portal podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08dc9-204">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="08dc9-205">Konfiguracja jest dostarczana przez plik *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="08dc9-205">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="08dc9-206">Przykład:</span><span class="sxs-lookup"><span data-stu-id="08dc9-206">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="08dc9-207">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="08dc9-207">Access token scopes</span></span>

<span data-ttu-id="08dc9-208">Domyślne zakresy tokenów dostępu reprezentują listę zakresów tokenów dostępu, które są następujące:</span><span class="sxs-lookup"><span data-stu-id="08dc9-208">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="08dc9-209">Uwzględnione domyślnie w żądaniu logowania.</span><span class="sxs-lookup"><span data-stu-id="08dc9-209">Included by default in the sign in request.</span></span>
* <span data-ttu-id="08dc9-210">Służy do aprowizacji tokenu dostępu zaraz po uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="08dc9-210">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="08dc9-211">Wszystkie zakresy muszą należeć do tej samej aplikacji na Azure Active Directory reguł.</span><span class="sxs-lookup"><span data-stu-id="08dc9-211">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="08dc9-212">Dodatkowe zakresy można dodać do dodatkowych aplikacji interfejsu API w razie potrzeby:</span><span class="sxs-lookup"><span data-stu-id="08dc9-212">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="08dc9-213">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="08dc9-213">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="08dc9-214">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="08dc9-214">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="08dc9-215">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="08dc9-215">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="08dc9-216">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="08dc9-216">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="08dc9-217">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="08dc9-217">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="08dc9-218">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="08dc9-218">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="08dc9-219">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="08dc9-219">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="08dc9-220">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="08dc9-220">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="08dc9-221">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="08dc9-221">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="08dc9-222">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="08dc9-222">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="08dc9-223">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="08dc9-223">Run the app</span></span>

<span data-ttu-id="08dc9-224">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="08dc9-224">Run the app from the Server project.</span></span> <span data-ttu-id="08dc9-225">W przypadku korzystania z programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="08dc9-225">When using Visual Studio, either:</span></span>

* <span data-ttu-id="08dc9-226">Ustaw listę rozwijaną **projekty startowe** na pasku narzędzi do *aplikacji interfejsu API serwera* i wybierz przycisk **Uruchom** .</span><span class="sxs-lookup"><span data-stu-id="08dc9-226">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="08dc9-227">Wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .</span><span class="sxs-lookup"><span data-stu-id="08dc9-227">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="08dc9-228">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="08dc9-228">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="08dc9-229">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="08dc9-229">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="08dc9-230">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="08dc9-230">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
