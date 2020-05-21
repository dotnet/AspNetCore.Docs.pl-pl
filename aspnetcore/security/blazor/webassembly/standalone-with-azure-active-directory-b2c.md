---
<span data-ttu-id="7b42c-101">title: "Zabezpiecz Blazor aplikację autonomiczną ASP.NET Core webassembly z Azure Active Directory B2C" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7b42c-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7b42c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7b42c-102">'Blazor'</span></span>
- <span data-ttu-id="7b42c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7b42c-103">'Identity'</span></span>
- <span data-ttu-id="7b42c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7b42c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="7b42c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7b42c-105">'Razor'</span></span>
- <span data-ttu-id="7b42c-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="7b42c-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="7b42c-107">Zabezpiecz ASP.NET Core Blazor autonomiczną aplikację webassembly z Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="7b42c-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="7b42c-108">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7b42c-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7b42c-109">Aby utworzyć Blazor autonomiczną aplikację webassembly, która używa usługi [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) do uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="7b42c-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="7b42c-110">Postępuj zgodnie ze wskazówkami w poniższych tematach, aby utworzyć dzierżawę i zarejestrować aplikację sieci Web w witrynie Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="7b42c-110">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="7b42c-111">Tworzenie dzierżawy AAD B2C</span><span class="sxs-lookup"><span data-stu-id="7b42c-111">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="7b42c-112">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="7b42c-112">Record the following information:</span></span>

* <span data-ttu-id="7b42c-113">Wystąpienie AAD B2C (na przykład `https://contoso.b2clogin.com/` , które obejmuje końcowy ukośnik).</span><span class="sxs-lookup"><span data-stu-id="7b42c-113">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span>
* <span data-ttu-id="7b42c-114">AAD B2C domenę dzierżawy (na przykład `contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="7b42c-114">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="7b42c-115">Postępuj zgodnie ze wskazówkami w [samouczku: Zarejestruj aplikację w Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) ponownie, aby zarejestrować aplikację usługi AAD dla *aplikacji klienckiej*:</span><span class="sxs-lookup"><span data-stu-id="7b42c-115">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="7b42c-116">W **Azure Active Directory**  >  **rejestracje aplikacji**wybierz pozycję **Nowa rejestracja**.</span><span class="sxs-lookup"><span data-stu-id="7b42c-116">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="7b42c-117">Podaj **nazwę** aplikacji (na przykład \*\* Blazor autonomiczna AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="7b42c-117">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="7b42c-118">W przypadku **obsługiwanych typów kont**wybierz opcję wiele dzierżawców: **konta w dowolnym katalogu organizacyjnym lub dowolnego dostawcę tożsamości. Do uwierzytelniania użytkowników za pomocą Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="7b42c-118">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="7b42c-119">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="7b42c-119">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="7b42c-120">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="7b42c-120">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="7b42c-121">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7b42c-121">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="7b42c-122">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="7b42c-122">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="7b42c-123">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="7b42c-123">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="7b42c-124">Uwaga zostanie wyświetlona w dalszej części tego tematu, aby przypominać IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="7b42c-124">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="7b42c-125">Upewnij się, że **uprawnienia**  >  **przyznają administratorowi wartość OpenID Connect, a uprawnienia offline_access** są włączone.</span><span class="sxs-lookup"><span data-stu-id="7b42c-125">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="7b42c-126">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="7b42c-126">Select **Register**.</span></span>

<span data-ttu-id="7b42c-127">Zapisz identyfikator aplikacji (identyfikator klienta) (na przykład `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="7b42c-127">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="7b42c-128">W **Authentication**obszarze  >  **konfiguracje platformy**uwierzytelniania w  >  **sieci Web**:</span><span class="sxs-lookup"><span data-stu-id="7b42c-128">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="7b42c-129">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="7b42c-129">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="7b42c-130">W przypadku **niejawnego przydzielenia**zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="7b42c-130">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="7b42c-131">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="7b42c-131">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="7b42c-132">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="7b42c-132">Select the **Save** button.</span></span>

<span data-ttu-id="7b42c-133">W **Home**obszarze  >  **Azure AD B2C**  >  **przepływy użytkowników**w Azure AD B2C domowej:</span><span class="sxs-lookup"><span data-stu-id="7b42c-133">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="7b42c-134">Tworzenie przepływu użytkownika dotyczącego rejestracji i logowania</span><span class="sxs-lookup"><span data-stu-id="7b42c-134">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="7b42c-135">**Application claims**  >  **Display Name** Aby wypełnić `context.User.Identity.Name` `LoginDisplay` składnik (*Shared/LoginDisplay. Razor*), wybierz co najmniej atrybut użytkownika nazwa wyświetlana oświadczenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7b42c-135">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="7b42c-136">Zapisz nazwę nowego przepływu logowania i logowania utworzonego dla aplikacji (na przykład `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="7b42c-136">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="7b42c-137">Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="7b42c-137">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

<span data-ttu-id="7b42c-138">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, Uwzględnij opcję Output w poleceniu z ścieżką (na przykład `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="7b42c-138">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="7b42c-139">Nazwa folderu jest również częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="7b42c-139">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="7b42c-140">W Azure Portal **Authentication**  >  **Konfiguracja platformy**uwierzytelniania w  >  **sieci Web**  >  **Identyfikator URI przekierowania** dla aplikacji jest skonfigurowany dla portu 5001 dla aplikacji, które działają na serwerze Kestrel z ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="7b42c-140">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="7b42c-141">Jeśli aplikacja jest uruchamiana na losowo IIS Express porcie, port aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="7b42c-141">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="7b42c-142">Jeśli port nie został wcześniej skonfigurowany przy użyciu znanego portu aplikacji, Wróć do rejestracji aplikacji w Azure Portal i zaktualizuj identyfikator URI przekierowania z prawidłowym portem.</span><span class="sxs-lookup"><span data-stu-id="7b42c-142">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="7b42c-143">Po utworzeniu aplikacji powinno być możliwe:</span><span class="sxs-lookup"><span data-stu-id="7b42c-143">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="7b42c-144">Zaloguj się do aplikacji przy użyciu konta użytkownika usługi AAD.</span><span class="sxs-lookup"><span data-stu-id="7b42c-144">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="7b42c-145">Zażądaj tokenów dostępu dla interfejsów API firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="7b42c-145">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="7b42c-146">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="7b42c-146">For more information, see:</span></span>
  * [<span data-ttu-id="7b42c-147">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="7b42c-147">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="7b42c-148">[Szybki Start: Konfigurowanie aplikacji do udostępniania interfejsów API sieci Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="7b42c-148">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="7b42c-149">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="7b42c-149">Authentication package</span></span>

<span data-ttu-id="7b42c-150">Gdy aplikacja zostanie utworzona w celu korzystania z pojedynczego konta B2C ( `IndividualB2C` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="7b42c-150">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="7b42c-151">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="7b42c-151">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7b42c-152">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7b42c-152">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="7b42c-153">`Microsoft.Authentication.WebAssembly.Msal`Pakiet zawiera przechodnie Dodawanie `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7b42c-153">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="7b42c-154">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="7b42c-154">Authentication service support</span></span>

<span data-ttu-id="7b42c-155">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu `AddMsalAuthentication` metody rozszerzenia dostarczonej przez `Microsoft.Authentication.WebAssembly.Msal` pakiet.</span><span class="sxs-lookup"><span data-stu-id="7b42c-155">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="7b42c-156">Ta metoda umożliwia skonfigurowanie wszystkich usług wymaganych przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="7b42c-156">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="7b42c-157">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7b42c-157">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="7b42c-158">`AddMsalAuthentication`Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7b42c-158">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="7b42c-159">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7b42c-159">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="7b42c-160">Konfiguracja jest dostarczana przez plik *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="7b42c-160">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="7b42c-161">Przykład:</span><span class="sxs-lookup"><span data-stu-id="7b42c-161">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="7b42c-162">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="7b42c-162">Access token scopes</span></span>

<span data-ttu-id="7b42c-163">BlazorSzablon webassembly nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="7b42c-163">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="7b42c-164">Aby zainicjować obsługę administracyjną tokenu dostępu w ramach przepływu logowania, Dodaj zakres do domyślnych zakresów tokenów dostępu `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="7b42c-164">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="7b42c-165">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="7b42c-165">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="7b42c-166">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="7b42c-166">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="7b42c-167">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="7b42c-167">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="7b42c-168">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="7b42c-168">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="7b42c-169">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="7b42c-169">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="7b42c-170">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="7b42c-170">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="7b42c-171">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="7b42c-171">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="7b42c-172">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="7b42c-172">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="7b42c-173">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="7b42c-173">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7b42c-174">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="7b42c-174">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="7b42c-175">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="7b42c-175">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="7b42c-176">Samouczek: tworzenie dzierżawy usługi Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="7b42c-176">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="7b42c-177">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="7b42c-177">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
