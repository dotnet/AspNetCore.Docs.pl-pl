---
<span data-ttu-id="6d77a-101">title: "Zabezpiecz Blazor aplikację autonomiczną ASP.NET Core webassembly z Azure Active Directory" Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6d77a-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d77a-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d77a-102">'Blazor'</span></span>
- <span data-ttu-id="6d77a-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d77a-103">'Identity'</span></span>
- <span data-ttu-id="6d77a-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d77a-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d77a-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d77a-105">'Razor'</span></span>
- <span data-ttu-id="6d77a-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="6d77a-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="6d77a-107">Zabezpiecz ASP.NET Core Blazor autonomiczną aplikację webassembly z Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="6d77a-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="6d77a-108">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6d77a-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6d77a-109">Aby utworzyć Blazor autonomiczną aplikację webassembly, która używa usługi [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="6d77a-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="6d77a-110">[Utwórz dzierżawę usługi AAD i aplikację sieci Web](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="6d77a-110">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="6d77a-111">Zarejestruj aplikację usługi AAD w obszarze **Azure Active Directory**  >  **rejestracje aplikacji** Azure Active Directory w Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="6d77a-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="6d77a-112">Podaj **nazwę** aplikacji (na przykład \*\* Blazor autonomicznej usługi AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="6d77a-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="6d77a-113">Wybierz **obsługiwane typy kont**.</span><span class="sxs-lookup"><span data-stu-id="6d77a-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="6d77a-114">**W tym katalogu organizacji można wybrać konta tylko** dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="6d77a-114">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="6d77a-115">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="6d77a-115">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="6d77a-116">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="6d77a-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="6d77a-117">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6d77a-117">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="6d77a-118">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="6d77a-118">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="6d77a-119">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="6d77a-119">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="6d77a-120">Uwaga zostanie wyświetlona w dalszej części tego tematu, aby przypominać IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="6d77a-120">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="6d77a-121">Wyłącz **Permissions**  >  pole wyboru**Przyznaj administratorowi uprawnienia do OpenID Connect i uprawnień offline_access** .</span><span class="sxs-lookup"><span data-stu-id="6d77a-121">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="6d77a-122">Wybierz pozycję **Zarejestruj**.</span><span class="sxs-lookup"><span data-stu-id="6d77a-122">Select **Register**.</span></span>

<span data-ttu-id="6d77a-123">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="6d77a-123">Record the following information:</span></span>

* <span data-ttu-id="6d77a-124">Identyfikator aplikacji (identyfikator klienta) (na przykład `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="6d77a-124">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="6d77a-125">Identyfikator katalogu (identyfikator dzierżawy) (na przykład `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="6d77a-125">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="6d77a-126">W **Authentication**obszarze  >  **konfiguracje platformy**uwierzytelniania w  >  **sieci Web**:</span><span class="sxs-lookup"><span data-stu-id="6d77a-126">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="6d77a-127">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="6d77a-127">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="6d77a-128">W przypadku **niejawnego przydzielenia**zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.</span><span class="sxs-lookup"><span data-stu-id="6d77a-128">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="6d77a-129">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="6d77a-129">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="6d77a-130">Wybierz ikonę **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="6d77a-130">Select the **Save** button.</span></span>

<span data-ttu-id="6d77a-131">Utwórz aplikację.</span><span class="sxs-lookup"><span data-stu-id="6d77a-131">Create the app.</span></span> <span data-ttu-id="6d77a-132">Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="6d77a-132">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="6d77a-133">Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, Uwzględnij opcję Output w poleceniu z ścieżką (na przykład `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="6d77a-133">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="6d77a-134">Nazwa folderu jest również częścią nazwy projektu.</span><span class="sxs-lookup"><span data-stu-id="6d77a-134">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="6d77a-135">W Azure Portal **Authentication**  >  **Konfiguracja platformy**uwierzytelniania w  >  **sieci Web**  >  **Identyfikator URI przekierowania** dla aplikacji jest skonfigurowany dla portu 5001 dla aplikacji, które działają na serwerze Kestrel z ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="6d77a-135">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="6d77a-136">Jeśli aplikacja jest uruchamiana na losowo IIS Express porcie, port aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="6d77a-136">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="6d77a-137">Jeśli port nie został wcześniej skonfigurowany przy użyciu znanego portu aplikacji, Wróć do rejestracji aplikacji w Azure Portal i zaktualizuj identyfikator URI przekierowania z prawidłowym portem.</span><span class="sxs-lookup"><span data-stu-id="6d77a-137">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="6d77a-138">Po utworzeniu aplikacji powinno być możliwe:</span><span class="sxs-lookup"><span data-stu-id="6d77a-138">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="6d77a-139">Zaloguj się do aplikacji przy użyciu konta użytkownika usługi AAD.</span><span class="sxs-lookup"><span data-stu-id="6d77a-139">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="6d77a-140">Zażądaj tokenów dostępu dla interfejsów API firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="6d77a-140">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="6d77a-141">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="6d77a-141">For more information, see:</span></span>
  * [<span data-ttu-id="6d77a-142">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="6d77a-142">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="6d77a-143">[Szybki Start: Konfigurowanie aplikacji do udostępniania interfejsów API sieci Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="6d77a-143">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="6d77a-144">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6d77a-144">Authentication package</span></span>

<span data-ttu-id="6d77a-145">Gdy aplikacja zostanie utworzona w celu korzystania z kont służbowych ( `SingleOrg` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="6d77a-145">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="6d77a-146">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="6d77a-146">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6d77a-147">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="6d77a-147">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="6d77a-148">`Microsoft.Authentication.WebAssembly.Msal`Pakiet zawiera przechodnie Dodawanie `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6d77a-148">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="6d77a-149">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6d77a-149">Authentication service support</span></span>

<span data-ttu-id="6d77a-150">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu `AddMsalAuthentication` metody rozszerzenia dostarczonej przez `Microsoft.Authentication.WebAssembly.Msal` pakiet.</span><span class="sxs-lookup"><span data-stu-id="6d77a-150">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="6d77a-151">Ta metoda konfiguruje usługi wymagane przez aplikację do współpracy z Identity dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="6d77a-151">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="6d77a-152">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6d77a-152">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="6d77a-153">`AddMsalAuthentication`Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6d77a-153">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="6d77a-154">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6d77a-154">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="6d77a-155">Konfiguracja jest dostarczana przez plik *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="6d77a-155">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="6d77a-156">Przykład:</span><span class="sxs-lookup"><span data-stu-id="6d77a-156">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="6d77a-157">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="6d77a-157">Access token scopes</span></span>

<span data-ttu-id="6d77a-158">BlazorSzablon webassembly nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="6d77a-158">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="6d77a-159">Aby zainicjować obsługę administracyjną tokenu dostępu w ramach przepływu logowania, Dodaj zakres do domyślnych zakresów tokenów dostępu `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="6d77a-159">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="6d77a-160">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="6d77a-160">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="6d77a-161">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="6d77a-161">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="6d77a-162">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="6d77a-162">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="6d77a-163">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="6d77a-163">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="6d77a-164">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="6d77a-164">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="6d77a-165">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="6d77a-165">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="6d77a-166">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="6d77a-166">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="6d77a-167">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="6d77a-167">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="6d77a-168">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="6d77a-168">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6d77a-169">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6d77a-169">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="6d77a-170">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="6d77a-170">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="6d77a-171">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="6d77a-171">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
