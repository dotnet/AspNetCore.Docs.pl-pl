---
title: Konfiguracja logowania zewnętrznego konta Microsoft z ASP.NET Core
author: rick-anderson
description: Ten przykład pokazuje integrację konto Microsoft uwierzytelniania użytkowników w istniejącej aplikacji ASP.NET Core.
ms.author: riande
ms.custom: mvc
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
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 3161e4f0f735294d69dd51634b424d1ed573e615
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060303"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Konfiguracja logowania zewnętrznego konta Microsoft z ASP.NET Core

Autorzy [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)

W tym przykładzie pokazano, jak umożliwić użytkownikom zalogowanie się przy użyciu pracy, szkoły lub konto Microsoft osobistych za pomocą projektu ASP.NET Core 3,0 utworzonego na [poprzedniej stronie](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Tworzenie aplikacji w portalu dla deweloperów firmy Microsoft

* Dodaj pakiet NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) do projektu.
* Przejdź do strony [Azure Portal-rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) i Utwórz lub Zaloguj się do konto Microsoft:

Jeśli nie masz konto Microsoft, wybierz pozycję **Utwórz** . Po zalogowaniu nastąpi przekierowanie do strony **rejestracje aplikacji** :

* Wybierz **nową rejestrację**
* Wprowadź **nazwę** .
* Wybierz opcję dla **obsługiwanych typów kont** .  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
  * `MicrosoftAccount`Pakiet obsługuje rejestracje aplikacji utworzone przy użyciu opcji "konta w dowolnym katalogu organizacyjnym" lub "konta w dowolnym katalogu organizacyjnym i na kontach Microsoft".
  * Aby skorzystać z innych opcji, ustaw `AuthorizationEndpoint` i `TokenEndpoint` elementy członkowskie `MicrosoftAccountOptions` użyte do zainicjowania uwierzytelniania konta Microsoft do adresów URL wyświetlanych na stronie **punkty końcowe** rejestracji aplikacji po jej utworzeniu (dostępne przez kliknięcie punktów końcowych na stronie **Przegląd** ).
* W obszarze **Identyfikator URI przekierowania** wprowadź adres URL programowania z `/signin-microsoft` dołączonym. Na przykład `https://localhost:5001/signin-microsoft`. Schemat uwierzytelniania firmy Microsoft skonfigurowany w dalszej części tego przykładu będzie automatycznie obsługiwał żądania w `/signin-microsoft` marszrucie w celu zaimplementowania przepływu OAuth.
* Wybierz pozycję **zarejestruj**

### <a name="create-client-secret"></a>Utwórz klucz tajny klienta

* W lewym okienku wybierz pozycję **certyfikaty & wpisy tajne** .
* W obszarze wpisy **tajne klienta** wybierz pozycję **nowy klucz tajny klienta** .

  * Dodaj opis wpisu tajnego klienta.
  * Wybierz przycisk **Add** (Dodaj).

* W obszarze wpisy **tajne klienta** skopiuj wartość klucza tajnego klienta.

Segment identyfikatora URI `/signin-microsoft` jest ustawiany jako domyślne wywołanie zwrotne dostawcy uwierzytelniania firmy Microsoft. Można zmienić domyślny identyfikator URI wywołania zwrotnego podczas konfigurowania oprogramowania pośredniczącego uwierzytelniania firmy Microsoft za pośrednictwem dziedziczonej właściwości [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) klasy [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .

## <a name="store-the-microsoft-client-id-and-secret"></a>Zapisz identyfikator i klucz tajny klienta firmy Microsoft

Przechowuj ustawienia poufne, takie jak identyfikator klienta firmy Microsoft i wartości tajne przy użyciu [Menedżera wpisów tajnych](xref:security/app-secrets). W tym przykładzie wykonaj następujące czynności:

1. Zainicjuj projekt dla magazynu wpisów tajnych zgodnie z instrukcjami w obszarze [Włączanie magazynu tajnego](xref:security/app-secrets#enable-secret-storage).
1. Zapisz poufne ustawienia w lokalnym magazynie wpisów tajnych przy użyciu kluczy tajnych `Authentication:Microsoft:ClientId` i `Authentication:Microsoft:ClientSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Konfigurowanie uwierzytelniania konta Microsoft

Dodaj usługę konta Microsoft do `Startup.ConfigureServices` :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Aby uzyskać więcej informacji na temat opcji konfiguracji obsługiwanych przez uwierzytelnianie konta Microsoft, zobacz Dokumentacja interfejsu API [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) . Może to służyć do żądania różnych informacji o użytkowniku.

## <a name="sign-in-with-microsoft-account"></a>Konto Zaloguj się przy użyciu konta Microsoft

Uruchom aplikację i kliknij pozycję **Zaloguj** . Zostanie wyświetlona opcja zalogowania się do firmy Microsoft. Po kliknięciu firmy Microsoft nastąpi przekierowanie do firmy Microsoft w celu uwierzytelnienia. Po zalogowaniu się przy użyciu konta Microsoft zostanie wyświetlony monit o zezwolenie aplikacji na dostęp do informacji:

Naciśnij pozycję **tak** . nastąpi przekierowanie z powrotem do witryny sieci Web, w której można ustawić swój adres e-mail.

Jesteś teraz zalogowany przy użyciu poświadczeń firmy Microsoft:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* Jeśli dostawca kont Microsoft przekieruje Cię do strony błędu logowania, należy zwrócić uwagę na tytuły i opis błędu parametrów zapytania bezpośrednio po elemencie `#` (hasztagów) w identyfikatorze URI.

  Mimo że zostanie wyświetlony komunikat o błędzie z informacją o problemie z uwierzytelnianiem firmy Microsoft, najbardziej typową przyczyną jest to, że identyfikator URI aplikacji nie pasuje do żadnego z **identyfikatorów URI przekierowania** określonych dla danej platformy **sieci Web** .
* Jeśli Identity nie jest skonfigurowany przez wywołanie `services.AddIdentity` w `ConfigureServices` , próba uwierzytelnienia spowoduje powstanie *argumentu ArgumentException: należy podać opcję "SignInScheme"* . Szablon projektu używany w tym przykładzie zapewnia, że jest to gotowe.
* Jeśli baza danych lokacji nie została utworzona przez zastosowanie początkowej migracji, *podczas przetwarzania błędu żądania nie powiodła się operacja bazy danych* . Naciśnij pozycję **Zastosuj migracje** , aby utworzyć bazę danych i odświeżyć, aby kontynuować z powodu błędu.

## <a name="next-steps"></a>Następne kroki

* W tym artykule pokazano, jak można uwierzytelnić się w firmie Microsoft. Podobne podejście można wykonać w celu uwierzytelnienia z innymi dostawcami wymienionymi na [poprzedniej stronie](xref:security/authentication/social/index).

* Po opublikowaniu witryny sieci Web w usłudze Azure Web App Utwórz nowe wpisy tajne klienta w portalu dla deweloperów firmy Microsoft.

* Ustaw `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret` Ustawienia aplikacji i na Azure Portal. System konfiguracji jest skonfigurowany do odczytywania kluczy ze zmiennych środowiskowych.
