---
title: Uwierzytelnianie dwuskładnikowe za pomocą wiadomości SMS w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować uwierzytelnianie dwuskładnikowe (funkcji 2FA) za pomocą aplikacji ASP.NET Core.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
no-loc:
- appsettings.json
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
uid: security/authentication/2fa
ms.openlocfilehash: 1ee9e656c2e631c9b5588149e0a75e07108baff1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051268"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a>Uwierzytelnianie dwuskładnikowe za pomocą wiadomości SMS w ASP.NET Core

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [szwajcarski-deweloperzy](https://github.com/Swiss-Devs)

>[!WARNING]
> Uwierzytelnianie dwuskładnikowe (funkcji 2FA) przy użyciu algorytmu hasła opartego na czasie (TOTP) jest zalecanym rozwiązaniem dla funkcji 2FA. FUNKCJI 2FA przy użyciu TOTP jest preferowana dla SMS funkcji 2FA. Aby uzyskać więcej informacji, zobacz [Włączanie generowania kodu QR dla aplikacji TOTP Authenticator w ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) dla ASP.NET Core 2,0 i nowszych.

W tym samouczku pokazano, jak skonfigurować uwierzytelnianie dwuskładnikowe (funkcji 2FA) przy użyciu wiadomości SMS. Instrukcje są podane dla [Twilio](https://www.twilio.com/) i [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), ale można użyć dowolnego innego dostawcy programu SMS. Przed rozpoczęciem tego samouczka zalecamy ukończenie [potwierdzeń konta i odzyskiwania hasła](xref:security/authentication/accconfirm) .

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA). [Jak pobrać](xref:index#how-to-download-a-sample).

## <a name="create-a-new-aspnet-core-project"></a>Utwórz nowy projekt ASP.NET Core

Utwórz nową aplikację sieci Web ASP.NET Core o nazwie `Web2FA` przy użyciu poszczególnych kont użytkowników. Postępuj zgodnie z instrukcjami w, <xref:security/enforcing-ssl> Aby skonfigurować i wymagać protokołu HTTPS.

### <a name="create-an-sms-account"></a>Utwórz konto programu SMS

Utwórz konto programu SMS, na przykład z [Twilio](https://www.twilio.com/) lub [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/). Zapisz poświadczenia uwierzytelniania (dla Twilio: accountSid i authToken, dla ASPSMS: userKey i hasła).

#### <a name="figuring-out-sms-provider-credentials"></a>Rozprowadzenie poświadczeń dostawcy programu SMS

**Twilio**

Na karcie Pulpit nawigacyjny konta usługi Twilio Skopiuj **Identyfikator SID konta** i **token uwierzytelniania** .

**ASPSMS:**

W ustawieniach konta przejdź do **userKey** i skopiuj go wraz z **hasłem** .

Te wartości zostaną później zapisane za pomocą narzędzia Menedżer kluczy tajnych w kluczach `SMSAccountIdentification` i `SMSAccountPassword` .

#### <a name="specifying-senderid--originator"></a>Określanie SenderID/inicjatora

**Twilio:** Na karcie liczby Skopiuj **numer telefonu** Twilio.

**ASPSMS:** W menu odblokowane odblokowywanie Odblokuj co najmniej jeden inicjator lub wybierz inicjator alfanumeryczny (nieobsługiwany przez wszystkie sieci).

Ta wartość zostanie później przechowana przy użyciu narzędzia do zarządzania kluczami tajnymi w ramach klucza `SMSAccountFrom` .

### <a name="provide-credentials-for-the-sms-service"></a>Podaj poświadczenia dla usługi programu SMS

Użyjemy [wzorca opcji](xref:fundamentals/configuration/options) , aby uzyskać dostęp do ustawień konta użytkownika i klucza.

* Utwórz klasę, aby pobrać bezpieczny klucz SMS. Dla tego przykładu `SMSoptions` Klasa jest tworzona w pliku *Services/SMSoptions. cs* .

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

Ustaw `SMSAccountIdentification` opcję `SMSAccountPassword` oraz `SMSAccountFrom` za pomocą narzędzia do [zarządzania kluczami tajnymi](xref:security/app-secrets). Przykład:

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* Dodaj pakiet NuGet dla dostawcy programu SMS. W konsoli Menedżera pakietów (PMC) Uruchom:

**Twilio**

`Install-Package Twilio`

**ASPSMS:**

`Install-Package ASPSMS`

* Aby włączyć program SMS, Dodaj kod w pliku *Services/MessageServices. cs* . Użyj Twilio lub ASPSMS sekcji:

**Twilio**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**ASPSMS:**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>Konfigurowanie uruchamiania do użycia `SMSoptions`

Dodaj `SMSoptions` do kontenera usługi w `ConfigureServices` metodzie w *Startup.cs* :

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>Włącz uwierzytelnianie dwuskładnikowe

Otwórz plik widoku views */Manage/index. cshtml* Razor i Usuń znaki komentarza (w związku z czym żaden znacznik nie jest oznaczony jako komentarz).

## <a name="log-in-with-two-factor-authentication"></a>Logowanie przy użyciu uwierzytelniania dwuskładnikowego

* Uruchom aplikację i Zarejestruj nowego użytkownika

![Widok rejestru aplikacji sieci Web otwarty w przeglądarce Microsoft Edge](2fa/_static/login2fa1.png)

* Naciśnij pozycję Nazwa użytkownika, która aktywuje `Index` metodę akcji w obszarze Zarządzanie kontrolerem. Następnie naciśnij link numer telefonu **Dodaj** .

![Zarządzanie widokiem — naciśnięcie linku "Dodaj"](2fa/_static/login2fa2.png)

* Dodaj numer telefonu, który będzie otrzymywał kod weryfikacyjny, a następnie naciśnij pozycję **Wyślij kod weryfikacyjny** .

![Strona dodawania numeru telefonu](2fa/_static/login2fa3.png)

* Otrzymasz wiadomość SMS z kodem weryfikacyjnym. Wprowadź go i naciśnij pozycję **Prześlij**

![Strona Weryfikowanie numeru telefonu](2fa/_static/login2fa4.png)

Jeśli nie otrzymasz wiadomości tekstowej, zobacz stronę dziennika Twilio.

* Widok zarządzanie pokazuje, że Twój numer telefonu został pomyślnie dodany.

![Zarządzanie widokiem — numer telefonu został pomyślnie dodany](2fa/_static/login2fa5.png)

* Naciśnij pozycję **Włącz** , aby włączyć uwierzytelnianie dwuskładnikowe.

![Zarządzanie widokiem — Włączanie uwierzytelniania dwuskładnikowego](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>Testowanie uwierzytelniania dwuskładnikowego

* Wyloguj się.

* Zaloguj się.

* Konto użytkownika mogło korzystać z uwierzytelniania dwuskładnikowego, więc należy podać drugi czynnik uwierzytelniania. W tym samouczku włączono weryfikację telefonu. Wbudowane szablony umożliwiają również skonfigurowanie poczty e-mail jako drugiego czynnika. Można skonfigurować dodatkowe dwa czynniki do uwierzytelniania, takie jak kody QR. Naciśnij pozycję **Prześlij** .

![Wyślij widok kodu weryfikacyjnego](2fa/_static/login2fa7.png)

* Wprowadź kod uzyskany w wiadomości SMS.

* Kliknięcie pola wyboru **Zapamiętaj tę przeglądarkę** spowoduje zwolnienie z używania funkcji 2FA do logowania się w przypadku korzystania z tego samego urządzenia i przeglądarki. Włączenie funkcji 2FA i kliknięcie opcji **Zapamiętaj, że ta przeglądarka** zapewni mocną ochronę funkcji 2FA przed złośliwymi użytkownikami próbującymi uzyskać dostęp do Twojego konta, o ile nie mają dostępu do urządzenia. Można to zrobić na dowolnym używanym przez siebie urządzeniu prywatnym. Dzięki ustawieniu  **Zapamiętaj tę przeglądarkę** możesz uzyskać dodatkowe zabezpieczenia funkcji 2FA z urządzeń, które nie są regularnie używane, i uzyskać wygodę, aby nie mieć możliwości przechodzenia przez funkcji 2FA na własne urządzenia.

![Weryfikuj widok](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>Blokada konta do ochrony przed atakami z wykorzystaniem pełnego wymuszania

Blokada konta jest zalecana w przypadku funkcji 2FA. Gdy użytkownik zaloguje się za pomocą konta lokalnego lub konta społecznościowego, każda próba zakończyła się niepowodzeniem w funkcji 2FA. Jeśli osiągnięto maksymalną liczbę nieudanych prób dostępu, użytkownik jest zablokowany (domyślnie: 5 minut blokada po 5 próbach dostępu zakończonych niepowodzeniem). Pomyślne uwierzytelnienie resetuje liczbę prób dostępu zakończonych niepowodzeniem i resetuje zegar. Maksymalna liczba nieudanych prób dostępu i czas blokady można ustawić za pomocą [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) i [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan). Następujące konfiguruje blokadę konta przez 10 minut po 10 próbach dostępu zakończonych niepowodzeniem:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

Potwierdź, że [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) ustawia `lockoutOnFailure` `true` :

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
