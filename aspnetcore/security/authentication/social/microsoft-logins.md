---
title: Konfiguracja logowania zewnętrznego konta Microsoft za pomocą ASP.NET Core
author: rick-anderson
description: W tym przykładzie pokazano integrację uwierzytelniania użytkowników konta Microsoft z istniejącą aplikacją ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 32315267e0672b0747917228f08591a15e4449f8
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277252"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Konfiguracja logowania zewnętrznego konta Microsoft za pomocą ASP.NET Core

Autorstwa [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)

W tym przykładzie pokazano, jak umożliwić użytkownikom zalogowanie się za pomocą konta Microsoft przy użyciu projektu ASP.NET Core 3.0 utworzonego na [poprzedniej stronie](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Tworzenie aplikacji w portalu Microsoft Developer Portal

* Dodaj pakiet [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet do projektu.
* Przejdź do strony [Azure portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) i utwórz lub zaloguj się do konta Microsoft:

Jeśli nie masz konta Microsoft, wybierz pozycję **Utwórz jedno**. Po zalogowaniu się użytkownik zostanie przekierowany na stronę **Rejestracje aplikacji:**

* Wybierz **nową rejestrację**
* Wprowadź **nazwę**.
* Wybierz opcję dla **obsługiwanych typów kont**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* W obszarze **Przekierowanie identyfikatora URI**wprowadź adres URL rozwoju z `/signin-microsoft` dołączeniem. Na przykład `https://localhost:5001/signin-microsoft`. Schemat uwierzytelniania firmy Microsoft skonfigurowany w dalszej `/signin-microsoft` części tego przykładu będzie automatycznie obsługiwał żądania w drodze w celu zaimplementowania przepływu OAuth.
* Wybierz **zarejestruj się**

### <a name="create-client-secret"></a>Tworzenie klucza tajnego klienta

* W lewym okienku wybierz pozycję **Certyfikaty & wpisy tajne**.
* W obszarze **Wpisy tajne klienta**wybierz pozycję **Nowy klucz tajny klienta**

  * Dodaj opis klucza tajnego klienta.
  * Wybierz przycisk **Add** (Dodaj).

* W obszarze **Wpisy tajne klienta**skopiuj wartość klucza tajnego klienta.

Segment identyfikatora `/signin-microsoft` URI jest ustawiony jako domyślne wywołanie zwrotne dostawcy uwierzytelniania firmy Microsoft. Domyślny identyfikator URI wywołania zwrotnego można zmienić podczas konfigurowania oprogramowania pośredniczącego uwierzytelniania firmy Microsoft za pośrednictwem dziedziczonej właściwości [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)

## <a name="store-the-microsoft-client-id-and-secret"></a>Przechowywanie identyfikatora klienta firmy Microsoft i klucza tajnego

Przechowuj poufne ustawienia, takie jak identyfikator klienta firmy Microsoft i wartości tajne, za pomocą [menedżera tajnego](xref:security/app-secrets). W tym przykładzie należy wykonać następujące czynności:

1. Inicjowanie projektu do przechowywania w celu uzyskania instrukcji w [usłudze Włącz magazyn tajny](xref:security/app-secrets#enable-secret-storage).
1. Przechowuj poufne ustawienia w lokalnym magazynie tajnym z tajnymi kluczami `Authentication:Microsoft:ClientId` i: `Authentication:Microsoft:ClientSecret`

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Konfigurowanie uwierzytelniania konta Microsoft

Dodaj usługę Konto Microsoft `Startup.ConfigureServices`do :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Aby uzyskać więcej informacji na temat opcji konfiguracji obsługiwanych przez uwierzytelnianie konta Microsoft, zobacz odwołanie do interfejsu API [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) Może to służyć do żądania różnych informacji o użytkowniku.

## <a name="sign-in-with-microsoft-account"></a>Logowanie się za pomocą konta Microsoft

Uruchom aplikację i kliknij pozycję **Zaloguj się**. Pojawi się opcja logowania się za pomocą firmy Microsoft. Po kliknięciu firmy Microsoft użytkownik zostanie przekierowany do firmy Microsoft w celu uwierzytelnienia. Po zalogowaniu się za pomocą konta Microsoft zostanie wyświetlony monit o umożliwienie aplikacji dostępu do informacji:

Naciśnij **pozycję Tak,** a zostaniesz przekierowany z powrotem do witryny sieci Web, na której możesz ustawić swój adres e-mail.

Jesteś teraz zalogowany przy użyciu poświadczeń firmy Microsoft:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* Jeśli dostawca konta Microsoft przekierowuje użytkownika do strony błędu logowania, zanotuj `#` parametry ciągu kwerendy o tytule błędu i opisie bezpośrednio po (hashtag) w identyfikatorze Uri.

  Chociaż komunikat o błędzie wydaje się wskazywać na problem z uwierzytelnianiem firmy Microsoft, najczęstszą przyczyną jest aplikacja Uri nie pasujące do żadnej z **identyfikatorów URI przekierowania** określonych dla platformy **sieci Web.**
* Jeśli tożsamość nie jest `services.AddIdentity` skonfigurowana przez `ConfigureServices`wywołanie, próba uwierzytelnienia *spowoduje, że argumentexception: musi zostać podana opcja "SignInScheme".* Szablon projektu użyty w tym przykładzie zapewnia, że jest to zrobione.
* Jeśli baza danych lokacji nie została utworzona przez zastosowanie migracji początkowej, podczas przetwarzania błędu żądania zostanie wyświetlony *komunikat A database.* Naciśnij pozycję **Zastosuj migracje,** aby utworzyć bazę danych i odświeżyć, aby przejść obok błędu.

## <a name="next-steps"></a>Następne kroki

* W tym artykule pokazano, jak można uwierzytelniać się w firmie Microsoft. Podobne podejście można stosować w celu uwierzytelnienia z innymi dostawcami wymienionymi na [poprzedniej stronie](xref:security/authentication/social/index).

* Po opublikowaniu witryny sieci Web w aplikacji sieci Web platformy Azure utwórz nowe wpisy tajne klienta w portalu microsoft developer portal.

* Ustaw `Authentication:Microsoft:ClientId` ustawienia `Authentication:Microsoft:ClientSecret` aplikacji i jako w witrynie Azure portal. System konfiguracji jest skonfigurowany do odczytu kluczy ze zmiennych środowiskowych.
