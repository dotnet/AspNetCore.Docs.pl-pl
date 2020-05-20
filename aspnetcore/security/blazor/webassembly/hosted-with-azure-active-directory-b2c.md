---
<span data-ttu-id="6aa7c-101">title: "Zabezpiecz Blazor aplikację hostowaną ASP.NET Core webassembly z Azure Active Directory B2C" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6aa7c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6aa7c-102">'Blazor'</span></span>
- <span data-ttu-id="6aa7c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6aa7c-103">'Identity'</span></span>
- <span data-ttu-id="6aa7c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6aa7c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="6aa7c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6aa7c-105">'Razor'</span></span>
- <span data-ttu-id="6aa7c-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="6aa7c-107">Zabezpiecz Blazor aplikację hostowaną ASP.NET Core webassembly przy użyciu Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="6aa7c-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="6aa7c-108">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6aa7c-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6aa7c-109">W tym artykule opisano sposób tworzenia Blazor autonomicznej aplikacji sieci Webassembly korzystającej z usługi [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) na potrzeby uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-109">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="6aa7c-110">Rejestrowanie aplikacji w AAD B2C i tworzenie rozwiązania</span><span class="sxs-lookup"><span data-stu-id="6aa7c-110">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="6aa7c-111">Tworzenie dzierżawy</span><span class="sxs-lookup"><span data-stu-id="6aa7c-111">Create a tenant</span></span>

<span data-ttu-id="6aa7c-112">Postępuj zgodnie ze wskazówkami w [samouczku: Tworzenie dzierżawy Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) , aby utworzyć dzierżawę AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-112">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="6aa7c-113">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-113">Record the following information:</span></span>

* <span data-ttu-id="6aa7c-114">Wystąpienie AAD B2C (na przykład `https://contoso.b2clogin.com/` , które obejmuje końcowy ukośnik)</span><span class="sxs-lookup"><span data-stu-id="6aa7c-114">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="6aa7c-115">AAD B2C domeny dzierżawy (na przykład `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="6aa7c-115">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="6aa7c-116">Rejestrowanie aplikacji interfejsu API serwera</span><span class="sxs-lookup"><span data-stu-id="6aa7c-116">Register a server API app</span></span>

<span data-ttu-id="6aa7c-117">Postępuj zgodnie ze wskazówkami w [samouczku: Zarejestruj aplikację w Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) , aby zarejestrować aplikację usługi AAD dla *aplikacji interfejsu API serwera*:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-117">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="6aa7c-118">W **Azure Active Directory**  >  **rejestracje aplikacji**wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-118">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="6aa7c-119">Podaj **nazwę** aplikacji (na przykład \*\* Blazor serwer AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="6aa7c-119">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="6aa7c-120">W przypadku **obsługiwanych typów kont**wybierz opcję wiele dzierżawców: **konta w dowolnym katalogu organizacyjnym lub dowolnego dostawcę tożsamości. Do uwierzytelniania użytkowników za pomocą Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="6aa7c-120">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="6aa7c-121">*Aplikacja interfejsu API serwera* nie wymaga **identyfikatora URI przekierowania** w tym scenariuszu, więc pozostaw listę rozwijaną w **sieci Web** i nie wprowadzaj identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-121">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="6aa7c-122">Upewnij się, że **uprawnienia**  >  **przyznają administratorowi wartość OpenID Connect, a uprawnienia offline_access** są włączone.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-122">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="6aa7c-123">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-123">Select **Register**.</span></span>

<span data-ttu-id="6aa7c-124">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-124">Record the following information:</span></span>

* <span data-ttu-id="6aa7c-125">*Aplikacja interfejsu API serwera* Identyfikator aplikacji (identyfikator klienta) (na przykład `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="6aa7c-125">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="6aa7c-126">Identyfikator katalogu (identyfikator dzierżawy) (na przykład `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="6aa7c-126">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="6aa7c-127">Domena dzierżawy usługi AAD (na przykład `contoso.onmicrosoft.com` ) &ndash; domena jest dostępna jako **domena wydawcy** w bloku **znakowania** Azure Portal dla zarejestrowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-127">AAD Tenant domain (for example, `contoso.onmicrosoft.com`) &ndash; The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="6aa7c-128">W obszarze **Uwidacznianie interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-128">In **Expose an API**:</span></span>

1. <span data-ttu-id="6aa7c-129">Wybierz polecenie **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-129">Select **Add a scope**.</span></span>
1. <span data-ttu-id="6aa7c-130">Wybierz przycisk **Zapisz i kontynuuj**.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-130">Select **Save and continue**.</span></span>
1. <span data-ttu-id="6aa7c-131">Podaj **nazwę zakresu** (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="6aa7c-131">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="6aa7c-132">Podaj **nazwę wyświetlaną zgody administratora** (na przykład `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="6aa7c-132">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="6aa7c-133">Podaj **Opis zgody administratora** (na przykład `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="6aa7c-133">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="6aa7c-134">Upewnij się, że **stan** jest ustawiony na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-134">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="6aa7c-135">Wybierz pozycję **Dodaj zakres**.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-135">Select **Add scope**.</span></span>

<span data-ttu-id="6aa7c-136">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-136">Record the following information:</span></span>

* <span data-ttu-id="6aa7c-137">Identyfikator URI identyfikatora aplikacji (na przykład `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` lub podana wartość niestandardowa)</span><span class="sxs-lookup"><span data-stu-id="6aa7c-137">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="6aa7c-138">Zakres domyślny (na przykład `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="6aa7c-138">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="6aa7c-139">Rejestrowanie aplikacji klienckiej</span><span class="sxs-lookup"><span data-stu-id="6aa7c-139">Register a client app</span></span>

<span data-ttu-id="6aa7c-140">Postępuj zgodnie ze wskazówkami w [samouczku: Zarejestruj aplikację w Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) ponownie, aby zarejestrować aplikację usługi AAD dla *aplikacji klienckiej*:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-140">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="6aa7c-141">W **Azure Active Directory**  >  **rejestracje aplikacji**wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-141">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="6aa7c-142">Podaj **nazwę** aplikacji (na przykład \*\* Blazor AAD B2C klienta\*\*).</span><span class="sxs-lookup"><span data-stu-id="6aa7c-142">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="6aa7c-143">W przypadku **obsługiwanych typów kont**wybierz opcję wiele dzierżawców: **konta w dowolnym katalogu organizacyjnym lub dowolnego dostawcę tożsamości. Do uwierzytelniania użytkowników za pomocą Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="6aa7c-143">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="6aa7c-144">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** na wartość **Web**i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="6aa7c-144">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="6aa7c-145">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="6aa7c-146">Aby uzyskać IIS Express, generowany losowo port można znaleźć we właściwościach aplikacji serwera w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="6aa7c-146">For IIS Express, the randomly generated port can be found in the Server app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="6aa7c-147">Upewnij się, że **uprawnienia**  >  **przyznają administratorowi wartość OpenID Connect, a uprawnienia offline_access** są włączone.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-147">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="6aa7c-148">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-148">Select **Register**.</span></span>

<span data-ttu-id="6aa7c-149">Zapisz identyfikator aplikacji (identyfikator klienta) (na przykład `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="6aa7c-149">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="6aa7c-150">W **Authentication**obszarze  >  **konfiguracje platformy**uwierzytelniania w  >  **sieci Web**:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-150">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="6aa7c-151">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="6aa7c-151">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="6aa7c-152">W przypadku **niejawnego przydzielenia**zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-152">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="6aa7c-153">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-153">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="6aa7c-154">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-154">Select the **Save** button.</span></span>

<span data-ttu-id="6aa7c-155">W **uprawnienia interfejsu API**:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-155">In **API permissions**:</span></span>

1. <span data-ttu-id="6aa7c-156">Wybierz pozycję **Dodaj uprawnienia** , a następnie **Moje interfejsy API**.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-156">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="6aa7c-157">Wybierz *aplikację interfejsu API serwera* z kolumny **Nazwa** (na przykład \*\* Blazor serwer AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="6aa7c-157">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="6aa7c-158">Otwórz listę **interfejsów API** .</span><span class="sxs-lookup"><span data-stu-id="6aa7c-158">Open the **API** list.</span></span>
1. <span data-ttu-id="6aa7c-159">Włącz dostęp do interfejsu API (na przykład `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="6aa7c-159">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="6aa7c-160">Wybierz pozycję **Dodaj uprawnienia**.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-160">Select **Add permissions**.</span></span>
1. <span data-ttu-id="6aa7c-161">Wybierz przycisk **Udziel zawartości administratora dla {Nazwa dzierżawy}** .</span><span class="sxs-lookup"><span data-stu-id="6aa7c-161">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="6aa7c-162">Kliknij przycisk **Tak**, aby potwierdzić.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-162">Select **Yes** to confirm.</span></span>

<span data-ttu-id="6aa7c-163">W **Home**obszarze  >  **Azure AD B2C**  >  **przepływy użytkowników**w Azure AD B2C domowej:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-163">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="6aa7c-164">Tworzenie przepływu użytkownika dotyczącego rejestracji i logowania</span><span class="sxs-lookup"><span data-stu-id="6aa7c-164">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="6aa7c-165">**Application claims**  >  **Display Name** Aby wypełnić `context.User.Identity.Name` `LoginDisplay` składnik (*Shared/LoginDisplay. Razor*), wybierz co najmniej atrybut użytkownika nazwa wyświetlana oświadczenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-165">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="6aa7c-166">Zapisz nazwę nowego przepływu logowania i logowania utworzonego dla aplikacji (na przykład `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="6aa7c-166">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="6aa7c-167">Tworzymy aplikację.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-167">Create the app</span></span>

<span data-ttu-id="6aa7c-168">Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-168">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="6aa7c-169">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, Uwzględnij opcję Output w poleceniu z ścieżką (na przykład `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="6aa7c-169">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="6aa7c-170">Nazwa folderu jest również częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-170">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="6aa7c-171">Przekaż identyfikator URI aplikacji do `app-id-uri` opcji, ale Uwaga w aplikacji klienckiej może być wymagana zmiana konfiguracji, która jest opisana w sekcji [zakresy tokenu dostępu](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="6aa7c-171">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="6aa7c-172">Ponadto zakres skonfigurowany przez Blazor szablon hostowany może być powtórzony na HOŚCIE identyfikatora URI aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-172">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="6aa7c-173">Upewnij się, że zakres skonfigurowany dla `DefaultAccessTokenScopes` kolekcji jest poprawny w `Program.Main` (*program.cs*) *aplikacji klienckiej*.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-173">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (*Program.cs*) of the *Client app*.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="6aa7c-174">Konfiguracja aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="6aa7c-174">Server app configuration</span></span>

<span data-ttu-id="6aa7c-175">*Ta sekcja dotyczy aplikacji **serwerowej** rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="6aa7c-175">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="6aa7c-176">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6aa7c-176">Authentication package</span></span>

<span data-ttu-id="6aa7c-177">Obsługa uwierzytelniania i autoryzowania wywołań ASP.NET Core interfejsów API sieci Web jest zapewniana przez `Microsoft.AspNetCore.Authentication.AzureADB2C.UI` :</span><span class="sxs-lookup"><span data-stu-id="6aa7c-177">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.2.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="6aa7c-178">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6aa7c-178">Authentication service support</span></span>

<span data-ttu-id="6aa7c-179">Metoda konfiguruje `AddAuthentication` usługi uwierzytelniania w ramach aplikacji i konfiguruje procedurę obsługi okaziciela JWT jako domyślną metodę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-179">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="6aa7c-180">`AddAzureADB2CBearer`Metoda ustawia określone parametry w obsłudze okaziciela JWT wymagane do weryfikacji tokenów emitowanych przez Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-180">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="6aa7c-181">`UseAuthentication`i `UseAuthorization` upewnij się, że:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-181">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="6aa7c-182">Aplikacja próbuje analizować tokeny i sprawdzać ich poprawność w żądaniach przychodzących.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-182">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="6aa7c-183">Wszystkie żądania próbujące uzyskać dostęp do chronionego zasobu bez poprawnego poświadczenia nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-183">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="6aa7c-184">Użytkownik. Identity . Nazwij</span><span class="sxs-lookup"><span data-stu-id="6aa7c-184">User.Identity.Name</span></span>

<span data-ttu-id="6aa7c-185">Domyślnie wartość `User.Identity.Name` nie jest wypełniona.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-185">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="6aa7c-186">Aby skonfigurować aplikację do odbierania wartości z `name` typu, należy skonfigurować [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w programie `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6aa7c-186">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="6aa7c-187">Ustawienia aplikacji</span><span class="sxs-lookup"><span data-stu-id="6aa7c-187">App settings</span></span>

<span data-ttu-id="6aa7c-188">Plik *appSettings. JSON* zawiera opcje konfigurowania procedury obsługi okaziciela JWT używanej do sprawdzania poprawności tokenów dostępu.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-188">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="6aa7c-189">Przykład:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-189">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="6aa7c-190">Kontroler WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="6aa7c-190">WeatherForecast controller</span></span>

<span data-ttu-id="6aa7c-191">Kontroler WeatherForecast (*controllers/WeatherForecastController. cs*) ujawnia chroniony interfejs API z `[Authorize]` atrybutem zastosowanym do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-191">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="6aa7c-192">**Ważne** jest, aby zrozumieć, że:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-192">It's **important** to understand that:</span></span>

* <span data-ttu-id="6aa7c-193">`[Authorize]`Atrybut w tym kontrolerze interfejsu API jest jedynym warunkiem, że ten interfejs API jest chroniony przed nieautoryzowanym dostępem.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-193">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="6aa7c-194">`[Authorize]`Atrybut używany w Blazor aplikacji webassembly służy tylko jako Wskazówka dla aplikacji, którą użytkownik powinien mieć autoryzację, aby aplikacja działała poprawnie.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-194">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="6aa7c-195">Konfiguracja aplikacji klienta</span><span class="sxs-lookup"><span data-stu-id="6aa7c-195">Client app configuration</span></span>

<span data-ttu-id="6aa7c-196">*Ta sekcja dotyczy aplikacji **klienckiej** rozwiązania.*</span><span class="sxs-lookup"><span data-stu-id="6aa7c-196">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="6aa7c-197">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6aa7c-197">Authentication package</span></span>

<span data-ttu-id="6aa7c-198">Gdy aplikacja zostanie utworzona w celu korzystania z pojedynczego konta B2C ( `IndividualB2C` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="6aa7c-198">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="6aa7c-199">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-199">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6aa7c-200">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-200">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="6aa7c-201">`Microsoft.Authentication.WebAssembly.Msal`Pakiet zawiera przechodnie Dodawanie `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-201">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="6aa7c-202">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6aa7c-202">Authentication service support</span></span>

<span data-ttu-id="6aa7c-203">Dodano obsługę `HttpClient` wystąpień, które obejmują tokeny dostępu podczas wykonywania żądań do projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-203">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="6aa7c-204">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-204">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="6aa7c-205">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu `AddMsalAuthentication` metody rozszerzenia dostarczonej przez `Microsoft.Authentication.WebAssembly.Msal` pakiet.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-205">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="6aa7c-206">Ta metoda konfiguruje usługi wymagane przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="6aa7c-206">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="6aa7c-207">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-207">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="6aa7c-208">`AddMsalAuthentication`Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-208">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="6aa7c-209">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD w witrynie Azure Portal podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-209">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="6aa7c-210">Konfiguracja jest dostarczana przez plik *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="6aa7c-210">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="6aa7c-211">Przykład:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-211">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="6aa7c-212">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="6aa7c-212">Access token scopes</span></span>

<span data-ttu-id="6aa7c-213">Domyślne zakresy tokenów dostępu reprezentują listę zakresów tokenów dostępu, które są następujące:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-213">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="6aa7c-214">Uwzględnione domyślnie w żądaniu logowania.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-214">Included by default in the sign in request.</span></span>
* <span data-ttu-id="6aa7c-215">Służy do aprowizacji tokenu dostępu zaraz po uwierzytelnieniu.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-215">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="6aa7c-216">Wszystkie zakresy muszą należeć do tej samej aplikacji na Azure Active Directory reguł.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-216">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="6aa7c-217">Dodatkowe zakresy można dodać do dodatkowych aplikacji interfejsu API w razie potrzeby:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-217">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="6aa7c-218">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="6aa7c-218">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="6aa7c-219">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="6aa7c-219">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="6aa7c-220">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="6aa7c-220">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="6aa7c-221">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="6aa7c-221">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="6aa7c-222">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="6aa7c-222">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="6aa7c-223">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="6aa7c-223">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="6aa7c-224">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="6aa7c-224">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="6aa7c-225">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="6aa7c-225">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="6aa7c-226">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6aa7c-226">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="6aa7c-227">Składnik FetchData</span><span class="sxs-lookup"><span data-stu-id="6aa7c-227">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="6aa7c-228">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="6aa7c-228">Run the app</span></span>

<span data-ttu-id="6aa7c-229">Uruchom aplikację z projektu serwera.</span><span class="sxs-lookup"><span data-stu-id="6aa7c-229">Run the app from the Server project.</span></span> <span data-ttu-id="6aa7c-230">W przypadku korzystania z programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="6aa7c-230">When using Visual Studio, either:</span></span>

* <span data-ttu-id="6aa7c-231">Ustaw listę rozwijaną **projekty startowe** na pasku narzędzi do *aplikacji interfejsu API serwera* i wybierz przycisk **Uruchom** .</span><span class="sxs-lookup"><span data-stu-id="6aa7c-231">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="6aa7c-232">Wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .</span><span class="sxs-lookup"><span data-stu-id="6aa7c-232">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6aa7c-233">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6aa7c-233">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="6aa7c-234">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="6aa7c-234">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="6aa7c-235">Samouczek: tworzenie dzierżawy usługi Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="6aa7c-235">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="6aa7c-236">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="6aa7c-236">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
