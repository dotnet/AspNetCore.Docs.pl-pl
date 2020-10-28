---
title: 'Zabezpieczanie ASP.NET Core :::no-loc(Blazor WebAssembly)::: aplikacji autonomicznej za pomocą Azure Active Directory B2C'
author: guardrex
description: 'Dowiedz się, jak zabezpieczyć :::no-loc(Blazor WebAssembly)::: aplikację autonomiczną ASP.NET Core przy użyciu Azure Active Directory B2C.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 134ec3a913d19c5aba38e7419f25352e93c04e31
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690411"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="4efb1-103">Zabezpieczanie ASP.NET Core :::no-loc(Blazor WebAssembly)::: aplikacji autonomicznej za pomocą Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="4efb1-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="4efb1-104">Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4efb1-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4efb1-105">Aby utworzyć [autonomiczną :::no-loc(Blazor WebAssembly)::: aplikację](xref:blazor/hosting-models#blazor-webassembly) korzystającą z [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) do uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="4efb1-105">To create a [standalone :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="4efb1-106">Postępuj zgodnie ze wskazówkami w poniższych tematach, aby utworzyć dzierżawę i zarejestrować aplikację sieci Web w witrynie Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="4efb1-106">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="4efb1-107">Tworzenie dzierżawy AAD B2C</span><span class="sxs-lookup"><span data-stu-id="4efb1-107">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="4efb1-108">Zapisz następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="4efb1-108">Record the following information:</span></span>

* <span data-ttu-id="4efb1-109">Wystąpienie AAD B2C (na przykład `https://contoso.b2clogin.com/` , które obejmuje końcowy ukośnik): wystąpienie jest schematem i hostem rejestracji aplikacji Azure B2C, którą można znaleźć, otwierając okno **punkty końcowe** ze strony **rejestracje aplikacji** w Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="4efb1-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash): The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>
* <span data-ttu-id="4efb1-110">AAD B2C domeny podstawowej/wydawcy/dzierżawy (na przykład `contoso.onmicrosoft.com` ): domena jest dostępna jako **domena wydawcy** w bloku **znakowania** Azure Portal dla zarejestrowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4efb1-110">AAD B2C Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="4efb1-111">Postępuj zgodnie ze wskazówkami w [samouczku: Zarejestruj aplikację w Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) ponownie, aby zarejestrować aplikację usługi AAD dla *`Client`* aplikacji, a następnie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="4efb1-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="4efb1-112">W **Azure Active Directory** > **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja** .</span><span class="sxs-lookup"><span data-stu-id="4efb1-112">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="4efb1-113">Podaj **nazwę** aplikacji (na przykład **:::no-loc(Blazor)::: autonomiczna AAD B2C** ).</span><span class="sxs-lookup"><span data-stu-id="4efb1-113">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Standalone AAD B2C** ).</span></span>
1. <span data-ttu-id="4efb1-114">W przypadku **obsługiwanych typów kont** wybierz opcję wiele dzierżawców: **konta w dowolnym katalogu organizacyjnym lub dowolnego dostawcę tożsamości. Do uwierzytelniania użytkowników za pomocą Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="4efb1-114">For **Supported account types** , select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="4efb1-115">Ustaw listę rozwijaną **URI przekierowania** na **aplikację jednostronicową (Spa)** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="4efb1-115">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="4efb1-116">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="4efb1-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="4efb1-117">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4efb1-117">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="4efb1-118">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="4efb1-118">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="4efb1-119">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="4efb1-119">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="4efb1-120">Uwaga zostanie wyświetlona w dalszej części tego tematu, aby przypominać IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="4efb1-120">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="4efb1-121">Upewnij się, że **uprawnienia** > **udzielają zgody administratora na OpenID Connect, a uprawnienia offline_access** są zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="4efb1-121">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="4efb1-122">Wybierz pozycję **Zarejestruj** .</span><span class="sxs-lookup"><span data-stu-id="4efb1-122">Select **Register** .</span></span>

<span data-ttu-id="4efb1-123">Zapisz identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="4efb1-123">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="4efb1-124">W **Authentication** obszarze > **Konfiguracja platformy** uwierzytelniania > **aplikacja jednostronicowa (Spa)** :</span><span class="sxs-lookup"><span data-stu-id="4efb1-124">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="4efb1-125">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="4efb1-125">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="4efb1-126">W przypadku **niejawnego udzielenia** upewnij się, że **nie** wybrano pól wyboru dla **tokenów dostępu** i **tokenów identyfikatorów** .</span><span class="sxs-lookup"><span data-stu-id="4efb1-126">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="4efb1-127">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="4efb1-127">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="4efb1-128">Wybierz ikonę **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="4efb1-128">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="4efb1-129">W **Azure Active Directory** > **rejestracje aplikacji** wybierz pozycję **Nowa rejestracja** .</span><span class="sxs-lookup"><span data-stu-id="4efb1-129">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="4efb1-130">Podaj **nazwę** aplikacji (na przykład **:::no-loc(Blazor)::: autonomiczna AAD B2C** ).</span><span class="sxs-lookup"><span data-stu-id="4efb1-130">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Standalone AAD B2C** ).</span></span>
1. <span data-ttu-id="4efb1-131">W przypadku **obsługiwanych typów kont** wybierz opcję wiele dzierżawców: **konta w dowolnym katalogu organizacyjnym lub dowolnego dostawcę tożsamości. Do uwierzytelniania użytkowników za pomocą Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="4efb1-131">For **Supported account types** , select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="4efb1-132">Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="4efb1-132">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="4efb1-133">Domyślnym portem dla aplikacji działającej w Kestrel jest 5001.</span><span class="sxs-lookup"><span data-stu-id="4efb1-133">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="4efb1-134">Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4efb1-134">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="4efb1-135">W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="4efb1-135">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="4efb1-136">Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="4efb1-136">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="4efb1-137">Uwaga zostanie wyświetlona w dalszej części tego tematu, aby przypominać IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.</span><span class="sxs-lookup"><span data-stu-id="4efb1-137">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="4efb1-138">Upewnij się, że **uprawnienia** > **udzielają zgody administratora na OpenID Connect, a uprawnienia offline_access** są zaznaczone.</span><span class="sxs-lookup"><span data-stu-id="4efb1-138">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="4efb1-139">Wybierz pozycję **Zarejestruj** .</span><span class="sxs-lookup"><span data-stu-id="4efb1-139">Select **Register** .</span></span>

<span data-ttu-id="4efb1-140">Zapisz identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="4efb1-140">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="4efb1-141">W **Authentication** obszarze > **konfiguracje platformy** uwierzytelniania w > **sieci Web** :</span><span class="sxs-lookup"><span data-stu-id="4efb1-141">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="4efb1-142">Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="4efb1-142">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="4efb1-143">W przypadku **niejawnego przydzielenia** zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów** .</span><span class="sxs-lookup"><span data-stu-id="4efb1-143">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens** .</span></span>
1. <span data-ttu-id="4efb1-144">Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.</span><span class="sxs-lookup"><span data-stu-id="4efb1-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="4efb1-145">Wybierz ikonę **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="4efb1-145">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="4efb1-146">W **Home** obszarze  >  **Azure AD B2C**  >  **przepływy użytkowników** w Azure AD B2C domowej:</span><span class="sxs-lookup"><span data-stu-id="4efb1-146">In **Home** > **Azure AD B2C** > **User flows** :</span></span>

[<span data-ttu-id="4efb1-147">Tworzenie przepływu użytkownika dotyczącego rejestracji i logowania</span><span class="sxs-lookup"><span data-stu-id="4efb1-147">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="4efb1-148">Wybierz co najmniej **Application claims**  >  atrybut użytkownika **Nazwa wyświetlana** oświadczenia aplikacji, aby wypełnić `context.User.:::no-loc(Identity):::.Name` `LoginDisplay` składnik ( `Shared/LoginDisplay.razor` ).</span><span class="sxs-lookup"><span data-stu-id="4efb1-148">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.:::no-loc(Identity):::.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="4efb1-149">Zapisz nazwę nowego przepływu logowania i logowania utworzonego dla aplikacji (na przykład `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="4efb1-149">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="4efb1-150">W pustym folderze Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="4efb1-150">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="4efb1-151">Symbol zastępczy</span><span class="sxs-lookup"><span data-stu-id="4efb1-151">Placeholder</span></span>                   | <span data-ttu-id="4efb1-152">Nazwa Azure Portal</span><span class="sxs-lookup"><span data-stu-id="4efb1-152">Azure portal name</span></span>               | <span data-ttu-id="4efb1-153">Przykład</span><span class="sxs-lookup"><span data-stu-id="4efb1-153">Example</span></span>                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="4efb1-154">Wystąpienie</span><span class="sxs-lookup"><span data-stu-id="4efb1-154">Instance</span></span>                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `:::no-loc(Blazor):::Sample`                         |
| `{CLIENT ID}`                 | <span data-ttu-id="4efb1-155">Identyfikator aplikacji (klienta)</span><span class="sxs-lookup"><span data-stu-id="4efb1-155">Application (client) ID</span></span>         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="4efb1-156">Przepływ użytkownika rejestracji/logowania</span><span class="sxs-lookup"><span data-stu-id="4efb1-156">Sign-up/sign-in user flow</span></span>       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="4efb1-157">Domena podstawowa/Wydawca/dzierżawa</span><span class="sxs-lookup"><span data-stu-id="4efb1-157">Primary/Publisher/Tenant domain</span></span> | `contoso.onmicrosoft.com`              |

<span data-ttu-id="4efb1-158">Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4efb1-158">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="4efb1-159">W Azure Portal **Identyfikator URI przekierowania** konfiguracji platformy aplikacji jest skonfigurowany dla portu 5001 dla aplikacji, które działają na serwerze Kestrel z ustawieniami domyślnymi.</span><span class="sxs-lookup"><span data-stu-id="4efb1-159">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="4efb1-160">Jeśli aplikacja jest uruchamiana na losowo IIS Express porcie, port aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .</span><span class="sxs-lookup"><span data-stu-id="4efb1-160">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="4efb1-161">Jeśli port nie został wcześniej skonfigurowany przy użyciu znanego portu aplikacji, Wróć do rejestracji aplikacji w Azure Portal i zaktualizuj identyfikator URI przekierowania z prawidłowym portem.</span><span class="sxs-lookup"><span data-stu-id="4efb1-161">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="4efb1-162">Po utworzeniu aplikacji powinno być możliwe:</span><span class="sxs-lookup"><span data-stu-id="4efb1-162">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="4efb1-163">Zaloguj się do aplikacji przy użyciu konta użytkownika usługi AAD.</span><span class="sxs-lookup"><span data-stu-id="4efb1-163">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="4efb1-164">Zażądaj tokenów dostępu dla interfejsów API firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="4efb1-164">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="4efb1-165">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="4efb1-165">For more information, see:</span></span>
  * [<span data-ttu-id="4efb1-166">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="4efb1-166">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="4efb1-167">[Szybki Start: Konfigurowanie aplikacji do udostępniania interfejsów API sieci Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="4efb1-167">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="4efb1-168">Pakiet uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="4efb1-168">Authentication package</span></span>

<span data-ttu-id="4efb1-169">Gdy aplikacja zostanie utworzona w celu korzystania z pojedynczego konta B2C ( `IndividualB2C` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="4efb1-169">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="4efb1-170">Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="4efb1-170">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="4efb1-171">W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:</span><span class="sxs-lookup"><span data-stu-id="4efb1-171">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="4efb1-172">Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="4efb1-172">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="4efb1-173">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Pakiet zawiera przechodnie Dodawanie [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakietu do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4efb1-173">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="4efb1-174">Obsługa usługi uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="4efb1-174">Authentication service support</span></span>

<span data-ttu-id="4efb1-175">Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pakiet.</span><span class="sxs-lookup"><span data-stu-id="4efb1-175">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="4efb1-176">Ta metoda umożliwia skonfigurowanie wszystkich usług wymaganych przez aplikację do współpracy z :::no-loc(Identity)::: dostawcą (IP).</span><span class="sxs-lookup"><span data-stu-id="4efb1-176">This method sets up all of the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="4efb1-177">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="4efb1-177">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="4efb1-178"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4efb1-178">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="4efb1-179">Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD podczas rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4efb1-179">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="4efb1-180">Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="4efb1-180">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="4efb1-181">Przykład:</span><span class="sxs-lookup"><span data-stu-id="4efb1-181">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="4efb1-182">Zakresy tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="4efb1-182">Access token scopes</span></span>

<span data-ttu-id="4efb1-183">:::no-loc(Blazor WebAssembly):::Szablon nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="4efb1-183">The :::no-loc(Blazor WebAssembly)::: template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="4efb1-184">Aby zainicjować obsługę administracyjną tokenu dostępu w ramach przepływu logowania, Dodaj zakres do domyślnych zakresów tokenów dostępu <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="4efb1-184">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="4efb1-185">Określ dodatkowe zakresy z `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="4efb1-185">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="4efb1-186">Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :</span><span class="sxs-lookup"><span data-stu-id="4efb1-186">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="4efb1-187">Żądaj dodatkowych tokenów dostępu</span><span class="sxs-lookup"><span data-stu-id="4efb1-187">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="4efb1-188">Dołącz tokeny do żądań wychodzących</span><span class="sxs-lookup"><span data-stu-id="4efb1-188">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="4efb1-189">Tryb logowania</span><span class="sxs-lookup"><span data-stu-id="4efb1-189">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="4efb1-190">Importuje plik</span><span class="sxs-lookup"><span data-stu-id="4efb1-190">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="4efb1-191">Strona indeksu</span><span class="sxs-lookup"><span data-stu-id="4efb1-191">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="4efb1-192">Składnik aplikacji</span><span class="sxs-lookup"><span data-stu-id="4efb1-192">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="4efb1-193">Składnik RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="4efb1-193">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="4efb1-194">Składnik LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="4efb1-194">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="4efb1-195">Składnik uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="4efb1-195">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="4efb1-196">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="4efb1-196">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="4efb1-197">Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym</span><span class="sxs-lookup"><span data-stu-id="4efb1-197">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="4efb1-198">Samouczek: Tworzenie dzierżawy usługi Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="4efb1-198">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="4efb1-199">Dokumentacja poświęcona platformie tożsamości firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="4efb1-199">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
