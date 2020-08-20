---
title: Uwierzytelnianie użytkowników za pomocą usługi WS-Federation w ASP.NET Core
author: chlowell
description: W tym samouczku pokazano, jak używać usługi WS-Federation w aplikacji ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
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
uid: security/authentication/ws-federation
ms.openlocfilehash: 8a593efd799e900483d0337a06e02c3558b63bfb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634088"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="1444c-103">Uwierzytelnianie użytkowników za pomocą usługi WS-Federation w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1444c-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="1444c-104">W tym samouczku pokazano, jak umożliwić użytkownikom logowanie się przy użyciu dostawcy uwierzytelniania WS-Federation, takiego jak Active Directory Federation Services (ADFS) lub [Azure Active Directory](/azure/active-directory/) (AAD).</span><span class="sxs-lookup"><span data-stu-id="1444c-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="1444c-105">Używa ona przykładowej aplikacji ASP.NET Core opisanej w temacie [uwierzytelnianie w serwisach Facebook, Google i dostawcy zewnętrznym](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="1444c-105">It uses the ASP.NET Core sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="1444c-106">W przypadku aplikacji ASP.NET Core obsługa protokołu WS-Federation jest zapewniana przez [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span><span class="sxs-lookup"><span data-stu-id="1444c-106">For ASP.NET Core apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="1444c-107">Ten składnik jest przewoźny z [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) i udostępnia wiele elementów tego Mechanics.</span><span class="sxs-lookup"><span data-stu-id="1444c-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="1444c-108">Składniki te są jednak różne na kilka ważnych sposobów.</span><span class="sxs-lookup"><span data-stu-id="1444c-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="1444c-109">Domyślnie nowe oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="1444c-109">By default, the new middleware:</span></span>

* <span data-ttu-id="1444c-110">Nie zezwala na nieżądane nazwy logowania.</span><span class="sxs-lookup"><span data-stu-id="1444c-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="1444c-111">Ta funkcja protokołu WS-Federation jest narażona na ataki XSRF.</span><span class="sxs-lookup"><span data-stu-id="1444c-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="1444c-112">Można go jednak włączyć przy użyciu `AllowUnsolicitedLogins` opcji.</span><span class="sxs-lookup"><span data-stu-id="1444c-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="1444c-113">Nie sprawdza każdego wpisu w formularzu dla wiadomości logowania.</span><span class="sxs-lookup"><span data-stu-id="1444c-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="1444c-114">Tylko żądania do programu `CallbackPath` są sprawdzane pod kątem logowania. `CallbackPath` wartość domyślna to, `/signin-wsfed` ale można ją zmienić za pomocą dziedziczonej właściwości [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) .</span><span class="sxs-lookup"><span data-stu-id="1444c-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="1444c-115">Ta ścieżka może być współużytkowana z innymi dostawcami uwierzytelniania przez włączenie opcji [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .</span><span class="sxs-lookup"><span data-stu-id="1444c-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="1444c-116">Zarejestruj aplikację w Active Directory</span><span class="sxs-lookup"><span data-stu-id="1444c-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="1444c-117">Usługi Active Directory Federation Services</span><span class="sxs-lookup"><span data-stu-id="1444c-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="1444c-118">Otwórz **Kreatora dodawania zaufania jednostki uzależnionej** serwera z konsoli zarządzania usług AD FS:</span><span class="sxs-lookup"><span data-stu-id="1444c-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![Kreator dodawania zaufania jednostki uzależnionej: Witamy](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="1444c-120">Wybierz, aby ręcznie wprowadzić dane:</span><span class="sxs-lookup"><span data-stu-id="1444c-120">Choose to enter data manually:</span></span>

![Kreator dodawania zaufania jednostki uzależnionej: Wybieranie źródła danych](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="1444c-122">Wprowadź nazwę wyświetlaną jednostki uzależnionej.</span><span class="sxs-lookup"><span data-stu-id="1444c-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="1444c-123">Nazwa nie jest ważna dla aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1444c-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="1444c-124">[Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) nie obsługuje szyfrowania tokenów, dlatego nie należy konfigurować certyfikatu szyfrowania tokenu:</span><span class="sxs-lookup"><span data-stu-id="1444c-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![Kreator dodawania zaufania jednostki uzależnionej: Konfigurowanie certyfikatu](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="1444c-126">Włącz obsługę protokołu pasywnego usługi WS-Federation przy użyciu adresu URL aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1444c-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="1444c-127">Sprawdź, czy port jest prawidłowy dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="1444c-127">Verify the port is correct for the app:</span></span>

![Kreator dodawania zaufania jednostki uzależnionej: Konfigurowanie adresu URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="1444c-129">Musi to być adres URL HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1444c-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="1444c-130">IIS Express może podać certyfikat z podpisem własnym podczas udostępniania aplikacji podczas opracowywania.</span><span class="sxs-lookup"><span data-stu-id="1444c-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="1444c-131">Kestrel wymaga ręcznej konfiguracji certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="1444c-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="1444c-132">Aby uzyskać więcej informacji, zobacz [dokumentację Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="1444c-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="1444c-133">Kliknij przycisk **dalej** w pozostałej części kreatora i **Zamknij** na końcu.</span><span class="sxs-lookup"><span data-stu-id="1444c-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="1444c-134">ASP.NET Core Identity wymaga podania **identyfikatora nazwy** .</span><span class="sxs-lookup"><span data-stu-id="1444c-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="1444c-135">Dodaj jedną z okna dialogowego **Edytowanie reguł dotyczących roszczeń** :</span><span class="sxs-lookup"><span data-stu-id="1444c-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![Edytuj reguły dotyczące roszczeń](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="1444c-137">W **Kreatorze dodawania reguły przekształcania oświadczeń**pozostaw zaznaczone pole wyboru domyślne **Wysyłaj atrybuty LDAP as** , a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="1444c-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="1444c-138">Dodaj mapowanie reguły atrybut LDAP **nazwa konta sam** do żądania wychodzącego **Identyfikator nazwy** :</span><span class="sxs-lookup"><span data-stu-id="1444c-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![Kreator dodawania reguły przekształcania roszczeń: Konfigurowanie reguły dotyczącej roszczeń](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="1444c-140">Kliknij przycisk **Zakończ**  >  **OK** w oknie **Edytowanie reguł roszczeń** .</span><span class="sxs-lookup"><span data-stu-id="1444c-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="1444c-141">Usługa Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="1444c-141">Azure Active Directory</span></span>

* <span data-ttu-id="1444c-142">Przejdź do bloku rejestracje aplikacji dzierżawy usługi AAD.</span><span class="sxs-lookup"><span data-stu-id="1444c-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="1444c-143">Kliknij pozycję **rejestracja nowej aplikacji**:</span><span class="sxs-lookup"><span data-stu-id="1444c-143">Click **New application registration**:</span></span>

![Azure Active Directory: Rejestracje aplikacji](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="1444c-145">Wprowadź nazwę rejestracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1444c-145">Enter a name for the app registration.</span></span> <span data-ttu-id="1444c-146">Nie ma to znaczenia dla aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1444c-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="1444c-147">Wprowadź adres URL, na który aplikacja nasłuchuje jako **adres URL logowania**:</span><span class="sxs-lookup"><span data-stu-id="1444c-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory: Utwórz rejestrację aplikacji](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="1444c-149">Kliknij pozycję **punkty końcowe** i Zanotuj adres URL **dokumentu metadanych Federacji** .</span><span class="sxs-lookup"><span data-stu-id="1444c-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="1444c-150">To jest oprogramowanie pośredniczące WS-Federation `MetadataAddress` :</span><span class="sxs-lookup"><span data-stu-id="1444c-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: punkty końcowe](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="1444c-152">Przejdź do rejestracji nowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1444c-152">Navigate to the new app registration.</span></span> <span data-ttu-id="1444c-153">Kliknij pozycję **uwidaczniaj interfejs API**.</span><span class="sxs-lookup"><span data-stu-id="1444c-153">Click **Expose an API**.</span></span> <span data-ttu-id="1444c-154">Kliknij przycisk identyfikatora aplikacji identyfikator URI **Ustawienia**  >  **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="1444c-154">Click Application ID URI **Set** > **Save**.</span></span> <span data-ttu-id="1444c-155">Zanotuj  **Identyfikator URI aplikacji**.</span><span class="sxs-lookup"><span data-stu-id="1444c-155">Make note of the  **Application ID URI**.</span></span> <span data-ttu-id="1444c-156">To jest oprogramowanie pośredniczące WS-Federation `Wtrealm` :</span><span class="sxs-lookup"><span data-stu-id="1444c-156">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: właściwości rejestracji aplikacji](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-no-locaspnet-core-identity"></a><span data-ttu-id="1444c-158">Korzystanie z protokołu WS-Federation bez ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="1444c-158">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="1444c-159">Oprogramowanie pośredniczące WS-Federation może być używane bez programu Identity .</span><span class="sxs-lookup"><span data-stu-id="1444c-159">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="1444c-160">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="1444c-160">For example:</span></span>
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-no-locaspnet-core-identity"></a><span data-ttu-id="1444c-161">Dodaj usługę WS-Federation jako zewnętrznego dostawcę logowania dla ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="1444c-161">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="1444c-162">Dodaj zależność od elementu [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) do projektu.</span><span class="sxs-lookup"><span data-stu-id="1444c-162">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="1444c-163">Dodaj usługę WS-Federation do `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="1444c-163">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="1444c-164">Logowanie za pomocą usługi WS-Federation</span><span class="sxs-lookup"><span data-stu-id="1444c-164">Log in with WS-Federation</span></span>

<span data-ttu-id="1444c-165">Przejdź do aplikacji, a następnie kliknij link **Zaloguj** w nagłówku nawigacji.</span><span class="sxs-lookup"><span data-stu-id="1444c-165">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="1444c-166">Istnieje możliwość zalogowania się za pomocą WsFederation: ![ log na stronie](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="1444c-166">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="1444c-167">Za pomocą usług ADFS jako dostawca przycisk przekierowuje do strony logowania ADFS: ![ Strona logowania ADFS](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="1444c-167">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="1444c-168">Za pomocą Azure Active Directory jako dostawca przycisk przekierowuje do strony logowania do usługi AAD: ![ Strona logowania usługi AAD](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="1444c-168">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="1444c-169">Pomyślne logowanie do nowego użytkownika przekierowuje do strony rejestracji użytkownika aplikacji: ![ Strona rejestr](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="1444c-169">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>
