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
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Konfiguracja zewnętrznego logowania facebooka w ASP.NET Core

Autorstwa [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

Ten samouczek z przykładami kodu pokazuje, jak umożliwić użytkownikom zalogowanie się za pomocą swojego konta na Facebooku przy użyciu przykładowego projektu ASP.NET core 3.0 utworzonego na [poprzedniej stronie](xref:security/authentication/social/index). Zaczynamy od utworzenia identyfikatora aplikacji Facebooka, wykonując [oficjalne kroki.](https://developers.facebook.com)

## <a name="create-the-app-in-facebook"></a>Tworzenie aplikacji na Facebooku

* Dodaj pakiet [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet do projektu.

* Przejdź do strony [aplikacji Facebook Developers](https://developers.facebook.com/apps/) i zaloguj się. Jeśli nie masz jeszcze konta na Facebooku, użyj linku **Zarejestruj się na Facebooku** na stronie logowania, aby je utworzyć.  Gdy masz konto na Facebooku, postępuj zgodnie z instrukcjami, aby zarejestrować się jako programista Facebooka.

* Z menu **Moje aplikacje** wybierz **pozycję Utwórz aplikację,** aby utworzyć nowy identyfikator aplikacji.

   ![Facebook dla programistów portal otwarty w przeglądarce Microsoft Edge](index/_static/FBMyApps.png)

* Wypełnij formularz i naciśnij przycisk **Utwórz identyfikator aplikacji.**

  ![Tworzenie nowego formularza identyfikatora aplikacji](index/_static/FBNewAppId.png)

* Na nowej karcie aplikacji wybierz pozycję **Dodaj produkt**.  Na karcie **logowania na Facebooku** kliknij pozycję **Skonfiguruj** 

  ![Strona Ustawienia produktu](index/_static/FBProductSetup.png)

* Kreator **szybkiego startu** uruchamia się z **wybierz platformę** jako pierwszą stronę. Omiń kreatora, klikając link **Ustawienia** **logowania FaceBook** w menu w lewym dolnym dolnym roku:

  ![Pomiń szybki start](index/_static/FBSkipQuickStart.png)

* Przedstawiono Ci stronę **Ustawienia klienta OAuth:**

  ![Strona Ustawienia OAuth klienta](index/_static/FBOAuthSetup.png)

* Wprowadź identyfikator URI rozwoju z */signin-facebook* dołączone do **prawidłowego OAuth Redirect URIs** pola (na przykład: `https://localhost:44320/signin-facebook`). Uwierzytelnianie na Facebooku skonfigurowane w dalszej części tego samouczka będzie automatycznie obsługiwać żądania na trasie */signin-facebook* w celu zaimplementowania przepływu OAuth.

> [!NOTE]
> Identyfikator URI */signin-facebook* jest ustawiony jako domyślne wywołanie zwrotne dostawcy uwierzytelniania Facebooka. Domyślny identyfikator URI wywołania zwrotnego można zmienić podczas konfigurowania oprogramowania pośredniczącego uwierzytelniania Facebooka za pośrednictwem dziedziczonej właściwości [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [FacebookOptions.](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)

* Kliknij **pozycję Zapisz zmiany**.

* Kliknij pozycję**Podstawowe** **łącze Ustawienia** > w lewej nawigacji.

  Na tej stronie zanotuj `App ID` `App Secret`swój i swój . W następnej sekcji dodasz zarówno aplikację ASP.NET Core:

* Podczas wdrażania witryny należy ponownie odwiedzić stronę konfiguracji **logowania do Facebooka** i zarejestrować nowy publiczny identyfikator URI.

## <a name="store-the-facebook-app-id-and-secret"></a>Przechowywanie identyfikatora aplikacji Facebook i klucza tajnego

Przechowuj poufne ustawienia, takie jak identyfikator aplikacji Facebook i tajne wartości za pomocą [Programu Secret Manager](xref:security/app-secrets). W tym przykładzie należy wykonać następujące czynności:

1. Inicjowanie projektu do przechowywania w celu uzyskania instrukcji w [usłudze Włącz magazyn tajny](xref:security/app-secrets#enable-secret-storage).
1. Przechowuj poufne ustawienia w lokalnym magazynie tajnym z tajnymi kluczami `Authentication:Facebook:AppId` i: `Authentication:Facebook:AppSecret`

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Konfigurowanie uwierzytelniania na Facebooku

Dodaj usługę Facebook `ConfigureServices` w metodzie w pliku *Startup.cs:*

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Zaloguj się za pomocą Facebooka

* Uruchom aplikację i wybierz pozycję **Zaloguj się**. 
* W obszarze **Użyj innej usługi, aby się zalogować.** wybierz Pozycję Facebook.
* Zostaniesz przekierowany do **Facebooka** w celu uwierzytelnienia.
* Wprowadź swoje dane logowania do Facebooka.
* Zostaniesz przekierowany z powrotem do witryny, gdzie możesz ustawić swój adres e-mail.

Jesteś teraz zalogowany przy użyciu swoich danych logowania na Facebooku:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>Reagowanie na anulowanie autoryzacji zewnętrznego logowania

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>może podać ścieżkę przekierowania do agenta użytkownika, gdy użytkownik nie zatwierdzi żądanego żądania autoryzacji.

Poniższy kod `AccessDeniedPath` ustawia `"/AccessDeniedPathInfo"`na:

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

Zalecamy, `AccessDeniedPath` aby strona zawierała następujące informacje:

*  Uwierzytelnianie zdalne zostało anulowane.
* Ta aplikacja wymaga uwierzytelniania.
* Aby ponownie spróbować zalogować się, wybierz łącze Zaloguj.

### <a name="test-accessdeniedpath"></a>Test AccessDeniedPath

* Przejdź do [facebook.com](https://www.facebook.com/)
* Jeśli jesteś zalogowany, musisz się wylogować.
* Uruchom aplikację i wybierz opcję Logowania na Facebooku.
* Wybierz **opcję Nie teraz**. Zostaniesz przekierowany do `AccessDeniedPath` określonej strony.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Więcej informacji na temat opcji konfiguracji obsługiwanych przez uwierzytelnianie na Facebooku można znaleźć w aplikacji [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API. Opcji konfiguracji można użyć do:

* Żądaj różnych informacji o użytkowniku.
* Dodaj argumenty ciągu kwerendy, aby dostosować środowisko logowania.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* **ASP.NET tylko Core 2.x:** Jeśli tożsamość nie jest `services.AddIdentity` skonfigurowana przez `ConfigureServices`wywołanie, próba uwierzytelnienia *spowoduje, że argumentexception: musi zostać podana opcja "SignInScheme".* Szablon projektu użyty w tym samouczku zapewnia, że jest to zrobione.
* Jeśli baza danych lokacji nie została utworzona przez zastosowanie migracji początkowej, podczas przetwarzania błędu *żądania zostanie ściągnięta operacja bazy danych.* Naciśnij pozycję **Zastosuj migracje,** aby utworzyć bazę danych i odświeżyć, aby przejść obok błędu.

## <a name="next-steps"></a>Następne kroki

* W tym artykule pokazano, jak można uwierzytelniać się za pomocą Facebooka. Podobne podejście można stosować w celu uwierzytelnienia z innymi dostawcami wymienionymi na [poprzedniej stronie](xref:security/authentication/social/index).

* Po opublikowaniu witryny sieci Web w aplikacji `AppSecret` sieci Web platformy Azure należy zresetować witrynę w portalu deweloperów Facebooka.

* Ustaw `Authentication:Facebook:AppId` ustawienia `Authentication:Facebook:AppSecret` aplikacji i jako w witrynie Azure portal. System konfiguracji jest skonfigurowany do odczytu kluczy ze zmiennych środowiskowych.
