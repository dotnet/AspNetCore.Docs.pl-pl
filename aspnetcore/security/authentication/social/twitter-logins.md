---
title: Konfiguracja logowania zewnętrznego usługi Twitter za pomocą ASP.NET Core
author: rick-anderson
description: W tym samouczku przedstawiono integrację uwierzytelniania użytkownika konta usługi Twitter z istniejącą aplikacją ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/twitter-logins
ms.openlocfilehash: c6498704214de5e805c9bf57033529d4acc5fd3e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775794"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Konfiguracja logowania zewnętrznego usługi Twitter za pomocą ASP.NET Core

Autorzy [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)

Ten przykład pokazuje, jak umożliwić użytkownikom [zalogowanie](https://dev.twitter.com/web/sign-in/desktop-browser) się przy użyciu konta usługi Twitter za pomocą przykładowego projektu ASP.NET Core 3,0 utworzonego na [poprzedniej stronie](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Tworzenie aplikacji w usłudze Twitter

* Dodaj pakiet NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) do projektu.

* Przejdź do [https://apps.twitter.com/](https://apps.twitter.com/) i zaloguj się. Jeśli nie masz jeszcze konta usługi Twitter, Użyj linku Utwórz **[teraz](https://twitter.com/signup)** , aby go utworzyć.

* Wybierz pozycję **Utwórz aplikację**. Wypełnij pola **Nazwa aplikacji**, **Opis aplikacji** i publiczny identyfikator URI **witryny sieci Web** (może to być czas tymczasowy do momentu zarejestrowania nazwy domeny):

* Zaznacz pole wyboru obok pozycji **Włącz logowanie za pomocą usługi Twitter**

* Microsoft. AspNetCore.Identity Domyślnie program wymaga od użytkowników posiadania adresu e-mail. Przejdź do karty **uprawnienia** , kliknij przycisk **Edytuj** , a następnie zaznacz pole wyboru obok **żądania adresu e-mail od użytkowników**.

* Wprowadź swój identyfikator URI rozwoju `/signin-twitter` z dołączonym do pola **adresy URL wywołania zwrotnego** ( `https://webapp128.azurewebsites.net/signin-twitter`na przykład:). Schemat uwierzytelniania usługi Twitter skonfigurowany w dalszej części tego przykładu będzie automatycznie `/signin-twitter` obsługiwał żądania w marszrucie w celu zaimplementowania przepływu OAuth.

  > [!NOTE]
  > Segment `/signin-twitter` identyfikatora URI jest ustawiany jako domyślne wywołanie zwrotne dostawcy uwierzytelniania w usłudze Twitter. Domyślny identyfikator URI wywołania zwrotnego można zmienić podczas konfigurowania oprogramowania pośredniczącego uwierzytelniania usługi Twitter za pomocą dziedziczonej właściwości [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .

* Wypełnij resztę formularza i wybierz pozycję **Utwórz**. Wyświetlane są nowe szczegóły aplikacji:

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Przechowywanie klucza i wpisu tajnego interfejsu API klienta usługi Twitter

Przechowuj ustawienia poufne, takie jak klucz interfejsu API użytkownika usługi Twitter i wpis tajny przy użyciu [Menedżera wpisów tajnych](xref:security/app-secrets). W tym przykładzie wykonaj następujące czynności:

1. Zainicjuj projekt dla magazynu wpisów tajnych zgodnie z instrukcjami w obszarze [Włączanie magazynu tajnego](xref:security/app-secrets#enable-secret-storage).
1. Zapisz ustawienia poufne w lokalnym magazynie wpisów tajnych przy użyciu kluczy `Authentication:Twitter:ConsumerKey` tajnych `Authentication:Twitter:ConsumerSecret`i:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Te tokeny można znaleźć na karcie **klucze i tokeny dostępu** po utworzeniu nowej aplikacji usługi Twitter:

## <a name="configure-twitter-authentication"></a>Konfigurowanie uwierzytelniania w usłudze Twitter

Dodaj usługę Twitter do `ConfigureServices` metody w pliku *Startup.cs* :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Zobacz Dokumentacja interfejsu API [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) , aby uzyskać więcej informacji na temat opcji konfiguracji obsługiwanych przez uwierzytelnianie w usłudze Twitter. Może to służyć do żądania różnych informacji o użytkowniku.

## <a name="sign-in-with-twitter"></a>Zaloguj się przy użyciu usługi Twitter

Uruchom aplikację i wybierz pozycję **Zaloguj się**. Zostanie wyświetlona opcja zalogowania się przy użyciu usługi Twitter:

Klikanie przekierowania w usłudze **Twitter** do usługi Twitter w celu uwierzytelnienia:

Po wprowadzeniu poświadczeń usługi Twitter nastąpi przekierowanie do witryny sieci Web, w której można ustawić swój adres e-mail.

Użytkownik jest obecnie zalogowany przy użyciu poświadczeń usługi Twitter:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* **Tylko ASP.NET Core 2. x:** Jeśli Identity nie jest skonfigurowany przez `services.AddIdentity` wywołanie `ConfigureServices`w, próba uwierzytelnienia spowoduje powstanie *argumentu ArgumentException: należy podać opcję "SignInScheme"*. Szablon projektu używany w tym przykładzie zapewnia, że jest to gotowe.
* Jeśli baza danych lokacji nie została utworzona przez zastosowanie początkowej migracji, *podczas przetwarzania błędu żądania nie powiodła się operacja bazy danych* . Naciśnij pozycję **Zastosuj migracje** , aby utworzyć bazę danych i odświeżyć, aby kontynuować z powodu błędu.

## <a name="next-steps"></a>Następne kroki

* W tym artykule pokazano, jak można uwierzytelnić się w usłudze Twitter. Podobne podejście można wykonać w celu uwierzytelnienia z innymi dostawcami wymienionymi na [poprzedniej stronie](xref:security/authentication/social/index).

* Po opublikowaniu witryny sieci Web w usłudze Azure Web App należy zresetować ją `ConsumerSecret` w portalu dla deweloperów w usłudze Twitter.

* Ustaw ustawienia `Authentication:Twitter:ConsumerKey` aplikacji `Authentication:Twitter:ConsumerSecret` i na Azure Portal. System konfiguracji jest skonfigurowany do odczytywania kluczy ze zmiennych środowiskowych.
