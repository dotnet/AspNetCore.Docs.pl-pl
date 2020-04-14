---
title: Zewnętrzna konfiguracja logowania na Twitterze z ASP.NET Core
author: rick-anderson
description: W tym samouczku pokazano integrację uwierzytelniania użytkowników konta Twitter z istniejącą aplikacją ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277291"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Zewnętrzna konfiguracja logowania na Twitterze z ASP.NET Core

Autorstwa [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)

W tym przykładzie pokazano, jak umożliwić użytkownikom zalogowanie się za pomocą [konta na Twitterze](https://dev.twitter.com/web/sign-in/desktop-browser) przy użyciu przykładowego projektu ASP.NET Core 3.0 utworzonego na [poprzedniej stronie](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Tworzenie aplikacji w Aplikacji Twitter

* Dodaj pakiet [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet do projektu.

* Przejdź [https://apps.twitter.com/](https://apps.twitter.com/) do i zaloguj się. Jeśli nie masz jeszcze konta na Twitterze, użyj linku **[Zarejestruj się teraz,](https://twitter.com/signup)** aby je utworzyć.

* Wybierz **pozycję Utwórz aplikację**. Wypełnij **nazwę aplikacji,** **opis aplikacji** i publiczny identyfikator URI **witryny sieci Web** (może to być tymczasowe, dopóki nie zarejestrujesz nazwy domeny):

* Zaznacz pole wyboru obok pozycji **Włącz logowanie za pomocą twittera**

* Microsoft.AspNetCore.Identity wymaga, aby użytkownicy mieli domyślnie adres e-mail. Przejdź do karty **Uprawnienia,** kliknij przycisk **Edytuj** i zaznacz pole wyboru Obok pozycji **Żądanie adresu e-mail od użytkowników**.

* Wprowadź identyfikator URI `/signin-twitter` rozwoju z dołączonym do pola `https://webapp128.azurewebsites.net/signin-twitter` **Adresy URL wywołania zwrotnego** (na przykład: ). Schemat uwierzytelniania Twitter skonfigurowany w dalszej części `/signin-twitter` tego przykładu będzie automatycznie obsługiwać żądania na trasie w celu zaimplementowania przepływu OAuth.

  > [!NOTE]
  > Segment identyfikatora `/signin-twitter` URI jest ustawiony jako domyślne wywołanie zwrotne dostawcy uwierzytelniania Twittera. Domyślny identyfikator URI wywołania zwrotnego można zmienić podczas konfigurowania oprogramowania pośredniczącego uwierzytelniania Twittera za pośrednictwem dziedziczonej właściwości [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [TwitterOptions.](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)

* Wypełnij pozostałą część formularza i wybierz pozycję **Utwórz**. Wyświetlane są nowe szczegóły aplikacji:

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Przechowywanie klucza API konsumenta Twittera i klucz tajny

Przechowuj poufne ustawienia, takie jak klucz interfejsu API konsumenta Twittera i klucz tajny za pomocą [Secret Managera](xref:security/app-secrets). W tym przykładzie należy wykonać następujące czynności:

1. Inicjowanie projektu do przechowywania w celu uzyskania instrukcji w [usłudze Włącz magazyn tajny](xref:security/app-secrets#enable-secret-storage).
1. Przechowuj poufne ustawienia w lokalnym magazynie tajnym z kluczami wpisów `Authentication:Twitter:ConsumerKey` tajnych i: `Authentication:Twitter:ConsumerSecret`

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Tokeny te można znaleźć na karcie **Klucze i tokeny dostępu** po utworzeniu nowej aplikacji Twitter:

## <a name="configure-twitter-authentication"></a>Konfigurowanie uwierzytelniania twitterowego

Dodaj usługę Twitter `ConfigureServices` w metodzie *w pliku Startup.cs:*

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Zobacz odwołanie interfejsu API [TwitterOptions,](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) aby uzyskać więcej informacji na temat opcji konfiguracji obsługiwanych przez uwierzytelnianie na Twitterze. Może to służyć do żądania różnych informacji o użytkowniku.

## <a name="sign-in-with-twitter"></a>Zaloguj się za pomocą Twittera

Uruchom aplikację i wybierz pozycję **Zaloguj się**. Pojawi się opcja logowania się za pomocą Twittera:

Kliknięcie na **Twitter** przekierowuje do Twitter do uwierzytelniania:

Po wprowadzeniu poświadczeń twittera zostaniesz przekierowany z powrotem do witryny sieci Web, na której możesz ustawić swój adres e-mail.

Jesteś teraz zalogowany przy użyciu poświadczeń Twitter:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* **ASP.NET tylko Core 2.x:** Jeśli tożsamość nie jest `services.AddIdentity` skonfigurowana przez `ConfigureServices`wywołanie, próba uwierzytelnienia *spowoduje, że argumentexception: musi zostać podana opcja "SignInScheme".* Szablon projektu użyty w tym przykładzie zapewnia, że jest to zrobione.
* Jeśli baza danych lokacji nie została utworzona przez zastosowanie migracji początkowej, podczas przetwarzania błędu żądania zostanie wyświetlony *komunikat A database.* Naciśnij pozycję **Zastosuj migracje,** aby utworzyć bazę danych i odświeżyć, aby przejść obok błędu.

## <a name="next-steps"></a>Następne kroki

* W tym artykule pokazano, jak można uwierzytelnić się za pomocą Twittera. Podobne podejście można stosować w celu uwierzytelnienia z innymi dostawcami wymienionymi na [poprzedniej stronie](xref:security/authentication/social/index).

* Po opublikowaniu witryny sieci Web w aplikacji `ConsumerSecret` sieci Web platformy Azure należy zresetować witrynę w portalu dewelopera Twitter.

* Ustaw `Authentication:Twitter:ConsumerKey` ustawienia `Authentication:Twitter:ConsumerSecret` aplikacji i jako w witrynie Azure portal. System konfiguracji jest skonfigurowany do odczytu kluczy ze zmiennych środowiskowych.
