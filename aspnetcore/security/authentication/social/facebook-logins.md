---
title: Konfiguracja logowania zewnętrznego w serwisie Facebook w ASP.NET Core
author: rick-anderson
description: Samouczek z przykładami kodu demonstrujących integrację uwierzytelniania użytkownika konta w serwisie Facebook w istniejącej aplikacji ASP.NET Core.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/facebook-logins
ms.openlocfilehash: df91b6f324de70b8492ccf0aef74c9264c3e9711
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403953"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="1b29d-103">Konfiguracja logowania zewnętrznego w serwisie Facebook w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b29d-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="1b29d-104">Autorzy [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1b29d-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="1b29d-105">Ten samouczek z przykładami kodu pokazuje, jak umożliwić użytkownikom zalogowanie się za pomocą konta w serwisie Facebook przy użyciu przykładowego projektu ASP.NET Core 3,0 utworzonego na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="1b29d-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="1b29d-106">Zacznij od utworzenia identyfikatora aplikacji Facebook, wykonując [czynności urzędowe](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="1b29d-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="1b29d-107">Tworzenie aplikacji w usłudze Facebook</span><span class="sxs-lookup"><span data-stu-id="1b29d-107">Create the app in Facebook</span></span>

* <span data-ttu-id="1b29d-108">Dodaj pakiet NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) do projektu.</span><span class="sxs-lookup"><span data-stu-id="1b29d-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="1b29d-109">Przejdź do strony [aplikacji deweloperzy serwisu Facebook](https://developers.facebook.com/apps/) i zaloguj się.</span><span class="sxs-lookup"><span data-stu-id="1b29d-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="1b29d-110">Jeśli nie masz jeszcze konta w serwisie Facebook, utwórz je za pomocą linku rejestracji w usłudze **Facebook** na stronie logowania.</span><span class="sxs-lookup"><span data-stu-id="1b29d-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="1b29d-111">Gdy masz konto w serwisie Facebook, postępuj zgodnie z instrukcjami, aby zarejestrować się jako deweloper w serwisie Facebook.</span><span class="sxs-lookup"><span data-stu-id="1b29d-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="1b29d-112">Z menu **Moje aplikacje** wybierz pozycję **Utwórz aplikację** , aby utworzyć nowy identyfikator aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1b29d-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Portal usługi Facebook dla deweloperów otwarty w przeglądarce Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="1b29d-114">Wypełnij formularz i naciśnij przycisk **Utwórz identyfikator aplikacji** .</span><span class="sxs-lookup"><span data-stu-id="1b29d-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Utwórz nowy formularz identyfikatora aplikacji](index/_static/FBNewAppId.png)

* <span data-ttu-id="1b29d-116">Na nowej karcie aplikacji wybierz pozycję **Dodaj produkt**.</span><span class="sxs-lookup"><span data-stu-id="1b29d-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="1b29d-117">Na karcie **Logowanie w serwisie Facebook** kliknij pozycję **Konfiguruj** .</span><span class="sxs-lookup"><span data-stu-id="1b29d-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Strona konfiguracji produktu](index/_static/FBProductSetup.png)

* <span data-ttu-id="1b29d-119">Zostanie uruchomiony Kreator **szybkiego startu** z **wybieraniem platformy** jako pierwszej strony.</span><span class="sxs-lookup"><span data-stu-id="1b29d-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="1b29d-120">Pomiń kreatora teraz, klikając link **Ustawienia** **logowania w serwisie Facebook** w menu po lewej stronie:</span><span class="sxs-lookup"><span data-stu-id="1b29d-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Pomiń Szybki start](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="1b29d-122">Zostanie wyświetlona strona **ustawień uwierzytelniania OAuth klienta** :</span><span class="sxs-lookup"><span data-stu-id="1b29d-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Strona ustawień uwierzytelniania OAuth klienta](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="1b29d-124">Wprowadź identyfikator URI programowania z */SignIn-facebooką* dołączoną do **prawidłowego pola URI przekierowania OAuth** (na przykład: `https://localhost:44320/signin-facebook` ).</span><span class="sxs-lookup"><span data-stu-id="1b29d-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="1b29d-125">Uwierzytelnianie w serwisie Facebook skonfigurowane w dalszej części tego samouczka będzie automatycznie obsługiwać żądania w marszrucie */SignIn-Facebook* w celu zaimplementowania przepływu OAuth.</span><span class="sxs-lookup"><span data-stu-id="1b29d-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="1b29d-126">Identyfikator URI */SignIn-Facebook* jest ustawiany jako domyślne wywołanie zwrotne dostawcy uwierzytelniania w serwisie Facebook.</span><span class="sxs-lookup"><span data-stu-id="1b29d-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="1b29d-127">Domyślny identyfikator URI wywołania zwrotnego można zmienić podczas konfigurowania oprogramowania pośredniczącego uwierzytelniania w serwisie Facebook za pośrednictwem dziedziczonej właściwości [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .</span><span class="sxs-lookup"><span data-stu-id="1b29d-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="1b29d-128">Kliknij przycisk **Zapisz zmiany**.</span><span class="sxs-lookup"><span data-stu-id="1b29d-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="1b29d-129">Kliknij link **Ustawienia**  >  **podstawowe** na lewym pasku nawigacyjnym.</span><span class="sxs-lookup"><span data-stu-id="1b29d-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="1b29d-130">Na tej stronie Zanotuj swoje `App ID` i Twoje `App Secret` .</span><span class="sxs-lookup"><span data-stu-id="1b29d-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="1b29d-131">Do aplikacji ASP.NET Core należy dodać w następnej sekcji:</span><span class="sxs-lookup"><span data-stu-id="1b29d-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="1b29d-132">Podczas wdrażania witryny należy ponownie odwiedzić stronę instalatora logowania do **serwisu Facebook** i zarejestrować nowy publiczny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="1b29d-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="1b29d-133">Przechowywanie identyfikatora i wpisu tajnego aplikacji w serwisie Facebook</span><span class="sxs-lookup"><span data-stu-id="1b29d-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="1b29d-134">Przechowuj ustawienia poufne, takie jak identyfikator aplikacji w serwisie Facebook i wartości tajne przy użyciu [Menedżera wpisów tajnych](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="1b29d-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="1b29d-135">W tym przykładzie wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="1b29d-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="1b29d-136">Zainicjuj projekt dla magazynu wpisów tajnych zgodnie z instrukcjami w obszarze [Włączanie magazynu tajnego](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="1b29d-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="1b29d-137">Zapisz poufne ustawienia w lokalnym magazynie wpisów tajnych przy użyciu kluczy tajnych `Authentication:Facebook:AppId` i `Authentication:Facebook:AppSecret` :</span><span class="sxs-lookup"><span data-stu-id="1b29d-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="1b29d-138">Konfigurowanie uwierzytelniania w usłudze Facebook</span><span class="sxs-lookup"><span data-stu-id="1b29d-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="1b29d-139">Dodaj usługę Facebook do `ConfigureServices` metody w pliku *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="1b29d-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="1b29d-140">Logowanie za pomocą usługi Facebook</span><span class="sxs-lookup"><span data-stu-id="1b29d-140">Sign in with Facebook</span></span>

* <span data-ttu-id="1b29d-141">Uruchom aplikację i wybierz pozycję **Zaloguj się**.</span><span class="sxs-lookup"><span data-stu-id="1b29d-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="1b29d-142">W obszarze **Użyj innej usługi do zalogowania się.** wybierz pozycję Facebook.</span><span class="sxs-lookup"><span data-stu-id="1b29d-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="1b29d-143">Nastąpi przekierowanie do usługi **Facebook** w celu uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="1b29d-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="1b29d-144">Wprowadź swoje poświadczenia w serwisie Facebook.</span><span class="sxs-lookup"><span data-stu-id="1b29d-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="1b29d-145">Nastąpi przekierowanie z powrotem do witryny, w której można ustawić swój adres e-mail.</span><span class="sxs-lookup"><span data-stu-id="1b29d-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="1b29d-146">Zalogowano Cię przy użyciu poświadczeń usługi Facebook:</span><span class="sxs-lookup"><span data-stu-id="1b29d-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="1b29d-147">Reagowanie na Anulowanie autoryzacji logowania zewnętrznego</span><span class="sxs-lookup"><span data-stu-id="1b29d-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="1b29d-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>Jeśli użytkownik nie zatwierdza żądanego zapotrzebowania na autoryzację, może podać ścieżkę przekierowania do agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="1b29d-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="1b29d-149">Poniższy kod ustawia `AccessDeniedPath` `"/AccessDeniedPathInfo"` :</span><span class="sxs-lookup"><span data-stu-id="1b29d-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="1b29d-150">Zalecamy, aby `AccessDeniedPath` strona zawierała następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="1b29d-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="1b29d-151">Zdalne uwierzytelnianie zostało anulowane.</span><span class="sxs-lookup"><span data-stu-id="1b29d-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="1b29d-152">Ta aplikacja wymaga uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="1b29d-152">This app requires authentication.</span></span>
* <span data-ttu-id="1b29d-153">Aby ponownie spróbować zalogować się, wybierz link logowania.</span><span class="sxs-lookup"><span data-stu-id="1b29d-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="1b29d-154">Test AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="1b29d-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="1b29d-155">Przejdź do [Facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="1b29d-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="1b29d-156">Jeśli użytkownik jest zalogowany, musisz się wylogować.</span><span class="sxs-lookup"><span data-stu-id="1b29d-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="1b29d-157">Uruchom aplikację i wybierz pozycję Logowanie do serwisu Facebook.</span><span class="sxs-lookup"><span data-stu-id="1b29d-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="1b29d-158">Wybierz pozycję **nie teraz**.</span><span class="sxs-lookup"><span data-stu-id="1b29d-158">Select **Not now**.</span></span> <span data-ttu-id="1b29d-159">Nastąpi przekierowanie do określonej `AccessDeniedPath` strony.</span><span class="sxs-lookup"><span data-stu-id="1b29d-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="1b29d-160">Zobacz Dokumentacja interfejsu API [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) , aby uzyskać więcej informacji na temat opcji konfiguracji obsługiwanych przez uwierzytelnianie w serwisie Facebook.</span><span class="sxs-lookup"><span data-stu-id="1b29d-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="1b29d-161">Opcje konfiguracji mogą służyć do:</span><span class="sxs-lookup"><span data-stu-id="1b29d-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="1b29d-162">Zażądaj innych informacji o użytkowniku.</span><span class="sxs-lookup"><span data-stu-id="1b29d-162">Request different information about the user.</span></span>
* <span data-ttu-id="1b29d-163">Dodaj argumenty ciągu zapytania, aby dostosować środowisko logowania.</span><span class="sxs-lookup"><span data-stu-id="1b29d-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1b29d-164">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="1b29d-164">Troubleshooting</span></span>

* <span data-ttu-id="1b29d-165">**Tylko ASP.NET Core 2. x:** Jeśli Identity nie jest skonfigurowany przez wywołanie `services.AddIdentity` w `ConfigureServices` , próba uwierzytelnienia spowoduje powstanie *argumentu ArgumentException: należy podać opcję "SignInScheme"*.</span><span class="sxs-lookup"><span data-stu-id="1b29d-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="1b29d-166">Szablon projektu używany w tym samouczku zapewnia, że jest to gotowe.</span><span class="sxs-lookup"><span data-stu-id="1b29d-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="1b29d-167">Jeśli baza danych lokacji nie została utworzona przez zastosowanie początkowej migracji, podczas *przetwarzania błędu żądania nie można wykonać operacji bazy danych* .</span><span class="sxs-lookup"><span data-stu-id="1b29d-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="1b29d-168">Naciśnij pozycję **Zastosuj migracje** , aby utworzyć bazę danych i odświeżyć, aby kontynuować z powodu błędu.</span><span class="sxs-lookup"><span data-stu-id="1b29d-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1b29d-169">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="1b29d-169">Next steps</span></span>

* <span data-ttu-id="1b29d-170">W tym artykule pokazano, jak można uwierzytelnić się w usłudze Facebook.</span><span class="sxs-lookup"><span data-stu-id="1b29d-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="1b29d-171">Podobne podejście można wykonać w celu uwierzytelnienia z innymi dostawcami wymienionymi na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="1b29d-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="1b29d-172">Po opublikowaniu witryny sieci Web w usłudze Azure Web App należy zresetować ją `AppSecret` w portalu dla deweloperów serwisu Facebook.</span><span class="sxs-lookup"><span data-stu-id="1b29d-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="1b29d-173">Ustaw `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` Ustawienia aplikacji i na Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="1b29d-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="1b29d-174">System konfiguracji jest skonfigurowany do odczytywania kluczy ze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="1b29d-174">The configuration system is set up to read keys from environment variables.</span></span>
