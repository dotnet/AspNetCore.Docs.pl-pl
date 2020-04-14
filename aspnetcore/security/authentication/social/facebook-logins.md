---
title: Konfiguracja zewnętrznego logowania facebooka w ASP.NET Core
author: rick-anderson
description: Samouczek z przykładami kodu przedstawiającymi integrację uwierzytelniania użytkowników konta na Facebooku z istniejącą aplikacją ASP.NET Core.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277304"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="6400a-103">Konfiguracja zewnętrznego logowania facebooka w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6400a-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="6400a-104">Autorstwa [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6400a-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="6400a-105">Ten samouczek z przykładami kodu pokazuje, jak umożliwić użytkownikom zalogowanie się za pomocą swojego konta na Facebooku przy użyciu przykładowego projektu ASP.NET core 3.0 utworzonego na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="6400a-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="6400a-106">Zaczynamy od utworzenia identyfikatora aplikacji Facebooka, wykonując [oficjalne kroki.](https://developers.facebook.com)</span><span class="sxs-lookup"><span data-stu-id="6400a-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="6400a-107">Tworzenie aplikacji na Facebooku</span><span class="sxs-lookup"><span data-stu-id="6400a-107">Create the app in Facebook</span></span>

* <span data-ttu-id="6400a-108">Dodaj pakiet [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="6400a-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="6400a-109">Przejdź do strony [aplikacji Facebook Developers](https://developers.facebook.com/apps/) i zaloguj się.</span><span class="sxs-lookup"><span data-stu-id="6400a-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="6400a-110">Jeśli nie masz jeszcze konta na Facebooku, użyj linku **Zarejestruj się na Facebooku** na stronie logowania, aby je utworzyć.</span><span class="sxs-lookup"><span data-stu-id="6400a-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="6400a-111">Gdy masz konto na Facebooku, postępuj zgodnie z instrukcjami, aby zarejestrować się jako programista Facebooka.</span><span class="sxs-lookup"><span data-stu-id="6400a-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="6400a-112">Z menu **Moje aplikacje** wybierz **pozycję Utwórz aplikację,** aby utworzyć nowy identyfikator aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6400a-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Facebook dla programistów portal otwarty w przeglądarce Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="6400a-114">Wypełnij formularz i naciśnij przycisk **Utwórz identyfikator aplikacji.**</span><span class="sxs-lookup"><span data-stu-id="6400a-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Tworzenie nowego formularza identyfikatora aplikacji](index/_static/FBNewAppId.png)

* <span data-ttu-id="6400a-116">Na nowej karcie aplikacji wybierz pozycję **Dodaj produkt**.</span><span class="sxs-lookup"><span data-stu-id="6400a-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="6400a-117">Na karcie **logowania na Facebooku** kliknij pozycję **Skonfiguruj**</span><span class="sxs-lookup"><span data-stu-id="6400a-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Strona Ustawienia produktu](index/_static/FBProductSetup.png)

* <span data-ttu-id="6400a-119">Kreator **szybkiego startu** uruchamia się z **wybierz platformę** jako pierwszą stronę.</span><span class="sxs-lookup"><span data-stu-id="6400a-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="6400a-120">Omiń kreatora, klikając link **Ustawienia** **logowania FaceBook** w menu w lewym dolnym dolnym roku:</span><span class="sxs-lookup"><span data-stu-id="6400a-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Pomiń szybki start](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="6400a-122">Przedstawiono Ci stronę **Ustawienia klienta OAuth:**</span><span class="sxs-lookup"><span data-stu-id="6400a-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Strona Ustawienia OAuth klienta](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="6400a-124">Wprowadź identyfikator URI rozwoju z */signin-facebook* dołączone do **prawidłowego OAuth Redirect URIs** pola (na przykład: `https://localhost:44320/signin-facebook`).</span><span class="sxs-lookup"><span data-stu-id="6400a-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="6400a-125">Uwierzytelnianie na Facebooku skonfigurowane w dalszej części tego samouczka będzie automatycznie obsługiwać żądania na trasie */signin-facebook* w celu zaimplementowania przepływu OAuth.</span><span class="sxs-lookup"><span data-stu-id="6400a-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="6400a-126">Identyfikator URI */signin-facebook* jest ustawiony jako domyślne wywołanie zwrotne dostawcy uwierzytelniania Facebooka.</span><span class="sxs-lookup"><span data-stu-id="6400a-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="6400a-127">Domyślny identyfikator URI wywołania zwrotnego można zmienić podczas konfigurowania oprogramowania pośredniczącego uwierzytelniania Facebooka za pośrednictwem dziedziczonej właściwości [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [FacebookOptions.](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)</span><span class="sxs-lookup"><span data-stu-id="6400a-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="6400a-128">Kliknij **pozycję Zapisz zmiany**.</span><span class="sxs-lookup"><span data-stu-id="6400a-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="6400a-129">Kliknij pozycję**Podstawowe** **łącze Ustawienia** > w lewej nawigacji.</span><span class="sxs-lookup"><span data-stu-id="6400a-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="6400a-130">Na tej stronie zanotuj `App ID` `App Secret`swój i swój .</span><span class="sxs-lookup"><span data-stu-id="6400a-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="6400a-131">W następnej sekcji dodasz zarówno aplikację ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6400a-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="6400a-132">Podczas wdrażania witryny należy ponownie odwiedzić stronę konfiguracji **logowania do Facebooka** i zarejestrować nowy publiczny identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="6400a-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="6400a-133">Przechowywanie identyfikatora aplikacji Facebook i klucza tajnego</span><span class="sxs-lookup"><span data-stu-id="6400a-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="6400a-134">Przechowuj poufne ustawienia, takie jak identyfikator aplikacji Facebook i tajne wartości za pomocą [Programu Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="6400a-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="6400a-135">W tym przykładzie należy wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="6400a-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="6400a-136">Inicjowanie projektu do przechowywania w celu uzyskania instrukcji w [usłudze Włącz magazyn tajny](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="6400a-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="6400a-137">Przechowuj poufne ustawienia w lokalnym magazynie tajnym z tajnymi kluczami `Authentication:Facebook:AppId` i: `Authentication:Facebook:AppSecret`</span><span class="sxs-lookup"><span data-stu-id="6400a-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="6400a-138">Konfigurowanie uwierzytelniania na Facebooku</span><span class="sxs-lookup"><span data-stu-id="6400a-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="6400a-139">Dodaj usługę Facebook `ConfigureServices` w metodzie w pliku *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="6400a-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="6400a-140">Zaloguj się za pomocą Facebooka</span><span class="sxs-lookup"><span data-stu-id="6400a-140">Sign in with Facebook</span></span>

* <span data-ttu-id="6400a-141">Uruchom aplikację i wybierz pozycję **Zaloguj się**.</span><span class="sxs-lookup"><span data-stu-id="6400a-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="6400a-142">W obszarze **Użyj innej usługi, aby się zalogować.** wybierz Pozycję Facebook.</span><span class="sxs-lookup"><span data-stu-id="6400a-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="6400a-143">Zostaniesz przekierowany do **Facebooka** w celu uwierzytelnienia.</span><span class="sxs-lookup"><span data-stu-id="6400a-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="6400a-144">Wprowadź swoje dane logowania do Facebooka.</span><span class="sxs-lookup"><span data-stu-id="6400a-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="6400a-145">Zostaniesz przekierowany z powrotem do witryny, gdzie możesz ustawić swój adres e-mail.</span><span class="sxs-lookup"><span data-stu-id="6400a-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="6400a-146">Jesteś teraz zalogowany przy użyciu swoich danych logowania na Facebooku:</span><span class="sxs-lookup"><span data-stu-id="6400a-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="6400a-147">Reagowanie na anulowanie autoryzacji zewnętrznego logowania</span><span class="sxs-lookup"><span data-stu-id="6400a-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="6400a-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>może podać ścieżkę przekierowania do agenta użytkownika, gdy użytkownik nie zatwierdzi żądanego żądania autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="6400a-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="6400a-149">Poniższy kod `AccessDeniedPath` ustawia `"/AccessDeniedPathInfo"`na:</span><span class="sxs-lookup"><span data-stu-id="6400a-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="6400a-150">Zalecamy, `AccessDeniedPath` aby strona zawierała następujące informacje:</span><span class="sxs-lookup"><span data-stu-id="6400a-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="6400a-151">Uwierzytelnianie zdalne zostało anulowane.</span><span class="sxs-lookup"><span data-stu-id="6400a-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="6400a-152">Ta aplikacja wymaga uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="6400a-152">This app requires authentication.</span></span>
* <span data-ttu-id="6400a-153">Aby ponownie spróbować zalogować się, wybierz łącze Zaloguj.</span><span class="sxs-lookup"><span data-stu-id="6400a-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="6400a-154">Test AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="6400a-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="6400a-155">Przejdź do [facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="6400a-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="6400a-156">Jeśli jesteś zalogowany, musisz się wylogować.</span><span class="sxs-lookup"><span data-stu-id="6400a-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="6400a-157">Uruchom aplikację i wybierz opcję Logowania na Facebooku.</span><span class="sxs-lookup"><span data-stu-id="6400a-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="6400a-158">Wybierz **opcję Nie teraz**.</span><span class="sxs-lookup"><span data-stu-id="6400a-158">Select **Not now**.</span></span> <span data-ttu-id="6400a-159">Zostaniesz przekierowany do `AccessDeniedPath` określonej strony.</span><span class="sxs-lookup"><span data-stu-id="6400a-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="6400a-160">Więcej informacji na temat opcji konfiguracji obsługiwanych przez uwierzytelnianie na Facebooku można znaleźć w aplikacji [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API.</span><span class="sxs-lookup"><span data-stu-id="6400a-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="6400a-161">Opcji konfiguracji można użyć do:</span><span class="sxs-lookup"><span data-stu-id="6400a-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="6400a-162">Żądaj różnych informacji o użytkowniku.</span><span class="sxs-lookup"><span data-stu-id="6400a-162">Request different information about the user.</span></span>
* <span data-ttu-id="6400a-163">Dodaj argumenty ciągu kwerendy, aby dostosować środowisko logowania.</span><span class="sxs-lookup"><span data-stu-id="6400a-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="6400a-164">Rozwiązywanie problemów</span><span class="sxs-lookup"><span data-stu-id="6400a-164">Troubleshooting</span></span>

* <span data-ttu-id="6400a-165">**ASP.NET tylko Core 2.x:** Jeśli tożsamość nie jest `services.AddIdentity` skonfigurowana przez `ConfigureServices`wywołanie, próba uwierzytelnienia *spowoduje, że argumentexception: musi zostać podana opcja "SignInScheme".*</span><span class="sxs-lookup"><span data-stu-id="6400a-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="6400a-166">Szablon projektu użyty w tym samouczku zapewnia, że jest to zrobione.</span><span class="sxs-lookup"><span data-stu-id="6400a-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="6400a-167">Jeśli baza danych lokacji nie została utworzona przez zastosowanie migracji początkowej, podczas przetwarzania błędu *żądania zostanie ściągnięta operacja bazy danych.*</span><span class="sxs-lookup"><span data-stu-id="6400a-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="6400a-168">Naciśnij pozycję **Zastosuj migracje,** aby utworzyć bazę danych i odświeżyć, aby przejść obok błędu.</span><span class="sxs-lookup"><span data-stu-id="6400a-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6400a-169">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="6400a-169">Next steps</span></span>

* <span data-ttu-id="6400a-170">W tym artykule pokazano, jak można uwierzytelniać się za pomocą Facebooka.</span><span class="sxs-lookup"><span data-stu-id="6400a-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="6400a-171">Podobne podejście można stosować w celu uwierzytelnienia z innymi dostawcami wymienionymi na [poprzedniej stronie](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="6400a-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="6400a-172">Po opublikowaniu witryny sieci Web w aplikacji `AppSecret` sieci Web platformy Azure należy zresetować witrynę w portalu deweloperów Facebooka.</span><span class="sxs-lookup"><span data-stu-id="6400a-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="6400a-173">Ustaw `Authentication:Facebook:AppId` ustawienia `Authentication:Facebook:AppSecret` aplikacji i jako w witrynie Azure portal.</span><span class="sxs-lookup"><span data-stu-id="6400a-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="6400a-174">System konfiguracji jest skonfigurowany do odczytu kluczy ze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="6400a-174">The configuration system is set up to read keys from environment variables.</span></span>
