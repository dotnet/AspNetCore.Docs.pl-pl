---
title: Potwierdzenie konta i odzyskiwanie hasła w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak utworzyć aplikację ASP.NET Core przy użyciu potwierdzenia wiadomości e-mail i resetowania hasła.
ms.author: riande
ms.date: 03/11/2019
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
uid: security/authentication/accconfirm
ms.openlocfilehash: 91148c67d5dc0bf97e2f926f50dcff5dd0708f4b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052321"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>Potwierdzenie konta i odzyskiwanie hasła w ASP.NET Core

[Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)i Jan [Audette](https://twitter.com/joeaudette)

W tym samouczku pokazano, jak utworzyć aplikację ASP.NET Core przy użyciu potwierdzenia wiadomości e-mail i resetowania hasła. Ten samouczek **nie** jest tematem początkowym. Należy zapoznać się z:

* [ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start)
* [Authentication](xref:security/authentication/identity)
* [Entity Framework Core](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Wymagania wstępne

[.NET Core 3,0 SDK lub nowszy](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a>Tworzenie i testowanie aplikacji sieci Web przy użyciu uwierzytelniania

Uruchom następujące polecenia, aby utworzyć aplikację sieci Web z uwierzytelnianiem.

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

Uruchom aplikację, wybierz łącze **zarejestruj** i zarejestruj użytkownika. Po zarejestrowaniu nastąpi przekierowanie na `/Identity/Account/RegisterConfirmation` stronę do, która zawiera link umożliwiający zasymulowanie potwierdzenia wiadomości e-mail:

* Wybierz link `Click here to confirm your account`.
* Wybierz łącze **logowania** i zaloguj się przy użyciu tych samych poświadczeń.
* Wybierz `Hello YourEmail@provider.com!` link, który przekierowuje Cię do `/Identity/Account/Manage/PersonalData` strony.
* Wybierz kartę **dane osobowe** po lewej stronie, a następnie wybierz pozycję **Usuń** .

### <a name="configure-an-email-provider"></a>Konfigurowanie dostawcy poczty e-mail

W tym samouczku [SendGrid](https://sendgrid.com) jest używany do wysyłania wiadomości e-mail. Aby wysłać wiadomość e-mail, musisz mieć konto SendGrid i klucz. Możesz użyć innych dostawców poczty e-mail. Zalecamy użycie SendGrid lub innej usługi poczty e-mail do wysyłania wiadomości e-mail. Protokół SMTP jest trudny do poprawnego zabezpieczania i konfigurowania.

Konto SendGrid może wymagać [dodania nadawcy](https://sendgrid.com/docs/ui/sending-email/senders/).

Utwórz klasę, aby pobrać bezpieczny klucz poczty e-mail. Dla tego przykładu Utwórz *usługi/AuthMessageSenderOptions. cs* :

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Konfigurowanie kluczy tajnych użytkownika SendGrid

Ustaw `SendGridUser` i `SendGridKey` za pomocą [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets). Przykład:

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

W systemie Windows Menedżer wpisów tajnych przechowuje pary klucz/wartość w *secrets.js* w pliku w `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` katalogu.

Zawartość *secrets.jsw* pliku nie jest zaszyfrowana. Poniższe znaczniki przedstawiają *secrets.js* pliku. `SendGridKey`Wartość została usunięta.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Aby uzyskać więcej informacji, zobacz [Opcje wzorca](xref:fundamentals/configuration/options) i [konfiguracji](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Zainstaluj SendGrid

W tym samouczku pokazano, jak dodać powiadomienia e-mail za pośrednictwem usługi [SendGrid](https://sendgrid.com/), ale można wysyłać wiadomości e-mail przy użyciu protokołu SMTP i innych mechanizmów.

Zainstaluj `SendGrid` pakiet NuGet:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

W konsoli Menedżera pakietów wprowadź następujące polecenie:

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

W konsoli programu wprowadź następujące polecenie:

```dotnetcli
dotnet add package SendGrid
```

---

Zobacz artykuł Wprowadzenie do usługi [SendGrid bezpłatnie](https://sendgrid.com/free/) , aby zarejestrować się w celu uzyskania bezpłatnego konta SendGrid.

### <a name="implement-iemailsender"></a>Implementuj IEmailSender

Aby zaimplementować `IEmailSender` program, Utwórz *usługi/EmailSender. cs* z kodem podobnym do poniższego:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Konfigurowanie uruchamiania do obsługi poczty e-mail

Dodaj następujący kod do `ConfigureServices` metody w pliku *Startup.cs* :

* Dodaj `EmailSender` jako usługę przejściową.
* Zarejestruj `AuthMessageSenderOptions` wystąpienie konfiguracji.

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a>RegisterConfirmation szkieletu

Postępuj zgodnie z instrukcjami dla [szkieletu Identity ](xref:security/authentication/scaffold-identity) i szkieletu `RegisterConfirmation` .

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a>Rejestrowanie, Potwierdzanie poczty e-mail i resetowanie hasła

Uruchom aplikację internetową, a następnie przetestuj potwierdzenie konta i przepływ odzyskiwania hasła.

* Uruchom aplikację i Zarejestruj nowego użytkownika
* Sprawdź wiadomość e-mail z linkiem potwierdzenia konta. Jeśli nie otrzymasz wiadomości e-mail, zobacz [debugowanie poczty e-mail](#debug) .
* Kliknij link, aby potwierdzić swój adres e-mail.
* Zaloguj się przy użyciu adresu e-mail i hasła.
* Wyloguj się.

### <a name="test-password-reset"></a>Resetowanie hasła do testu

* Jeśli użytkownik jest zalogowany, wybierz pozycję **Wyloguj** .
* Wybierz link **Zaloguj** i wybierz łącze nie **pamiętasz hasła?**
* Wprowadź adres e-mail, który został użyty do zarejestrowania konta.
* Zostanie wysłana wiadomość e-mail z linkiem służącym do resetowania hasła. Sprawdź swój adres e-mail i kliknij link, aby zresetować hasło. Po pomyślnym zresetowaniu hasła możesz zalogować się przy użyciu adresu e-mail i nowego hasła.

<a name="resend"></a>

## <a name="resend-email-confirmation"></a>Potwierdzenie ponownego wysłania wiadomości e-mail

W ASP.NET Core 5,0 i nowszych wybierz link **Wyślij wiadomość e-mail z potwierdzeniem** na stronie **logowania** .

### <a name="change-email-and-activity-timeout"></a>Zmień limit czasu wiadomości e-mail i aktywności

Domyślny limit czasu braku aktywności wynosi 14 dni. Następujący kod ustawia limit czasu braku aktywności na 5 dni:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Zmień wszystkie lifespans tokenów ochrony danych

Następujący kod zmienia limit czasu tokenów ochrony danych na 3 godziny:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

Wbudowane Identity tokeny użytkownika (zobacz [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mają [jeden dzień limitu czasu](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Zmień cykl życia tokenu poczty e-mail

Domyślny token cykl życia tokenów [ Identity użytkownika](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) to [jeden dzień](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). W tej sekcji przedstawiono sposób zmiany cykl życia tokenu poczty e-mail.

Dodaj niestandardową [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) i <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Dodaj dostawcę niestandardowego do kontenera usługi:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

<a name="debug"></a>

### <a name="debug-email"></a>Debuguj wiadomość e-mail

Jeśli nie możesz uzyskać pracy z wiadomościami e-mail:

* Ustaw punkt przerwania w programie `EmailSender.Execute` , aby sprawdzić, czy `SendGridClient.SendEmailAsync` jest wywoływana.
* Utwórz [aplikację konsolową do wysyłania wiadomości e-mail](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) przy użyciu podobnego kodu do programu `EmailSender.Execute` .
* Przejrzyj stronę [działania e-mail](https://sendgrid.com/docs/User_Guide/email_activity.html) .
* Sprawdź swój folder spamu.
* Wypróbuj inny alias poczty e-mail na innym dostawcy poczty e-mail (Microsoft, Yahoo, Gmail itp.)
* Spróbuj wysłać konto do innych kont e-mail.

**Najlepszym rozwiązaniem** w zakresie zabezpieczeń jest **nieużywanie produkcyjnych** wpisów tajnych w testowaniu i programowaniu. Jeśli opublikujesz aplikację na platformie Azure, ustaw wpisy tajne SendGrid jako ustawienia aplikacji w portalu aplikacji sieci Web platformy Azure. System konfiguracji jest skonfigurowany do odczytywania kluczy ze zmiennych środowiskowych.

## <a name="combine-social-and-local-login-accounts"></a>Łączenie kont logowania społecznościowych i lokalnych

Aby ukończyć tę sekcję, należy najpierw włączyć zewnętrznego dostawcę uwierzytelniania. Zobacz [uwierzytelnianie w usługach Facebook, Google i dostawcy zewnętrznym](xref:security/authentication/social/index).

Konta lokalne i społecznościowe można łączyć przez kliknięcie linku do poczty e-mail. W następującej kolejności " RickAndMSFT@gmail.com " jest najpierw tworzony jako logowanie lokalne; można jednak najpierw utworzyć konto jako nazwę logowania w sieci społecznościowej, a następnie dodać lokalną nazwę logowania.

![Aplikacja sieci Web: RickAndMSFT@gmail.com użytkownik uwierzytelniony](accconfirm/_static/rick.png)

Kliknij link **Zarządzaj** . Zwróć uwagę na wartość 0 External (logowanie społeczne) skojarzoną z tym kontem.

![Zarządzaj widokiem](accconfirm/_static/manage.png)

Kliknij link do innej usługi logowania i zaakceptuj żądania aplikacji. Na poniższym obrazie serwis Facebook jest dostawcą zewnętrznym uwierzytelniania:

![Zarządzanie widokiem logowania zewnętrznego w serwisie Facebook](accconfirm/_static/fb.png)

Te dwa konta zostały połączone. Możesz zalogować się przy użyciu dowolnego konta. Możesz chcieć, aby użytkownicy mogli dodawać konta lokalne w przypadku, gdy usługa uwierzytelniania przy logowaniu w sieci społecznościowej nie działa, lub prawdopodobnie utraciła dostęp do konta społecznościowego.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Włącz potwierdzenie konta po stronie, w której znajdują się użytkownicy

Włączenie potwierdzenia konta w witrynie z użytkownikami powoduje zablokowanie wszystkich istniejących użytkowników. Istniejący użytkownicy są Zablokowani, ponieważ ich konta nie zostały potwierdzone. Aby obejść istniejącą blokadę użytkownika, należy użyć jednej z następujących metod:

* Zaktualizuj bazę danych, aby oznaczyć wszystkich istniejących użytkowników jako potwierdzone.
* Potwierdź istniejących użytkowników. Na przykład wysyłanie wsadowe wiadomości e-mail z linkami potwierdzającymi.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Wymagania wstępne

[.NET Core 2,2 SDK lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-no-locidentity"></a>Tworzenie aplikacji sieci Web i szkieletu Identity

Uruchom następujące polecenia, aby utworzyć aplikację sieci Web z uwierzytelnianiem.

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
> Jeśli <xref:Microsoft.AspNetCore.Identity.PasswordOptions> są skonfigurowane w programie `Startup.ConfigureServices` , konfiguracja [ `[StringLength]` atrybutu](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) może być wymagana dla `Password` właściwości na stronach szkieletowych Identity . `InputModel` `Password` Właściwość znajduje się w `Areas/Identity/Pages/Account/Register.cshtml.cs` pliku po utworzeniu szkieletu Identity .

## <a name="test-new-user-registration"></a>Testowanie rejestracji nowego użytkownika

Uruchom aplikację, wybierz łącze **zarejestruj** i zarejestruj użytkownika. W tym momencie jedynym sprawdzaniem poprawności w wiadomości e-mail jest [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atrybut. Po przesłaniu rejestracji nastąpi zalogowanie do aplikacji. W dalszej części tego samouczka kod zostanie zaktualizowany, więc nowi użytkownicy nie będą mogli się zalogować do momentu zweryfikowania ich poczty e-mail.

[!INCLUDE[](~/includes/view-identity-db.md)]

Zwróć uwagę na to, że `EmailConfirmed` pole tabeli ma wartość `False` .

Możesz chcieć ponownie użyć tej wiadomości e-mail w następnym kroku, gdy aplikacja wyśle wiadomość e-mail z potwierdzeniem. Kliknij prawym przyciskiem myszy wiersz i wybierz polecenie **Usuń** . Usunięcie aliasu e-mail ułatwia wykonanie następujących czynności.

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a>Żądaj potwierdzenia wiadomości e-mail

Najlepszym rozwiązaniem jest potwierdzenie wiadomości e-mail o nowej rejestracji użytkownika. Potwierdzenie poczty e-mail pomaga sprawdzić, czy nie personifikują kogoś innego (oznacza to, że nie zostały zarejestrowane w wiadomości e-mail innej osoby). Załóżmy, że masz forum dyskusyjne i chcesz uniemożliwić " yli@example.com " Rejestrowanie jako "" nolivetto@contoso.com . Bez potwierdzenia wiadomości e-mail " nolivetto@contoso.com " może odbierać niechciane wiadomości e-mail z aplikacji. Załóżmy, że użytkownik przypadkowo zarejestrował się jako " ylo@example.com " i nie błąd pisowni "yli". Nie będą one mogły korzystać z odzyskiwania hasła, ponieważ aplikacja nie ma poprawnego adresu e-mail. Potwierdzenie poczty e-mail zapewnia ograniczoną ochronę z botów. Potwierdzenie poczty e-mail nie zapewnia ochrony przed złośliwymi użytkownikami z wieloma kontami e-mail.

Zazwyczaj chcesz uniemożliwić nowym użytkownikom ogłaszanie danych w witrynie sieci Web przed potwierdzeniem wiadomości e-mail.

Aktualizuj `Startup.ConfigureServices`  , aby wymagać potwierdzonej wiadomości e-mail:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

`config.SignIn.RequireConfirmedEmail = true;` zapobiega logowaniu się zarejestrowanych użytkowników do momentu potwierdzenia ich wiadomości e-mail.

### <a name="configure-email-provider"></a>Konfigurowanie dostawcy poczty e-mail

W tym samouczku [SendGrid](https://sendgrid.com) jest używany do wysyłania wiadomości e-mail. Aby wysłać wiadomość e-mail, musisz mieć konto SendGrid i klucz. Możesz użyć innych dostawców poczty e-mail. Program zawiera ASP.NET Core 2. x `System.Net.Mail` , który umożliwia wysyłanie wiadomości e-mail z aplikacji. Zalecamy użycie SendGrid lub innej usługi poczty e-mail do wysyłania wiadomości e-mail. Protokół SMTP jest trudny do poprawnego zabezpieczania i konfigurowania.

Utwórz klasę, aby pobrać bezpieczny klucz poczty e-mail. Dla tego przykładu Utwórz *usługi/AuthMessageSenderOptions. cs* :

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Konfigurowanie kluczy tajnych użytkownika SendGrid

Ustaw `SendGridUser` i `SendGridKey` za pomocą [Narzędzia do zarządzania kluczami tajnymi](xref:security/app-secrets). Przykład:

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

W systemie Windows Menedżer wpisów tajnych przechowuje pary klucz/wartość w *secrets.js* w pliku w `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` katalogu.

Zawartość *secrets.jsw* pliku nie jest zaszyfrowana. Poniższe znaczniki przedstawiają *secrets.js* pliku. `SendGridKey`Wartość została usunięta.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Aby uzyskać więcej informacji, zobacz [Opcje wzorca](xref:fundamentals/configuration/options) i [konfiguracji](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Zainstaluj SendGrid

W tym samouczku pokazano, jak dodać powiadomienia e-mail za pośrednictwem usługi [SendGrid](https://sendgrid.com/), ale można wysyłać wiadomości e-mail przy użyciu protokołu SMTP i innych mechanizmów.

Zainstaluj `SendGrid` pakiet NuGet:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

W konsoli Menedżera pakietów wprowadź następujące polecenie:

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

W konsoli programu wprowadź następujące polecenie:

```dotnetcli
dotnet add package SendGrid
```

---

Zobacz artykuł Wprowadzenie do usługi [SendGrid bezpłatnie](https://sendgrid.com/free/) , aby zarejestrować się w celu uzyskania bezpłatnego konta SendGrid.

### <a name="implement-iemailsender"></a>Implementuj IEmailSender

Aby zaimplementować `IEmailSender` program, Utwórz *usługi/EmailSender. cs* z kodem podobnym do poniższego:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Konfigurowanie uruchamiania do obsługi poczty e-mail

Dodaj następujący kod do `ConfigureServices` metody w pliku *Startup.cs* :

* Dodaj `EmailSender` jako usługę przejściową.
* Zarejestruj `AuthMessageSenderOptions` wystąpienie konfiguracji.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a>Włącz potwierdzenie konta i odzyskiwanie hasła

Szablon zawiera kod służący do potwierdzenia konta i odzyskiwania hasła. Znajdź `OnPostAsync` metodę w *obszarach/ Identity /Pages/Account/register.cshtml.cs* .

Zablokuj automatyczne logowanie nowo zarejestrowanych użytkowników, dodając komentarz do następującego wiersza:

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

Pełna metoda jest pokazywana z wyróżnioną zmienionym wierszem:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a>Rejestrowanie, Potwierdzanie poczty e-mail i resetowanie hasła

Uruchom aplikację internetową, a następnie przetestuj potwierdzenie konta i przepływ odzyskiwania hasła.

* Uruchom aplikację i Zarejestruj nowego użytkownika
* Sprawdź wiadomość e-mail z linkiem potwierdzenia konta. Jeśli nie otrzymasz wiadomości e-mail, zobacz [debugowanie poczty e-mail](#debug) .
* Kliknij link, aby potwierdzić swój adres e-mail.
* Zaloguj się przy użyciu adresu e-mail i hasła.
* Wyloguj się.

### <a name="view-the-manage-page"></a>Wyświetlanie strony zarządzania

Wybierz swoją nazwę użytkownika w przeglądarce: ![ okno przeglądarki z nazwą użytkownika](accconfirm/_static/un.png)

Zostanie wyświetlona strona zarządzanie z wybraną kartą **profil** . **Wiadomość e-mail** zawiera pole wyboru informujące o potwierdzeniu wiadomości e-mail.

### <a name="test-password-reset"></a>Resetowanie hasła do testu

* Jeśli użytkownik jest zalogowany, wybierz pozycję **Wyloguj** .
* Wybierz link **Zaloguj** i wybierz łącze nie **pamiętasz hasła?**
* Wprowadź adres e-mail, który został użyty do zarejestrowania konta.
* Zostanie wysłana wiadomość e-mail z linkiem służącym do resetowania hasła. Sprawdź swój adres e-mail i kliknij link, aby zresetować hasło. Po pomyślnym zresetowaniu hasła możesz zalogować się przy użyciu adresu e-mail i nowego hasła.

## <a name="change-email-and-activity-timeout"></a>Zmień limit czasu wiadomości e-mail i aktywności

Domyślny limit czasu braku aktywności wynosi 14 dni. Następujący kod ustawia limit czasu braku aktywności na 5 dni:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Zmień wszystkie lifespans tokenów ochrony danych

Następujący kod zmienia limit czasu tokenów ochrony danych na 3 godziny:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

Wbudowane Identity tokeny użytkownika (zobacz [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mają [jeden dzień limitu czasu](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Zmień cykl życia tokenu poczty e-mail

Domyślny token cykl życia tokenów [ Identity użytkownika](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) to [jeden dzień](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). W tej sekcji przedstawiono sposób zmiany cykl życia tokenu poczty e-mail.

Dodaj niestandardową [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) i <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Dodaj dostawcę niestandardowego do kontenera usługi:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a>Potwierdzenie ponownego wysłania wiadomości e-mail

Zobacz [ten problem](https://github.com/dotnet/AspNetCore/issues/5410)w serwisie GitHub.

<a name="debug"></a>

### <a name="debug-email"></a>Debuguj wiadomość e-mail

Jeśli nie możesz uzyskać pracy z wiadomościami e-mail:

* Ustaw punkt przerwania w programie `EmailSender.Execute` , aby sprawdzić, czy `SendGridClient.SendEmailAsync` jest wywoływana.
* Utwórz [aplikację konsolową do wysyłania wiadomości e-mail](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) przy użyciu podobnego kodu do programu `EmailSender.Execute` .
* Przejrzyj stronę [działania e-mail](https://sendgrid.com/docs/User_Guide/email_activity.html) .
* Sprawdź swój folder spamu.
* Wypróbuj inny alias poczty e-mail na innym dostawcy poczty e-mail (Microsoft, Yahoo, Gmail itp.)
* Spróbuj wysłać konto do innych kont e-mail.

**Najlepszym rozwiązaniem** w zakresie zabezpieczeń jest **nieużywanie produkcyjnych** wpisów tajnych w testowaniu i programowaniu. Jeśli opublikujesz aplikację na platformie Azure, możesz ustawić wpisy tajne SendGrid jako ustawienia aplikacji w portalu aplikacji sieci Web platformy Azure. System konfiguracji jest skonfigurowany do odczytywania kluczy ze zmiennych środowiskowych.

## <a name="combine-social-and-local-login-accounts"></a>Łączenie kont logowania społecznościowych i lokalnych

Aby ukończyć tę sekcję, należy najpierw włączyć zewnętrznego dostawcę uwierzytelniania. Zobacz [uwierzytelnianie w usługach Facebook, Google i dostawcy zewnętrznym](xref:security/authentication/social/index).

Konta lokalne i społecznościowe można łączyć przez kliknięcie linku do poczty e-mail. W następującej kolejności " RickAndMSFT@gmail.com " jest najpierw tworzony jako logowanie lokalne; można jednak najpierw utworzyć konto jako nazwę logowania w sieci społecznościowej, a następnie dodać lokalną nazwę logowania.

![Aplikacja sieci Web: RickAndMSFT@gmail.com użytkownik uwierzytelniony](accconfirm/_static/rick.png)

Kliknij link **Zarządzaj** . Zwróć uwagę na wartość 0 External (logowanie społeczne) skojarzoną z tym kontem.

![Zarządzaj widokiem](accconfirm/_static/manage.png)

Kliknij link do innej usługi logowania i zaakceptuj żądania aplikacji. Na poniższym obrazie serwis Facebook jest dostawcą zewnętrznym uwierzytelniania:

![Zarządzanie widokiem logowania zewnętrznego w serwisie Facebook](accconfirm/_static/fb.png)

Te dwa konta zostały połączone. Możesz zalogować się przy użyciu dowolnego konta. Możesz chcieć, aby użytkownicy mogli dodawać konta lokalne w przypadku, gdy usługa uwierzytelniania przy logowaniu w sieci społecznościowej nie działa, lub prawdopodobnie utraciła dostęp do konta społecznościowego.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Włącz potwierdzenie konta po stronie, w której znajdują się użytkownicy

Włączenie potwierdzenia konta w witrynie z użytkownikami powoduje zablokowanie wszystkich istniejących użytkowników. Istniejący użytkownicy są Zablokowani, ponieważ ich konta nie zostały potwierdzone. Aby obejść istniejącą blokadę użytkownika, należy użyć jednej z następujących metod:

* Zaktualizuj bazę danych, aby oznaczyć wszystkich istniejących użytkowników jako potwierdzone.
* Potwierdź istniejących użytkowników. Na przykład wysyłanie wsadowe wiadomości e-mail z linkami potwierdzającymi.

::: moniker-end
