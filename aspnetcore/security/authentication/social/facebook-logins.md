---
title: Konfiguracja logowania zewnętrznego w serwisie Facebook w ASP.NET Core
author: rick-anderson
description: Samouczek z przykładami kodu demonstrujących integrację uwierzytelniania użytkownika konta w serwisie Facebook w istniejącej aplikacji ASP.NET Core.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/facebook-logins
ms.openlocfilehash: be0b655645fd2bd0eab9f9c30a65485f386cead3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053361"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Konfiguracja logowania zewnętrznego w serwisie Facebook w ASP.NET Core

Autorzy [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

Ten samouczek z przykładami kodu pokazuje, jak umożliwić użytkownikom zalogowanie się za pomocą konta w serwisie Facebook przy użyciu przykładowego projektu ASP.NET Core 3,0 utworzonego na [poprzedniej stronie](xref:security/authentication/social/index). Zacznij od utworzenia identyfikatora aplikacji Facebook, wykonując [czynności urzędowe](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Tworzenie aplikacji w usłudze Facebook

* Dodaj pakiet NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) do projektu.

* Przejdź do strony [aplikacji deweloperzy serwisu Facebook](https://developers.facebook.com/apps/) i zaloguj się. Jeśli nie masz jeszcze konta w serwisie Facebook, utwórz je za pomocą linku rejestracji w usłudze **Facebook** na stronie logowania.  Gdy masz konto w serwisie Facebook, postępuj zgodnie z instrukcjami, aby zarejestrować się jako deweloper w serwisie Facebook.

* Z menu **Moje aplikacje** wybierz pozycję **Utwórz aplikację** , aby utworzyć nowy identyfikator aplikacji.

   ![Portal usługi Facebook dla deweloperów otwarty w przeglądarce Microsoft Edge](index/_static/FBMyApps.png)

* Wypełnij formularz i naciśnij przycisk **Utwórz identyfikator aplikacji** .

  ![Utwórz nowy formularz identyfikatora aplikacji](index/_static/FBNewAppId.png)

* Na nowej karcie aplikacji wybierz pozycję **Dodaj produkt** .  Na karcie **Logowanie w serwisie Facebook** kliknij pozycję **Konfiguruj** . 

  ![Strona konfiguracji produktu](index/_static/FBProductSetup.png)

* Zostanie uruchomiony Kreator **szybkiego startu** z **wybieraniem platformy** jako pierwszej strony. Pomiń kreatora teraz, klikając link **Ustawienia** **logowania w serwisie Facebook** w menu po lewej stronie:

  ![Pomiń Szybki start](index/_static/FBSkipQuickStart.png)

* Zostanie wyświetlona strona **ustawień uwierzytelniania OAuth klienta** :

  ![Strona ustawień uwierzytelniania OAuth klienta](index/_static/FBOAuthSetup.png)

* Wprowadź identyfikator URI programowania z */SignIn-facebooką* dołączoną do **prawidłowego pola URI przekierowania OAuth** (na przykład: `https://localhost:44320/signin-facebook` ). Uwierzytelnianie w serwisie Facebook skonfigurowane w dalszej części tego samouczka będzie automatycznie obsługiwać żądania w marszrucie */SignIn-Facebook* w celu zaimplementowania przepływu OAuth.

> [!NOTE]
> Identyfikator URI */SignIn-Facebook* jest ustawiany jako domyślne wywołanie zwrotne dostawcy uwierzytelniania w serwisie Facebook. Domyślny identyfikator URI wywołania zwrotnego można zmienić podczas konfigurowania oprogramowania pośredniczącego uwierzytelniania w serwisie Facebook za pośrednictwem dziedziczonej właściwości [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .

* Kliknij przycisk **Zapisz zmiany** .

* Kliknij link **Ustawienia**  >  **podstawowe** na lewym pasku nawigacyjnym.

  Na tej stronie Zanotuj swoje `App ID` i Twoje `App Secret` . Do aplikacji ASP.NET Core należy dodać w następnej sekcji:

* Podczas wdrażania witryny należy ponownie odwiedzić stronę instalatora logowania do **serwisu Facebook** i zarejestrować nowy publiczny identyfikator URI.

## <a name="store-the-facebook-app-id-and-secret"></a>Przechowywanie identyfikatora i wpisu tajnego aplikacji w serwisie Facebook

Przechowuj ustawienia poufne, takie jak identyfikator aplikacji w serwisie Facebook i wartości tajne przy użyciu [Menedżera wpisów tajnych](xref:security/app-secrets). W tym przykładzie wykonaj następujące czynności:

1. Zainicjuj projekt dla magazynu wpisów tajnych zgodnie z instrukcjami w obszarze [Włączanie magazynu tajnego](xref:security/app-secrets#enable-secret-storage).
1. Zapisz poufne ustawienia w lokalnym magazynie wpisów tajnych przy użyciu kluczy tajnych `Authentication:Facebook:AppId` i `Authentication:Facebook:AppSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Konfigurowanie uwierzytelniania w usłudze Facebook

Dodaj usługę Facebook do `ConfigureServices` metody w pliku *Startup.cs* :

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Logowanie za pomocą usługi Facebook

* Uruchom aplikację i wybierz pozycję **Zaloguj się** . 
* W obszarze **Użyj innej usługi do zalogowania się.** wybierz pozycję Facebook.
* Nastąpi przekierowanie do usługi **Facebook** w celu uwierzytelnienia.
* Wprowadź swoje poświadczenia w serwisie Facebook.
* Nastąpi przekierowanie z powrotem do witryny, w której można ustawić swój adres e-mail.

Zalogowano Cię przy użyciu poświadczeń usługi Facebook:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>Reagowanie na Anulowanie autoryzacji logowania zewnętrznego

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> Jeśli użytkownik nie zatwierdza żądanego zapotrzebowania na autoryzację, może podać ścieżkę przekierowania do agenta użytkownika.

Poniższy kod ustawia `AccessDeniedPath` `"/AccessDeniedPathInfo"` :

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

Zalecamy, aby `AccessDeniedPath` strona zawierała następujące informacje:

*  Zdalne uwierzytelnianie zostało anulowane.
* Ta aplikacja wymaga uwierzytelniania.
* Aby ponownie spróbować zalogować się, wybierz link logowania.

### <a name="test-accessdeniedpath"></a>Test AccessDeniedPath

* Przejdź do [Facebook.com](https://www.facebook.com/)
* Jeśli użytkownik jest zalogowany, musisz się wylogować.
* Uruchom aplikację i wybierz pozycję Logowanie do serwisu Facebook.
* Wybierz pozycję **nie teraz** . Nastąpi przekierowanie do określonej `AccessDeniedPath` strony.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Zobacz Dokumentacja interfejsu API [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) , aby uzyskać więcej informacji na temat opcji konfiguracji obsługiwanych przez uwierzytelnianie w serwisie Facebook. Opcje konfiguracji mogą służyć do:

* Zażądaj innych informacji o użytkowniku.
* Dodaj argumenty ciągu zapytania, aby dostosować środowisko logowania.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* **Tylko ASP.NET Core 2. x:** Jeśli Identity nie jest skonfigurowany przez wywołanie `services.AddIdentity` w `ConfigureServices` , próba uwierzytelnienia spowoduje powstanie *argumentu ArgumentException: należy podać opcję "SignInScheme"* . Szablon projektu używany w tym samouczku zapewnia, że jest to gotowe.
* Jeśli baza danych lokacji nie została utworzona przez zastosowanie początkowej migracji, podczas *przetwarzania błędu żądania nie można wykonać operacji bazy danych* . Naciśnij pozycję **Zastosuj migracje** , aby utworzyć bazę danych i odświeżyć, aby kontynuować z powodu błędu.

## <a name="next-steps"></a>Następne kroki

* W tym artykule pokazano, jak można uwierzytelnić się w usłudze Facebook. Podobne podejście można wykonać w celu uwierzytelnienia z innymi dostawcami wymienionymi na [poprzedniej stronie](xref:security/authentication/social/index).

* Po opublikowaniu witryny sieci Web w usłudze Azure Web App należy zresetować ją `AppSecret` w portalu dla deweloperów serwisu Facebook.

* Ustaw `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` Ustawienia aplikacji i na Azure Portal. System konfiguracji jest skonfigurowany do odczytywania kluczy ze zmiennych środowiskowych.
