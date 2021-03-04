---
title: Konfiguracja zewnętrznego logowania do usługi Google w ASP.NET Core
author: rick-anderson
description: W tym samouczku przedstawiono integrację uwierzytelniania użytkownika konta Google z istniejącą aplikacją ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/18/2021
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
uid: security/authentication/google-logins
ms.openlocfilehash: 181ce87e8085839e0fcc0d77010c588ef7a290b1
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110134"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>Konfiguracja zewnętrznego logowania do usługi Google w ASP.NET Core

Autorzy [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)

W tym samouczku pokazano, jak umożliwić użytkownikom logowanie się za pomocą konta Google przy użyciu projektu ASP.NET Core 3,0 utworzonego na [poprzedniej stronie](xref:security/authentication/social/index).

## <a name="create-a-google-api-console-project-and-client-id"></a>Tworzenie projektu konsoli interfejsu API firmy Google i identyfikatora klienta

* Dodaj pakiet [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) NuGet do aplikacji.
* Postępuj zgodnie ze wskazówkami zawartymi w temacie [Integrowanie usługi Google Sign-In z aplikacją sieci Web](https://developers.google.com/identity/sign-in/web/sign-in) (dokumentacją Google).
* Na stronie **poświadczenia** w [konsoli Google](https://console.developers.google.com/apis/credentials)wybierz pozycję **Utwórz poświadczenia**  >  **Identyfikator klienta OAuth**.
* W oknie dialogowym **Typ aplikacji** wybierz pozycję **aplikacja sieci Web**. Podaj **nazwę** aplikacji.
* W sekcji **autoryzowane adresy URI przekierowania** wybierz pozycję **Dodaj identyfikator URI** , aby ustawić identyfikator URI przekierowania. Przykładowy identyfikator URI przekierowania: `https://localhost:{PORT}/signin-google` , gdzie `{PORT}` symbol zastępczy jest portem aplikacji.
* Wybierz przycisk **Utwórz** .
* Zapisz **Identyfikator klienta** i **klucz tajny klienta** do użycia w konfiguracji aplikacji.
* Podczas wdrażania lokacji:
  * Zaktualizuj identyfikator URI przekierowania aplikacji w **konsoli Google** do WDROŻONEGO identyfikatora URI przekierowania aplikacji.
  * Utwórz nową rejestrację usługi Google API w **konsoli Google** dla aplikacji produkcyjnej przy użyciu identyfikatora URI przekierowania produkcji.

## <a name="store-the-google-client-id-and-secret"></a>Zapisz identyfikator i klucz tajny klienta Google

Przechowuj ustawienia poufne, takie jak identyfikator klienta Google i wartości tajne przy użyciu [Menedżera wpisów tajnych](xref:security/app-secrets). W tym przykładzie wykonaj następujące czynności:

1. Zainicjuj projekt dla magazynu wpisów tajnych zgodnie z instrukcjami w obszarze [Włączanie magazynu tajnego](xref:security/app-secrets#enable-secret-storage).
1. Zapisz poufne ustawienia w lokalnym magazynie wpisów tajnych przy użyciu kluczy tajnych `Authentication:Google:ClientId` i `Authentication:Google:ClientSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Poświadczenia interfejsu API i użycie można zarządzać w [konsoli interfejsu API](https://console.developers.google.com/apis/dashboard).

## <a name="configure-google-authentication"></a>Skonfiguruj uwierzytelnianie Google

Dodaj usługę Google do `Startup.ConfigureServices` :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Zaloguj się przy użyciu usługi Google

* Uruchom aplikację i kliknij pozycję **Zaloguj**. Zostanie wyświetlona opcja zalogowania się za pomocą usługi Google.
* Kliknij przycisk **Google** , który przekierowuje do usługi Google w celu uwierzytelnienia.
* Po wprowadzeniu poświadczeń Google nastąpi przekierowanie z powrotem do witryny sieci Web.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>Więcej informacji o opcjach konfiguracji obsługiwanych przez uwierzytelnianie Google można znaleźć w dokumentacji interfejsu API. Może to służyć do żądania różnych informacji o użytkowniku.

## <a name="change-the-default-callback-uri"></a>Zmień domyślny identyfikator URI wywołania zwrotnego

Segment identyfikatora URI `/signin-google` jest ustawiany jako domyślne wywołanie zwrotne dostawcy uwierzytelniania Google. Domyślny identyfikator URI wywołania zwrotnego można zmienić podczas konfigurowania oprogramowania pośredniczącego usługi Google Authentication za pośrednictwem odziedziczonej <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType> właściwości <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> klasy.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* Jeśli logowanie nie działa i nie pojawiają się żadne błędy, przełącz się do trybu deweloperskiego, aby ułatwić debugowanie problemu.
* Jeśli Identity nie jest skonfigurowany przez wywołanie `services.AddIdentity` w `ConfigureServices` , próba uwierzytelnienia wyników w *argumencieexception: należy podać opcję "SignInScheme"*. Szablon projektu używany w tym samouczku zapewnia, że jest to gotowe.
* Jeśli baza danych lokacji nie została utworzona przez zastosowanie początkowej migracji, podczas *przetwarzania błędu żądania nie można wykonać operacji bazy danych* . Wybierz pozycję **Zastosuj migracje** , aby utworzyć bazę danych, a następnie Odśwież stronę, aby kontynuować z powodu błędu.

## <a name="next-steps"></a>Następne kroki

* W tym artykule pokazano, jak można uwierzytelniać za pomocą usługi Google. Podobne podejście można wykonać w celu uwierzytelnienia z innymi dostawcami wymienionymi na [poprzedniej stronie](xref:security/authentication/social/index).
* Po opublikowaniu aplikacji na platformie Azure zresetuj ją `ClientSecret` w konsoli interfejsu API firmy Google.
* Ustaw `Authentication:Google:ClientId` `Authentication:Google:ClientSecret` Ustawienia aplikacji i na Azure Portal. System konfiguracji jest skonfigurowany do odczytywania kluczy ze zmiennych środowiskowych.
