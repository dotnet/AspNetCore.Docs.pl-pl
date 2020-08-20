---
title: Potwierdzenie konta i odzyskiwanie hasła w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak utworzyć aplikację ASP.NET Core przy użyciu potwierdzenia wiadomości e-mail i resetowania hasła.
ms.author: riande
ms.date: 03/11/2019
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
uid: security/authentication/accconfirm
ms.openlocfilehash: 57607390e7d5e58df9f27437faecd57504ad64df
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635375"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="cb52c-103">Potwierdzenie konta i odzyskiwanie hasła w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb52c-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="cb52c-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)i Jan [Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="cb52c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="cb52c-105">W tym samouczku pokazano, jak utworzyć aplikację ASP.NET Core przy użyciu potwierdzenia wiadomości e-mail i resetowania hasła.</span><span class="sxs-lookup"><span data-stu-id="cb52c-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="cb52c-106">Ten samouczek **nie** jest tematem początkowym.</span><span class="sxs-lookup"><span data-stu-id="cb52c-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="cb52c-107">Należy zapoznać się z:</span><span class="sxs-lookup"><span data-stu-id="cb52c-107">You should be familiar with:</span></span>

* [<span data-ttu-id="cb52c-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb52c-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="cb52c-109">Authentication</span><span class="sxs-lookup"><span data-stu-id="cb52c-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="cb52c-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="cb52c-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="cb52c-111">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="cb52c-111">Prerequisites</span></span>

[<span data-ttu-id="cb52c-112">.NET Core 3,0 SDK lub nowszy</span><span class="sxs-lookup"><span data-stu-id="cb52c-112">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="cb52c-113">Tworzenie i testowanie aplikacji sieci Web przy użyciu uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="cb52c-113">Create and test a web app with authentication</span></span>

<span data-ttu-id="cb52c-114">Uruchom następujące polecenia, aby utworzyć aplikację sieci Web z uwierzytelnianiem.</span><span class="sxs-lookup"><span data-stu-id="cb52c-114">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="cb52c-115">Uruchom aplikację, wybierz łącze **zarejestruj** i zarejestruj użytkownika.</span><span class="sxs-lookup"><span data-stu-id="cb52c-115">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="cb52c-116">Po zarejestrowaniu nastąpi przekierowanie na `/Identity/Account/RegisterConfirmation` stronę do, która zawiera link umożliwiający zasymulowanie potwierdzenia wiadomości e-mail:</span><span class="sxs-lookup"><span data-stu-id="cb52c-116">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="cb52c-117">Wybierz link `Click here to confirm your account`.</span><span class="sxs-lookup"><span data-stu-id="cb52c-117">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="cb52c-118">Wybierz łącze **logowania** i zaloguj się przy użyciu tych samych poświadczeń.</span><span class="sxs-lookup"><span data-stu-id="cb52c-118">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="cb52c-119">Wybierz `Hello YourEmail@provider.com!` link, który przekierowuje Cię do `/Identity/Account/Manage/PersonalData` strony.</span><span class="sxs-lookup"><span data-stu-id="cb52c-119">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="cb52c-120">Wybierz kartę **dane osobowe** po lewej stronie, a następnie wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="cb52c-120">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="cb52c-121">Konfigurowanie dostawcy poczty e-mail</span><span class="sxs-lookup"><span data-stu-id="cb52c-121">Configure an email provider</span></span>

<span data-ttu-id="cb52c-122">W tym samouczku [SendGrid](https://sendgrid.com) jest używany do wysyłania wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-122">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="cb52c-123">Aby wysłać wiadomość e-mail, musisz mieć konto SendGrid i klucz.</span><span class="sxs-lookup"><span data-stu-id="cb52c-123">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="cb52c-124">Możesz użyć innych dostawców poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-124">You can use other email providers.</span></span> <span data-ttu-id="cb52c-125">Zalecamy użycie SendGrid lub innej usługi poczty e-mail do wysyłania wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-125">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="cb52c-126">Protokół SMTP jest trudny do poprawnego zabezpieczania i konfigurowania.</span><span class="sxs-lookup"><span data-stu-id="cb52c-126">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="cb52c-127">Konto SendGrid może wymagać [dodania nadawcy](https://sendgrid.com/docs/ui/sending-email/senders/).</span><span class="sxs-lookup"><span data-stu-id="cb52c-127">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="cb52c-128">Utwórz klasę, aby pobrać bezpieczny klucz poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="cb52c-129">Dla tego przykładu Utwórz *usługi/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="cb52c-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="cb52c-130">Konfigurowanie kluczy tajnych użytkownika SendGrid</span><span class="sxs-lookup"><span data-stu-id="cb52c-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="cb52c-131">Ustaw `SendGridUser` i `SendGridKey` za pomocą [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="cb52c-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="cb52c-132">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb52c-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="cb52c-133">W systemie Windows Menedżer wpisów tajnych przechowuje pary klucz/wartość w *secrets.js* w pliku w `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="cb52c-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="cb52c-134">Zawartość *secrets.jsw* pliku nie jest zaszyfrowana.</span><span class="sxs-lookup"><span data-stu-id="cb52c-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="cb52c-135">Poniższe znaczniki przedstawiają *secrets.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="cb52c-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="cb52c-136">`SendGridKey`Wartość została usunięta.</span><span class="sxs-lookup"><span data-stu-id="cb52c-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="cb52c-137">Aby uzyskać więcej informacji, zobacz [Opcje wzorca](xref:fundamentals/configuration/options) i [konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="cb52c-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="cb52c-138">Zainstaluj SendGrid</span><span class="sxs-lookup"><span data-stu-id="cb52c-138">Install SendGrid</span></span>

<span data-ttu-id="cb52c-139">W tym samouczku pokazano, jak dodać powiadomienia e-mail za pośrednictwem usługi [SendGrid](https://sendgrid.com/), ale można wysyłać wiadomości e-mail przy użyciu protokołu SMTP i innych mechanizmów.</span><span class="sxs-lookup"><span data-stu-id="cb52c-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="cb52c-140">Zainstaluj `SendGrid` pakiet NuGet:</span><span class="sxs-lookup"><span data-stu-id="cb52c-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb52c-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb52c-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cb52c-142">W konsoli Menedżera pakietów wprowadź następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="cb52c-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="cb52c-143">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="cb52c-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cb52c-144">W konsoli programu wprowadź następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="cb52c-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="cb52c-145">Zobacz artykuł Wprowadzenie do usługi [SendGrid bezpłatnie](https://sendgrid.com/free/) , aby zarejestrować się w celu uzyskania bezpłatnego konta SendGrid.</span><span class="sxs-lookup"><span data-stu-id="cb52c-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="cb52c-146">Implementuj IEmailSender</span><span class="sxs-lookup"><span data-stu-id="cb52c-146">Implement IEmailSender</span></span>

<span data-ttu-id="cb52c-147">Aby zaimplementować `IEmailSender` program, Utwórz *usługi/EmailSender. cs* z kodem podobnym do poniższego:</span><span class="sxs-lookup"><span data-stu-id="cb52c-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="cb52c-148">Konfigurowanie uruchamiania do obsługi poczty e-mail</span><span class="sxs-lookup"><span data-stu-id="cb52c-148">Configure startup to support email</span></span>

<span data-ttu-id="cb52c-149">Dodaj następujący kod do `ConfigureServices` metody w pliku *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="cb52c-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="cb52c-150">Dodaj `EmailSender` jako usługę przejściową.</span><span class="sxs-lookup"><span data-stu-id="cb52c-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="cb52c-151">Zarejestruj `AuthMessageSenderOptions` wystąpienie konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="cb52c-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a><span data-ttu-id="cb52c-152">RegisterConfirmation szkieletu</span><span class="sxs-lookup"><span data-stu-id="cb52c-152">Scaffold RegisterConfirmation</span></span>

<span data-ttu-id="cb52c-153">Postępuj zgodnie z instrukcjami dla [szkieletu Identity ](xref:security/authentication/scaffold-identity) i szkieletu `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="cb52c-153">Follow the instructions for [Scaffold Identity](xref:security/authentication/scaffold-identity) and scaffold `RegisterConfirmation`.</span></span>

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="cb52c-154">Rejestrowanie, Potwierdzanie poczty e-mail i resetowanie hasła</span><span class="sxs-lookup"><span data-stu-id="cb52c-154">Register, confirm email, and reset password</span></span>

<span data-ttu-id="cb52c-155">Uruchom aplikację internetową, a następnie przetestuj potwierdzenie konta i przepływ odzyskiwania hasła.</span><span class="sxs-lookup"><span data-stu-id="cb52c-155">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="cb52c-156">Uruchom aplikację i Zarejestruj nowego użytkownika</span><span class="sxs-lookup"><span data-stu-id="cb52c-156">Run the app and register a new user</span></span>
* <span data-ttu-id="cb52c-157">Sprawdź wiadomość e-mail z linkiem potwierdzenia konta.</span><span class="sxs-lookup"><span data-stu-id="cb52c-157">Check your email for the account confirmation link.</span></span> <span data-ttu-id="cb52c-158">Jeśli nie otrzymasz wiadomości e-mail, zobacz [debugowanie poczty e-mail](#debug) .</span><span class="sxs-lookup"><span data-stu-id="cb52c-158">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="cb52c-159">Kliknij link, aby potwierdzić swój adres e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-159">Click the link to confirm your email.</span></span>
* <span data-ttu-id="cb52c-160">Zaloguj się przy użyciu adresu e-mail i hasła.</span><span class="sxs-lookup"><span data-stu-id="cb52c-160">Sign in with your email and password.</span></span>
* <span data-ttu-id="cb52c-161">Wyloguj się.</span><span class="sxs-lookup"><span data-stu-id="cb52c-161">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="cb52c-162">Resetowanie hasła do testu</span><span class="sxs-lookup"><span data-stu-id="cb52c-162">Test password reset</span></span>

* <span data-ttu-id="cb52c-163">Jeśli użytkownik jest zalogowany, wybierz pozycję **Wyloguj**.</span><span class="sxs-lookup"><span data-stu-id="cb52c-163">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="cb52c-164">Wybierz link **Zaloguj** i wybierz łącze nie **pamiętasz hasła?**</span><span class="sxs-lookup"><span data-stu-id="cb52c-164">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="cb52c-165">Wprowadź adres e-mail, który został użyty do zarejestrowania konta.</span><span class="sxs-lookup"><span data-stu-id="cb52c-165">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="cb52c-166">Zostanie wysłana wiadomość e-mail z linkiem służącym do resetowania hasła.</span><span class="sxs-lookup"><span data-stu-id="cb52c-166">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="cb52c-167">Sprawdź swój adres e-mail i kliknij link, aby zresetować hasło.</span><span class="sxs-lookup"><span data-stu-id="cb52c-167">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="cb52c-168">Po pomyślnym zresetowaniu hasła możesz zalogować się przy użyciu adresu e-mail i nowego hasła.</span><span class="sxs-lookup"><span data-stu-id="cb52c-168">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="cb52c-169">Zmień limit czasu wiadomości e-mail i aktywności</span><span class="sxs-lookup"><span data-stu-id="cb52c-169">Change email and activity timeout</span></span>

<span data-ttu-id="cb52c-170">Domyślny limit czasu braku aktywności wynosi 14 dni.</span><span class="sxs-lookup"><span data-stu-id="cb52c-170">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="cb52c-171">Następujący kod ustawia limit czasu braku aktywności na 5 dni:</span><span class="sxs-lookup"><span data-stu-id="cb52c-171">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="cb52c-172">Zmień wszystkie lifespans tokenów ochrony danych</span><span class="sxs-lookup"><span data-stu-id="cb52c-172">Change all data protection token lifespans</span></span>

<span data-ttu-id="cb52c-173">Następujący kod zmienia limit czasu tokenów ochrony danych na 3 godziny:</span><span class="sxs-lookup"><span data-stu-id="cb52c-173">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="cb52c-174">Wbudowane Identity tokeny użytkownika (zobacz [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mają [jeden dzień limitu czasu](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="cb52c-174">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="cb52c-175">Zmień cykl życia tokenu poczty e-mail</span><span class="sxs-lookup"><span data-stu-id="cb52c-175">Change the email token lifespan</span></span>

<span data-ttu-id="cb52c-176">Domyślny token cykl życia tokenów [ Identity użytkownika](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) to [jeden dzień](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="cb52c-176">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="cb52c-177">W tej sekcji przedstawiono sposób zmiany cykl życia tokenu poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-177">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="cb52c-178">Dodaj niestandardową [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) i <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="cb52c-178">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="cb52c-179">Dodaj dostawcę niestandardowego do kontenera usługi:</span><span class="sxs-lookup"><span data-stu-id="cb52c-179">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="cb52c-180">Potwierdzenie ponownego wysłania wiadomości e-mail</span><span class="sxs-lookup"><span data-stu-id="cb52c-180">Resend email confirmation</span></span>

<span data-ttu-id="cb52c-181">Zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/5410)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="cb52c-181">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="cb52c-182">Debuguj wiadomość e-mail</span><span class="sxs-lookup"><span data-stu-id="cb52c-182">Debug email</span></span>

<span data-ttu-id="cb52c-183">Jeśli nie możesz uzyskać pracy z wiadomościami e-mail:</span><span class="sxs-lookup"><span data-stu-id="cb52c-183">If you can't get email working:</span></span>

* <span data-ttu-id="cb52c-184">Ustaw punkt przerwania w programie `EmailSender.Execute` , aby sprawdzić, czy `SendGridClient.SendEmailAsync` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="cb52c-184">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="cb52c-185">Utwórz [aplikację konsolową do wysyłania wiadomości e-mail](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) przy użyciu podobnego kodu do programu `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="cb52c-185">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="cb52c-186">Przejrzyj stronę [działania e-mail](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="cb52c-186">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="cb52c-187">Sprawdź swój folder spamu.</span><span class="sxs-lookup"><span data-stu-id="cb52c-187">Check your spam folder.</span></span>
* <span data-ttu-id="cb52c-188">Wypróbuj inny alias poczty e-mail na innym dostawcy poczty e-mail (Microsoft, Yahoo, Gmail itp.)</span><span class="sxs-lookup"><span data-stu-id="cb52c-188">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="cb52c-189">Spróbuj wysłać konto do innych kont e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-189">Try sending to different email accounts.</span></span>

<span data-ttu-id="cb52c-190">**Najlepszym rozwiązaniem** w zakresie zabezpieczeń jest **nieużywanie produkcyjnych** wpisów tajnych w testowaniu i programowaniu.</span><span class="sxs-lookup"><span data-stu-id="cb52c-190">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="cb52c-191">Jeśli opublikujesz aplikację na platformie Azure, ustaw wpisy tajne SendGrid jako ustawienia aplikacji w portalu aplikacji sieci Web platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb52c-191">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="cb52c-192">System konfiguracji jest skonfigurowany do odczytywania kluczy ze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="cb52c-192">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="cb52c-193">Łączenie kont logowania społecznościowych i lokalnych</span><span class="sxs-lookup"><span data-stu-id="cb52c-193">Combine social and local login accounts</span></span>

<span data-ttu-id="cb52c-194">Aby ukończyć tę sekcję, należy najpierw włączyć zewnętrznego dostawcę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="cb52c-194">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="cb52c-195">Zobacz [uwierzytelnianie w usługach Facebook, Google i dostawcy zewnętrznym](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="cb52c-195">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="cb52c-196">Konta lokalne i społecznościowe można łączyć przez kliknięcie linku do poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-196">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="cb52c-197">W następującej kolejności " RickAndMSFT@gmail.com " jest najpierw tworzony jako logowanie lokalne; można jednak najpierw utworzyć konto jako nazwę logowania w sieci społecznościowej, a następnie dodać lokalną nazwę logowania.</span><span class="sxs-lookup"><span data-stu-id="cb52c-197">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplikacja sieci Web: RickAndMSFT@gmail.com użytkownik uwierzytelniony](accconfirm/_static/rick.png)

<span data-ttu-id="cb52c-199">Kliknij link **Zarządzaj** .</span><span class="sxs-lookup"><span data-stu-id="cb52c-199">Click on the **Manage** link.</span></span> <span data-ttu-id="cb52c-200">Zwróć uwagę na wartość 0 External (logowanie społeczne) skojarzoną z tym kontem.</span><span class="sxs-lookup"><span data-stu-id="cb52c-200">Note the 0 external (social logins) associated with this account.</span></span>

![Zarządzaj widokiem](accconfirm/_static/manage.png)

<span data-ttu-id="cb52c-202">Kliknij link do innej usługi logowania i zaakceptuj żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cb52c-202">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="cb52c-203">Na poniższym obrazie serwis Facebook jest dostawcą zewnętrznym uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="cb52c-203">In the following image, Facebook is the external authentication provider:</span></span>

![Zarządzanie widokiem logowania zewnętrznego w serwisie Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="cb52c-205">Te dwa konta zostały połączone.</span><span class="sxs-lookup"><span data-stu-id="cb52c-205">The two accounts have been combined.</span></span> <span data-ttu-id="cb52c-206">Możesz zalogować się przy użyciu dowolnego konta.</span><span class="sxs-lookup"><span data-stu-id="cb52c-206">You are able to sign in with either account.</span></span> <span data-ttu-id="cb52c-207">Możesz chcieć, aby użytkownicy mogli dodawać konta lokalne w przypadku, gdy usługa uwierzytelniania przy logowaniu w sieci społecznościowej nie działa, lub prawdopodobnie utraciła dostęp do konta społecznościowego.</span><span class="sxs-lookup"><span data-stu-id="cb52c-207">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="cb52c-208">Włącz potwierdzenie konta po stronie, w której znajdują się użytkownicy</span><span class="sxs-lookup"><span data-stu-id="cb52c-208">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="cb52c-209">Włączenie potwierdzenia konta w witrynie z użytkownikami powoduje zablokowanie wszystkich istniejących użytkowników.</span><span class="sxs-lookup"><span data-stu-id="cb52c-209">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="cb52c-210">Istniejący użytkownicy są Zablokowani, ponieważ ich konta nie zostały potwierdzone.</span><span class="sxs-lookup"><span data-stu-id="cb52c-210">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="cb52c-211">Aby obejść istniejącą blokadę użytkownika, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="cb52c-211">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="cb52c-212">Zaktualizuj bazę danych, aby oznaczyć wszystkich istniejących użytkowników jako potwierdzone.</span><span class="sxs-lookup"><span data-stu-id="cb52c-212">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="cb52c-213">Potwierdź istniejących użytkowników.</span><span class="sxs-lookup"><span data-stu-id="cb52c-213">Confirm existing users.</span></span> <span data-ttu-id="cb52c-214">Na przykład wysyłanie wsadowe wiadomości e-mail z linkami potwierdzającymi.</span><span class="sxs-lookup"><span data-stu-id="cb52c-214">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="cb52c-215">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="cb52c-215">Prerequisites</span></span>

[<span data-ttu-id="cb52c-216">.NET Core 2,2 SDK lub nowszy</span><span class="sxs-lookup"><span data-stu-id="cb52c-216">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-no-locidentity"></a><span data-ttu-id="cb52c-217">Tworzenie aplikacji sieci Web i szkieletu Identity</span><span class="sxs-lookup"><span data-stu-id="cb52c-217">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="cb52c-218">Uruchom następujące polecenia, aby utworzyć aplikację sieci Web z uwierzytelnianiem.</span><span class="sxs-lookup"><span data-stu-id="cb52c-218">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run
```

> [!NOTE]
> <span data-ttu-id="cb52c-219">Jeśli <xref:Microsoft.AspNetCore.Identity.PasswordOptions> są skonfigurowane w programie `Startup.ConfigureServices` , konfiguracja [ `[StringLength]` atrybutu](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) może być wymagana dla `Password` właściwości na stronach szkieletowych Identity .</span><span class="sxs-lookup"><span data-stu-id="cb52c-219">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="cb52c-220">`InputModel` `Password` Właściwość znajduje się w `Areas/Identity/Pages/Account/Register.cshtml.cs` pliku po utworzeniu szkieletu Identity .</span><span class="sxs-lookup"><span data-stu-id="cb52c-220">An `InputModel` `Password` property is found in the `Areas/Identity/Pages/Account/Register.cshtml.cs` file after scaffolding Identity.</span></span>

## <a name="test-new-user-registration"></a><span data-ttu-id="cb52c-221">Testowanie rejestracji nowego użytkownika</span><span class="sxs-lookup"><span data-stu-id="cb52c-221">Test new user registration</span></span>

<span data-ttu-id="cb52c-222">Uruchom aplikację, wybierz łącze **zarejestruj** i zarejestruj użytkownika.</span><span class="sxs-lookup"><span data-stu-id="cb52c-222">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="cb52c-223">W tym momencie jedynym sprawdzaniem poprawności w wiadomości e-mail jest [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atrybut.</span><span class="sxs-lookup"><span data-stu-id="cb52c-223">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="cb52c-224">Po przesłaniu rejestracji nastąpi zalogowanie do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cb52c-224">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="cb52c-225">W dalszej części tego samouczka kod zostanie zaktualizowany, więc nowi użytkownicy nie będą mogli się zalogować do momentu zweryfikowania ich poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-225">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="cb52c-226">Zwróć uwagę na to, że `EmailConfirmed` pole tabeli ma wartość `False` .</span><span class="sxs-lookup"><span data-stu-id="cb52c-226">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="cb52c-227">Możesz chcieć ponownie użyć tej wiadomości e-mail w następnym kroku, gdy aplikacja wyśle wiadomość e-mail z potwierdzeniem.</span><span class="sxs-lookup"><span data-stu-id="cb52c-227">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="cb52c-228">Kliknij prawym przyciskiem myszy wiersz i wybierz polecenie **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="cb52c-228">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="cb52c-229">Usunięcie aliasu e-mail ułatwia wykonanie następujących czynności.</span><span class="sxs-lookup"><span data-stu-id="cb52c-229">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="cb52c-230">Żądaj potwierdzenia wiadomości e-mail</span><span class="sxs-lookup"><span data-stu-id="cb52c-230">Require email confirmation</span></span>

<span data-ttu-id="cb52c-231">Najlepszym rozwiązaniem jest potwierdzenie wiadomości e-mail o nowej rejestracji użytkownika.</span><span class="sxs-lookup"><span data-stu-id="cb52c-231">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="cb52c-232">Potwierdzenie poczty e-mail pomaga sprawdzić, czy nie personifikują kogoś innego (oznacza to, że nie zostały zarejestrowane w wiadomości e-mail innej osoby).</span><span class="sxs-lookup"><span data-stu-id="cb52c-232">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="cb52c-233">Załóżmy, że masz forum dyskusyjne i chcesz uniemożliwić " yli@example.com " Rejestrowanie jako "" nolivetto@contoso.com .</span><span class="sxs-lookup"><span data-stu-id="cb52c-233">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="cb52c-234">Bez potwierdzenia wiadomości e-mail " nolivetto@contoso.com " może odbierać niechciane wiadomości e-mail z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cb52c-234">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="cb52c-235">Załóżmy, że użytkownik przypadkowo zarejestrował się jako " ylo@example.com " i nie błąd pisowni "yli".</span><span class="sxs-lookup"><span data-stu-id="cb52c-235">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="cb52c-236">Nie będą one mogły korzystać z odzyskiwania hasła, ponieważ aplikacja nie ma poprawnego adresu e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-236">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="cb52c-237">Potwierdzenie poczty e-mail zapewnia ograniczoną ochronę z botów.</span><span class="sxs-lookup"><span data-stu-id="cb52c-237">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="cb52c-238">Potwierdzenie poczty e-mail nie zapewnia ochrony przed złośliwymi użytkownikami z wieloma kontami e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-238">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="cb52c-239">Zazwyczaj chcesz uniemożliwić nowym użytkownikom ogłaszanie danych w witrynie sieci Web przed potwierdzeniem wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-239">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="cb52c-240">Aktualizuj `Startup.ConfigureServices`  , aby wymagać potwierdzonej wiadomości e-mail:</span><span class="sxs-lookup"><span data-stu-id="cb52c-240">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="cb52c-241">`config.SignIn.RequireConfirmedEmail = true;` zapobiega logowaniu się zarejestrowanych użytkowników do momentu potwierdzenia ich wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-241">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="cb52c-242">Konfigurowanie dostawcy poczty e-mail</span><span class="sxs-lookup"><span data-stu-id="cb52c-242">Configure email provider</span></span>

<span data-ttu-id="cb52c-243">W tym samouczku [SendGrid](https://sendgrid.com) jest używany do wysyłania wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-243">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="cb52c-244">Aby wysłać wiadomość e-mail, musisz mieć konto SendGrid i klucz.</span><span class="sxs-lookup"><span data-stu-id="cb52c-244">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="cb52c-245">Możesz użyć innych dostawców poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-245">You can use other email providers.</span></span> <span data-ttu-id="cb52c-246">Program zawiera ASP.NET Core 2. x `System.Net.Mail` , który umożliwia wysyłanie wiadomości e-mail z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cb52c-246">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="cb52c-247">Zalecamy użycie SendGrid lub innej usługi poczty e-mail do wysyłania wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-247">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="cb52c-248">Protokół SMTP jest trudny do poprawnego zabezpieczania i konfigurowania.</span><span class="sxs-lookup"><span data-stu-id="cb52c-248">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="cb52c-249">Utwórz klasę, aby pobrać bezpieczny klucz poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-249">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="cb52c-250">Dla tego przykładu Utwórz *usługi/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="cb52c-250">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="cb52c-251">Konfigurowanie kluczy tajnych użytkownika SendGrid</span><span class="sxs-lookup"><span data-stu-id="cb52c-251">Configure SendGrid user secrets</span></span>

<span data-ttu-id="cb52c-252">Ustaw `SendGridUser` i `SendGridKey` za pomocą [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="cb52c-252">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="cb52c-253">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb52c-253">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="cb52c-254">W systemie Windows Menedżer wpisów tajnych przechowuje pary klucz/wartość w *secrets.js* w pliku w `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` katalogu.</span><span class="sxs-lookup"><span data-stu-id="cb52c-254">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="cb52c-255">Zawartość *secrets.jsw* pliku nie jest zaszyfrowana.</span><span class="sxs-lookup"><span data-stu-id="cb52c-255">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="cb52c-256">Poniższe znaczniki przedstawiają *secrets.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="cb52c-256">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="cb52c-257">`SendGridKey`Wartość została usunięta.</span><span class="sxs-lookup"><span data-stu-id="cb52c-257">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="cb52c-258">Aby uzyskać więcej informacji, zobacz [Opcje wzorca](xref:fundamentals/configuration/options) i [konfiguracji](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="cb52c-258">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="cb52c-259">Zainstaluj SendGrid</span><span class="sxs-lookup"><span data-stu-id="cb52c-259">Install SendGrid</span></span>

<span data-ttu-id="cb52c-260">W tym samouczku pokazano, jak dodać powiadomienia e-mail za pośrednictwem usługi [SendGrid](https://sendgrid.com/), ale można wysyłać wiadomości e-mail przy użyciu protokołu SMTP i innych mechanizmów.</span><span class="sxs-lookup"><span data-stu-id="cb52c-260">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="cb52c-261">Zainstaluj `SendGrid` pakiet NuGet:</span><span class="sxs-lookup"><span data-stu-id="cb52c-261">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb52c-262">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb52c-262">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cb52c-263">W konsoli Menedżera pakietów wprowadź następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="cb52c-263">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="cb52c-264">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="cb52c-264">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cb52c-265">W konsoli programu wprowadź następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="cb52c-265">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="cb52c-266">Zobacz artykuł Wprowadzenie do usługi [SendGrid bezpłatnie](https://sendgrid.com/free/) , aby zarejestrować się w celu uzyskania bezpłatnego konta SendGrid.</span><span class="sxs-lookup"><span data-stu-id="cb52c-266">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="cb52c-267">Implementuj IEmailSender</span><span class="sxs-lookup"><span data-stu-id="cb52c-267">Implement IEmailSender</span></span>

<span data-ttu-id="cb52c-268">Aby zaimplementować `IEmailSender` program, Utwórz *usługi/EmailSender. cs* z kodem podobnym do poniższego:</span><span class="sxs-lookup"><span data-stu-id="cb52c-268">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="cb52c-269">Konfigurowanie uruchamiania do obsługi poczty e-mail</span><span class="sxs-lookup"><span data-stu-id="cb52c-269">Configure startup to support email</span></span>

<span data-ttu-id="cb52c-270">Dodaj następujący kod do `ConfigureServices` metody w pliku *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="cb52c-270">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="cb52c-271">Dodaj `EmailSender` jako usługę przejściową.</span><span class="sxs-lookup"><span data-stu-id="cb52c-271">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="cb52c-272">Zarejestruj `AuthMessageSenderOptions` wystąpienie konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="cb52c-272">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="cb52c-273">Włącz potwierdzenie konta i odzyskiwanie hasła</span><span class="sxs-lookup"><span data-stu-id="cb52c-273">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="cb52c-274">Szablon zawiera kod służący do potwierdzenia konta i odzyskiwania hasła.</span><span class="sxs-lookup"><span data-stu-id="cb52c-274">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="cb52c-275">Znajdź `OnPostAsync` metodę w *obszarach/ Identity /Pages/Account/register.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="cb52c-275">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="cb52c-276">Zablokuj automatyczne logowanie nowo zarejestrowanych użytkowników, dodając komentarz do następującego wiersza:</span><span class="sxs-lookup"><span data-stu-id="cb52c-276">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="cb52c-277">Pełna metoda jest pokazywana z wyróżnioną zmienionym wierszem:</span><span class="sxs-lookup"><span data-stu-id="cb52c-277">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="cb52c-278">Rejestrowanie, Potwierdzanie poczty e-mail i resetowanie hasła</span><span class="sxs-lookup"><span data-stu-id="cb52c-278">Register, confirm email, and reset password</span></span>

<span data-ttu-id="cb52c-279">Uruchom aplikację internetową, a następnie przetestuj potwierdzenie konta i przepływ odzyskiwania hasła.</span><span class="sxs-lookup"><span data-stu-id="cb52c-279">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="cb52c-280">Uruchom aplikację i Zarejestruj nowego użytkownika</span><span class="sxs-lookup"><span data-stu-id="cb52c-280">Run the app and register a new user</span></span>
* <span data-ttu-id="cb52c-281">Sprawdź wiadomość e-mail z linkiem potwierdzenia konta.</span><span class="sxs-lookup"><span data-stu-id="cb52c-281">Check your email for the account confirmation link.</span></span> <span data-ttu-id="cb52c-282">Jeśli nie otrzymasz wiadomości e-mail, zobacz [debugowanie poczty e-mail](#debug) .</span><span class="sxs-lookup"><span data-stu-id="cb52c-282">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="cb52c-283">Kliknij link, aby potwierdzić swój adres e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-283">Click the link to confirm your email.</span></span>
* <span data-ttu-id="cb52c-284">Zaloguj się przy użyciu adresu e-mail i hasła.</span><span class="sxs-lookup"><span data-stu-id="cb52c-284">Sign in with your email and password.</span></span>
* <span data-ttu-id="cb52c-285">Wyloguj się.</span><span class="sxs-lookup"><span data-stu-id="cb52c-285">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="cb52c-286">Wyświetlanie strony zarządzania</span><span class="sxs-lookup"><span data-stu-id="cb52c-286">View the manage page</span></span>

<span data-ttu-id="cb52c-287">Wybierz swoją nazwę użytkownika w przeglądarce: ![ okno przeglądarki z nazwą użytkownika](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="cb52c-287">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="cb52c-288">Zostanie wyświetlona strona zarządzanie z wybraną kartą **profil** .</span><span class="sxs-lookup"><span data-stu-id="cb52c-288">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="cb52c-289">**Wiadomość e-mail** zawiera pole wyboru informujące o potwierdzeniu wiadomości e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-289">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="cb52c-290">Resetowanie hasła do testu</span><span class="sxs-lookup"><span data-stu-id="cb52c-290">Test password reset</span></span>

* <span data-ttu-id="cb52c-291">Jeśli użytkownik jest zalogowany, wybierz pozycję **Wyloguj**.</span><span class="sxs-lookup"><span data-stu-id="cb52c-291">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="cb52c-292">Wybierz link **Zaloguj** i wybierz łącze nie **pamiętasz hasła?**</span><span class="sxs-lookup"><span data-stu-id="cb52c-292">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="cb52c-293">Wprowadź adres e-mail, który został użyty do zarejestrowania konta.</span><span class="sxs-lookup"><span data-stu-id="cb52c-293">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="cb52c-294">Zostanie wysłana wiadomość e-mail z linkiem służącym do resetowania hasła.</span><span class="sxs-lookup"><span data-stu-id="cb52c-294">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="cb52c-295">Sprawdź swój adres e-mail i kliknij link, aby zresetować hasło.</span><span class="sxs-lookup"><span data-stu-id="cb52c-295">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="cb52c-296">Po pomyślnym zresetowaniu hasła możesz zalogować się przy użyciu adresu e-mail i nowego hasła.</span><span class="sxs-lookup"><span data-stu-id="cb52c-296">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="cb52c-297">Zmień limit czasu wiadomości e-mail i aktywności</span><span class="sxs-lookup"><span data-stu-id="cb52c-297">Change email and activity timeout</span></span>

<span data-ttu-id="cb52c-298">Domyślny limit czasu braku aktywności wynosi 14 dni.</span><span class="sxs-lookup"><span data-stu-id="cb52c-298">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="cb52c-299">Następujący kod ustawia limit czasu braku aktywności na 5 dni:</span><span class="sxs-lookup"><span data-stu-id="cb52c-299">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="cb52c-300">Zmień wszystkie lifespans tokenów ochrony danych</span><span class="sxs-lookup"><span data-stu-id="cb52c-300">Change all data protection token lifespans</span></span>

<span data-ttu-id="cb52c-301">Następujący kod zmienia limit czasu tokenów ochrony danych na 3 godziny:</span><span class="sxs-lookup"><span data-stu-id="cb52c-301">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="cb52c-302">Wbudowane Identity tokeny użytkownika (zobacz [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mają [jeden dzień limitu czasu](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="cb52c-302">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="cb52c-303">Zmień cykl życia tokenu poczty e-mail</span><span class="sxs-lookup"><span data-stu-id="cb52c-303">Change the email token lifespan</span></span>

<span data-ttu-id="cb52c-304">Domyślny token cykl życia tokenów [ Identity użytkownika](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) to [jeden dzień](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="cb52c-304">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="cb52c-305">W tej sekcji przedstawiono sposób zmiany cykl życia tokenu poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-305">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="cb52c-306">Dodaj niestandardową [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) i <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="cb52c-306">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="cb52c-307">Dodaj dostawcę niestandardowego do kontenera usługi:</span><span class="sxs-lookup"><span data-stu-id="cb52c-307">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="cb52c-308">Potwierdzenie ponownego wysłania wiadomości e-mail</span><span class="sxs-lookup"><span data-stu-id="cb52c-308">Resend email confirmation</span></span>

<span data-ttu-id="cb52c-309">Zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/5410)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="cb52c-309">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="cb52c-310">Debuguj wiadomość e-mail</span><span class="sxs-lookup"><span data-stu-id="cb52c-310">Debug email</span></span>

<span data-ttu-id="cb52c-311">Jeśli nie możesz uzyskać pracy z wiadomościami e-mail:</span><span class="sxs-lookup"><span data-stu-id="cb52c-311">If you can't get email working:</span></span>

* <span data-ttu-id="cb52c-312">Ustaw punkt przerwania w programie `EmailSender.Execute` , aby sprawdzić, czy `SendGridClient.SendEmailAsync` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="cb52c-312">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="cb52c-313">Utwórz [aplikację konsolową do wysyłania wiadomości e-mail](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) przy użyciu podobnego kodu do programu `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="cb52c-313">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="cb52c-314">Przejrzyj stronę [działania e-mail](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="cb52c-314">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="cb52c-315">Sprawdź swój folder spamu.</span><span class="sxs-lookup"><span data-stu-id="cb52c-315">Check your spam folder.</span></span>
* <span data-ttu-id="cb52c-316">Wypróbuj inny alias poczty e-mail na innym dostawcy poczty e-mail (Microsoft, Yahoo, Gmail itp.)</span><span class="sxs-lookup"><span data-stu-id="cb52c-316">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="cb52c-317">Spróbuj wysłać konto do innych kont e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-317">Try sending to different email accounts.</span></span>

<span data-ttu-id="cb52c-318">**Najlepszym rozwiązaniem** w zakresie zabezpieczeń jest **nieużywanie produkcyjnych** wpisów tajnych w testowaniu i programowaniu.</span><span class="sxs-lookup"><span data-stu-id="cb52c-318">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="cb52c-319">Jeśli opublikujesz aplikację na platformie Azure, możesz ustawić wpisy tajne SendGrid jako ustawienia aplikacji w portalu aplikacji sieci Web platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="cb52c-319">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="cb52c-320">System konfiguracji jest skonfigurowany do odczytywania kluczy ze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="cb52c-320">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="cb52c-321">Łączenie kont logowania społecznościowych i lokalnych</span><span class="sxs-lookup"><span data-stu-id="cb52c-321">Combine social and local login accounts</span></span>

<span data-ttu-id="cb52c-322">Aby ukończyć tę sekcję, należy najpierw włączyć zewnętrznego dostawcę uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="cb52c-322">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="cb52c-323">Zobacz [uwierzytelnianie w usługach Facebook, Google i dostawcy zewnętrznym](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="cb52c-323">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="cb52c-324">Konta lokalne i społecznościowe można łączyć przez kliknięcie linku do poczty e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb52c-324">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="cb52c-325">W następującej kolejności " RickAndMSFT@gmail.com " jest najpierw tworzony jako logowanie lokalne; można jednak najpierw utworzyć konto jako nazwę logowania w sieci społecznościowej, a następnie dodać lokalną nazwę logowania.</span><span class="sxs-lookup"><span data-stu-id="cb52c-325">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplikacja sieci Web: RickAndMSFT@gmail.com użytkownik uwierzytelniony](accconfirm/_static/rick.png)

<span data-ttu-id="cb52c-327">Kliknij link **Zarządzaj** .</span><span class="sxs-lookup"><span data-stu-id="cb52c-327">Click on the **Manage** link.</span></span> <span data-ttu-id="cb52c-328">Zwróć uwagę na wartość 0 External (logowanie społeczne) skojarzoną z tym kontem.</span><span class="sxs-lookup"><span data-stu-id="cb52c-328">Note the 0 external (social logins) associated with this account.</span></span>

![Zarządzaj widokiem](accconfirm/_static/manage.png)

<span data-ttu-id="cb52c-330">Kliknij link do innej usługi logowania i zaakceptuj żądania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="cb52c-330">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="cb52c-331">Na poniższym obrazie serwis Facebook jest dostawcą zewnętrznym uwierzytelniania:</span><span class="sxs-lookup"><span data-stu-id="cb52c-331">In the following image, Facebook is the external authentication provider:</span></span>

![Zarządzanie widokiem logowania zewnętrznego w serwisie Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="cb52c-333">Te dwa konta zostały połączone.</span><span class="sxs-lookup"><span data-stu-id="cb52c-333">The two accounts have been combined.</span></span> <span data-ttu-id="cb52c-334">Możesz zalogować się przy użyciu dowolnego konta.</span><span class="sxs-lookup"><span data-stu-id="cb52c-334">You are able to sign in with either account.</span></span> <span data-ttu-id="cb52c-335">Możesz chcieć, aby użytkownicy mogli dodawać konta lokalne w przypadku, gdy usługa uwierzytelniania przy logowaniu w sieci społecznościowej nie działa, lub prawdopodobnie utraciła dostęp do konta społecznościowego.</span><span class="sxs-lookup"><span data-stu-id="cb52c-335">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="cb52c-336">Włącz potwierdzenie konta po stronie, w której znajdują się użytkownicy</span><span class="sxs-lookup"><span data-stu-id="cb52c-336">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="cb52c-337">Włączenie potwierdzenia konta w witrynie z użytkownikami powoduje zablokowanie wszystkich istniejących użytkowników.</span><span class="sxs-lookup"><span data-stu-id="cb52c-337">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="cb52c-338">Istniejący użytkownicy są Zablokowani, ponieważ ich konta nie zostały potwierdzone.</span><span class="sxs-lookup"><span data-stu-id="cb52c-338">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="cb52c-339">Aby obejść istniejącą blokadę użytkownika, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="cb52c-339">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="cb52c-340">Zaktualizuj bazę danych, aby oznaczyć wszystkich istniejących użytkowników jako potwierdzone.</span><span class="sxs-lookup"><span data-stu-id="cb52c-340">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="cb52c-341">Potwierdź istniejących użytkowników.</span><span class="sxs-lookup"><span data-stu-id="cb52c-341">Confirm existing users.</span></span> <span data-ttu-id="cb52c-342">Na przykład wysyłanie wsadowe wiadomości e-mail z linkami potwierdzającymi.</span><span class="sxs-lookup"><span data-stu-id="cb52c-342">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
