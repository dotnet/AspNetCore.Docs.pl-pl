---
title: Uwierzytelnianie dwuskładnikowe za pomocą wiadomości SMS w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować uwierzytelnianie dwuskładnikowe (funkcji 2FA) za pomocą aplikacji ASP.NET Core.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/2fa
ms.openlocfilehash: 1ee9e656c2e631c9b5588149e0a75e07108baff1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051268"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="945ca-103">Uwierzytelnianie dwuskładnikowe za pomocą wiadomości SMS w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="945ca-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="945ca-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [szwajcarski-deweloperzy](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="945ca-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="945ca-105">Uwierzytelnianie dwuskładnikowe (funkcji 2FA) przy użyciu algorytmu hasła opartego na czasie (TOTP) jest zalecanym rozwiązaniem dla funkcji 2FA.</span><span class="sxs-lookup"><span data-stu-id="945ca-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="945ca-106">FUNKCJI 2FA przy użyciu TOTP jest preferowana dla SMS funkcji 2FA.</span><span class="sxs-lookup"><span data-stu-id="945ca-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="945ca-107">Aby uzyskać więcej informacji, zobacz [Włączanie generowania kodu QR dla aplikacji TOTP Authenticator w ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) dla ASP.NET Core 2,0 i nowszych.</span><span class="sxs-lookup"><span data-stu-id="945ca-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="945ca-108">W tym samouczku pokazano, jak skonfigurować uwierzytelnianie dwuskładnikowe (funkcji 2FA) przy użyciu wiadomości SMS.</span><span class="sxs-lookup"><span data-stu-id="945ca-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="945ca-109">Instrukcje są podane dla [Twilio](https://www.twilio.com/) i [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), ale można użyć dowolnego innego dostawcy programu SMS.</span><span class="sxs-lookup"><span data-stu-id="945ca-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="945ca-110">Przed rozpoczęciem tego samouczka zalecamy ukończenie [potwierdzeń konta i odzyskiwania hasła](xref:security/authentication/accconfirm) .</span><span class="sxs-lookup"><span data-stu-id="945ca-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="945ca-111">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span><span class="sxs-lookup"><span data-stu-id="945ca-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="945ca-112">[Jak pobrać](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="945ca-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="945ca-113">Utwórz nowy projekt ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="945ca-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="945ca-114">Utwórz nową aplikację sieci Web ASP.NET Core o nazwie `Web2FA` przy użyciu poszczególnych kont użytkowników.</span><span class="sxs-lookup"><span data-stu-id="945ca-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="945ca-115">Postępuj zgodnie z instrukcjami w, <xref:security/enforcing-ssl> Aby skonfigurować i wymagać protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="945ca-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="945ca-116">Utwórz konto programu SMS</span><span class="sxs-lookup"><span data-stu-id="945ca-116">Create an SMS account</span></span>

<span data-ttu-id="945ca-117">Utwórz konto programu SMS, na przykład z [Twilio](https://www.twilio.com/) lub [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span><span class="sxs-lookup"><span data-stu-id="945ca-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="945ca-118">Zapisz poświadczenia uwierzytelniania (dla Twilio: accountSid i authToken, dla ASPSMS: userKey i hasła).</span><span class="sxs-lookup"><span data-stu-id="945ca-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="945ca-119">Rozprowadzenie poświadczeń dostawcy programu SMS</span><span class="sxs-lookup"><span data-stu-id="945ca-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="945ca-120">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="945ca-120">**Twilio:**</span></span>

<span data-ttu-id="945ca-121">Na karcie Pulpit nawigacyjny konta usługi Twilio Skopiuj **Identyfikator SID konta** i **token uwierzytelniania** .</span><span class="sxs-lookup"><span data-stu-id="945ca-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token** .</span></span>

<span data-ttu-id="945ca-122">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="945ca-122">**ASPSMS:**</span></span>

<span data-ttu-id="945ca-123">W ustawieniach konta przejdź do **userKey** i skopiuj go wraz z **hasłem** .</span><span class="sxs-lookup"><span data-stu-id="945ca-123">From your account settings, navigate to **Userkey** and copy it together with your **Password** .</span></span>

<span data-ttu-id="945ca-124">Te wartości zostaną później zapisane za pomocą narzędzia Menedżer kluczy tajnych w kluczach `SMSAccountIdentification` i `SMSAccountPassword` .</span><span class="sxs-lookup"><span data-stu-id="945ca-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="945ca-125">Określanie SenderID/inicjatora</span><span class="sxs-lookup"><span data-stu-id="945ca-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="945ca-126">**Twilio:** Na karcie liczby Skopiuj **numer telefonu** Twilio.</span><span class="sxs-lookup"><span data-stu-id="945ca-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number** .</span></span>

<span data-ttu-id="945ca-127">**ASPSMS:** W menu odblokowane odblokowywanie Odblokuj co najmniej jeden inicjator lub wybierz inicjator alfanumeryczny (nieobsługiwany przez wszystkie sieci).</span><span class="sxs-lookup"><span data-stu-id="945ca-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="945ca-128">Ta wartość zostanie później przechowana przy użyciu narzędzia do zarządzania kluczami tajnymi w ramach klucza `SMSAccountFrom` .</span><span class="sxs-lookup"><span data-stu-id="945ca-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="945ca-129">Podaj poświadczenia dla usługi programu SMS</span><span class="sxs-lookup"><span data-stu-id="945ca-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="945ca-130">Użyjemy [wzorca opcji](xref:fundamentals/configuration/options) , aby uzyskać dostęp do ustawień konta użytkownika i klucza.</span><span class="sxs-lookup"><span data-stu-id="945ca-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="945ca-131">Utwórz klasę, aby pobrać bezpieczny klucz SMS.</span><span class="sxs-lookup"><span data-stu-id="945ca-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="945ca-132">Dla tego przykładu `SMSoptions` Klasa jest tworzona w pliku *Services/SMSoptions. cs* .</span><span class="sxs-lookup"><span data-stu-id="945ca-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="945ca-133">Ustaw `SMSAccountIdentification` opcję `SMSAccountPassword` oraz `SMSAccountFrom` za pomocą narzędzia do [zarządzania kluczami tajnymi](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="945ca-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="945ca-134">Przykład:</span><span class="sxs-lookup"><span data-stu-id="945ca-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="945ca-135">Dodaj pakiet NuGet dla dostawcy programu SMS.</span><span class="sxs-lookup"><span data-stu-id="945ca-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="945ca-136">W konsoli Menedżera pakietów (PMC) Uruchom:</span><span class="sxs-lookup"><span data-stu-id="945ca-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="945ca-137">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="945ca-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="945ca-138">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="945ca-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="945ca-139">Aby włączyć program SMS, Dodaj kod w pliku *Services/MessageServices. cs* .</span><span class="sxs-lookup"><span data-stu-id="945ca-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="945ca-140">Użyj Twilio lub ASPSMS sekcji:</span><span class="sxs-lookup"><span data-stu-id="945ca-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="945ca-141">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="945ca-141">**Twilio:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

<span data-ttu-id="945ca-142">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="945ca-142">**ASPSMS:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="945ca-143">Konfigurowanie uruchamiania do użycia `SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="945ca-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="945ca-144">Dodaj `SMSoptions` do kontenera usługi w `ConfigureServices` metodzie w *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="945ca-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs* :</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="945ca-145">Włącz uwierzytelnianie dwuskładnikowe</span><span class="sxs-lookup"><span data-stu-id="945ca-145">Enable two-factor authentication</span></span>

<span data-ttu-id="945ca-146">Otwórz plik widoku views */Manage/index. cshtml* Razor i Usuń znaki komentarza (w związku z czym żaden znacznik nie jest oznaczony jako komentarz).</span><span class="sxs-lookup"><span data-stu-id="945ca-146">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="945ca-147">Logowanie przy użyciu uwierzytelniania dwuskładnikowego</span><span class="sxs-lookup"><span data-stu-id="945ca-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="945ca-148">Uruchom aplikację i Zarejestruj nowego użytkownika</span><span class="sxs-lookup"><span data-stu-id="945ca-148">Run the app and register a new user</span></span>

![Widok rejestru aplikacji sieci Web otwarty w przeglądarce Microsoft Edge](2fa/_static/login2fa1.png)

* <span data-ttu-id="945ca-150">Naciśnij pozycję Nazwa użytkownika, która aktywuje `Index` metodę akcji w obszarze Zarządzanie kontrolerem.</span><span class="sxs-lookup"><span data-stu-id="945ca-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="945ca-151">Następnie naciśnij link numer telefonu **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="945ca-151">Then tap the phone number **Add** link.</span></span>

![Zarządzanie widokiem — naciśnięcie linku "Dodaj"](2fa/_static/login2fa2.png)

* <span data-ttu-id="945ca-153">Dodaj numer telefonu, który będzie otrzymywał kod weryfikacyjny, a następnie naciśnij pozycję **Wyślij kod weryfikacyjny** .</span><span class="sxs-lookup"><span data-stu-id="945ca-153">Add a phone number that will receive the verification code, and tap **Send verification code** .</span></span>

![Strona dodawania numeru telefonu](2fa/_static/login2fa3.png)

* <span data-ttu-id="945ca-155">Otrzymasz wiadomość SMS z kodem weryfikacyjnym.</span><span class="sxs-lookup"><span data-stu-id="945ca-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="945ca-156">Wprowadź go i naciśnij pozycję **Prześlij**</span><span class="sxs-lookup"><span data-stu-id="945ca-156">Enter it and tap **Submit**</span></span>

![Strona Weryfikowanie numeru telefonu](2fa/_static/login2fa4.png)

<span data-ttu-id="945ca-158">Jeśli nie otrzymasz wiadomości tekstowej, zobacz stronę dziennika Twilio.</span><span class="sxs-lookup"><span data-stu-id="945ca-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="945ca-159">Widok zarządzanie pokazuje, że Twój numer telefonu został pomyślnie dodany.</span><span class="sxs-lookup"><span data-stu-id="945ca-159">The Manage view shows your phone number was added successfully.</span></span>

![Zarządzanie widokiem — numer telefonu został pomyślnie dodany](2fa/_static/login2fa5.png)

* <span data-ttu-id="945ca-161">Naciśnij pozycję **Włącz** , aby włączyć uwierzytelnianie dwuskładnikowe.</span><span class="sxs-lookup"><span data-stu-id="945ca-161">Tap **Enable** to enable two-factor authentication.</span></span>

![Zarządzanie widokiem — Włączanie uwierzytelniania dwuskładnikowego](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="945ca-163">Testowanie uwierzytelniania dwuskładnikowego</span><span class="sxs-lookup"><span data-stu-id="945ca-163">Test two-factor authentication</span></span>

* <span data-ttu-id="945ca-164">Wyloguj się.</span><span class="sxs-lookup"><span data-stu-id="945ca-164">Log off.</span></span>

* <span data-ttu-id="945ca-165">Zaloguj się.</span><span class="sxs-lookup"><span data-stu-id="945ca-165">Log in.</span></span>

* <span data-ttu-id="945ca-166">Konto użytkownika mogło korzystać z uwierzytelniania dwuskładnikowego, więc należy podać drugi czynnik uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="945ca-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="945ca-167">W tym samouczku włączono weryfikację telefonu.</span><span class="sxs-lookup"><span data-stu-id="945ca-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="945ca-168">Wbudowane szablony umożliwiają również skonfigurowanie poczty e-mail jako drugiego czynnika.</span><span class="sxs-lookup"><span data-stu-id="945ca-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="945ca-169">Można skonfigurować dodatkowe dwa czynniki do uwierzytelniania, takie jak kody QR.</span><span class="sxs-lookup"><span data-stu-id="945ca-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="945ca-170">Naciśnij pozycję **Prześlij** .</span><span class="sxs-lookup"><span data-stu-id="945ca-170">Tap **Submit** .</span></span>

![Wyślij widok kodu weryfikacyjnego](2fa/_static/login2fa7.png)

* <span data-ttu-id="945ca-172">Wprowadź kod uzyskany w wiadomości SMS.</span><span class="sxs-lookup"><span data-stu-id="945ca-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="945ca-173">Kliknięcie pola wyboru **Zapamiętaj tę przeglądarkę** spowoduje zwolnienie z używania funkcji 2FA do logowania się w przypadku korzystania z tego samego urządzenia i przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="945ca-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="945ca-174">Włączenie funkcji 2FA i kliknięcie opcji **Zapamiętaj, że ta przeglądarka** zapewni mocną ochronę funkcji 2FA przed złośliwymi użytkownikami próbującymi uzyskać dostęp do Twojego konta, o ile nie mają dostępu do urządzenia.</span><span class="sxs-lookup"><span data-stu-id="945ca-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="945ca-175">Można to zrobić na dowolnym używanym przez siebie urządzeniu prywatnym.</span><span class="sxs-lookup"><span data-stu-id="945ca-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="945ca-176">Dzięki ustawieniu  **Zapamiętaj tę przeglądarkę** możesz uzyskać dodatkowe zabezpieczenia funkcji 2FA z urządzeń, które nie są regularnie używane, i uzyskać wygodę, aby nie mieć możliwości przechodzenia przez funkcji 2FA na własne urządzenia.</span><span class="sxs-lookup"><span data-stu-id="945ca-176">By setting  **Remember this browser** , you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![Weryfikuj widok](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="945ca-178">Blokada konta do ochrony przed atakami z wykorzystaniem pełnego wymuszania</span><span class="sxs-lookup"><span data-stu-id="945ca-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="945ca-179">Blokada konta jest zalecana w przypadku funkcji 2FA.</span><span class="sxs-lookup"><span data-stu-id="945ca-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="945ca-180">Gdy użytkownik zaloguje się za pomocą konta lokalnego lub konta społecznościowego, każda próba zakończyła się niepowodzeniem w funkcji 2FA.</span><span class="sxs-lookup"><span data-stu-id="945ca-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="945ca-181">Jeśli osiągnięto maksymalną liczbę nieudanych prób dostępu, użytkownik jest zablokowany (domyślnie: 5 minut blokada po 5 próbach dostępu zakończonych niepowodzeniem).</span><span class="sxs-lookup"><span data-stu-id="945ca-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="945ca-182">Pomyślne uwierzytelnienie resetuje liczbę prób dostępu zakończonych niepowodzeniem i resetuje zegar.</span><span class="sxs-lookup"><span data-stu-id="945ca-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="945ca-183">Maksymalna liczba nieudanych prób dostępu i czas blokady można ustawić za pomocą [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) i [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span><span class="sxs-lookup"><span data-stu-id="945ca-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="945ca-184">Następujące konfiguruje blokadę konta przez 10 minut po 10 próbach dostępu zakończonych niepowodzeniem:</span><span class="sxs-lookup"><span data-stu-id="945ca-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="945ca-185">Potwierdź, że [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) ustawia `lockoutOnFailure` `true` :</span><span class="sxs-lookup"><span data-stu-id="945ca-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
