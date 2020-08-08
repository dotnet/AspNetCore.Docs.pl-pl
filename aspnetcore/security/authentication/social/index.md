---
title: Uwierzytelnianie w serwisach Facebook, Google i dostawcy zewnętrznym w ASP.NET Core
author: rick-anderson
description: W tym samouczku przedstawiono sposób tworzenia aplikacji ASP.NET Core przy użyciu protokołu OAuth 2,0 z zewnętrznymi dostawcami uwierzytelniania.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/index
ms.openlocfilehash: 7dacbb00b40d2741fcbfc78ec7cdf2a67482a8da
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020473"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a>Uwierzytelnianie w serwisach Facebook, Google i dostawcy zewnętrznym w ASP.NET Core

Autorzy [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)

W tym samouczku przedstawiono sposób tworzenia aplikacji ASP.NET Core 3,0, która umożliwia użytkownikom logowanie się przy użyciu uwierzytelniania OAuth 2,0 z poświadczeniami od zewnętrznych dostawców uwierzytelniania.

Dostawcy [serwisu Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins)i [Microsoft](xref:security/authentication/microsoft-logins) są objęci następującymi sekcjami i używają początkowego projektu utworzonego w tym artykule. Inni dostawcy są dostępni w pakietach innych firm, takich jak [ASPNET. Security. OAuth. Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) i [ASPNET. Security. OpenID Connect. Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).

Umożliwienie użytkownikom logowania się przy użyciu istniejących poświadczeń:

* Jest wygodna dla użytkowników.
* Przenosi wiele kompleksów związanych z zarządzaniem procesem logowania na stronie trzeciej.

Aby zapoznać się z przykładami sposobu, w jaki nazwy logowania społecznościowe mogą na przykład przeanalizować [ruch i konwersje](https://dev.twitter.com/resources/case-studies)klientów, zobacz temat analizy przypadków według [serwisu Facebook](https://www.facebook.com/unsupportedbrowser)

## <a name="create-a-new-aspnet-core-project"></a>Utwórz nowy projekt ASP.NET Core

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Tworzenie nowego projektu.
* Wybierz pozycję **ASP.NET Core aplikacja sieci Web** i przycisk **dalej**.
* Podaj **nazwę projektu** i Potwierdź lub Zmień **lokalizację**. Wybierz pozycję **Utwórz**.
* Wybierz najnowszą wersję ASP.NET Core z listy rozwijanej (**ASP.NET Core {X. Y}**), a następnie wybierz pozycję **aplikacja sieci Web**.
* W obszarze **uwierzytelnianie**wybierz opcję **Zmień** i ustaw uwierzytelnianie na **konta poszczególnych użytkowników**. Wybierz przycisk **OK**.
* W oknie **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Utwórz**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

* Otwórz Terminal.  Aby uzyskać Visual Studio Code można otworzyć [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Zmień katalogi ( `cd` ) na folder, który będzie zawierać projekt.

* W systemie Windows uruchom następujące polecenie:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  W przypadku systemów macOS i Linux Uruchom następujące polecenie:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * `dotnet new`Polecenie tworzy nowy Razor Projekt strony w folderze *WebApp1* .
  * `-au Individual`Tworzy kod dla indywidualnego uwierzytelniania.
  * `-uld`używa LocalDB, uproszczonej wersji SQL Server Express dla systemu Windows. Pomiń `-uld` Korzystanie z oprogramowania SQLite.
  * `code`Polecenie otwiera folder *WebApp1* w nowym wystąpieniu Visual Studio Code.

---

## <a name="apply-migrations"></a>Zastosuj migracje

* Uruchom aplikację i wybierz łącze **zarejestruj** .
* Wprowadź adres e-mail i hasło dla nowego konta, a następnie wybierz pozycję **zarejestruj**.
* Postępuj zgodnie z instrukcjami, aby zastosować migracje.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>Używanie klucza tajnego do przechowywania tokenów przypisanych przez dostawców logowania

Dostawcy logowania społecznościowego przypisują **tokeny** i **identyfikatory aplikacji** podczas procesu rejestracji. Dokładne nazwy tokenów różnią się w zależności od dostawcy. Te tokeny reprezentują poświadczenia używane przez aplikację w celu uzyskania dostępu do interfejsu API. Tokeny stanowią "wpisy tajne", które mogą być połączone z konfiguracją aplikacji za pomocą [Menedżera wpisów tajnych](xref:security/app-secrets#secret-manager). Program Secret Manager jest bezpieczniejszym rozwiązaniem do przechowywania tokenów w pliku konfiguracyjnym, takim jak *appsettings.json*.

> [!IMPORTANT]
> Menedżer wpisów tajnych służy tylko do celów deweloperskich. Za pomocą [dostawcy konfiguracji Azure Key Vault](xref:security/key-vault-configuration)można przechowywać i chronić wpisy tajne środowiska Azure test i produkcyjne.

Postępuj zgodnie z instrukcjami [dotyczącymi bezpiecznego przechowywania wpisów tajnych aplikacji podczas opracowywania w ASP.NET Core](xref:security/app-secrets) tematu, aby przechowywać tokeny przypisane przez każdego dostawcę logowania poniżej.

## <a name="setup-login-providers-required-by-your-application"></a>Konfigurowanie dostawców logowania wymaganych przez aplikację

Poniższe tematy zawierają informacje dotyczące konfigurowania aplikacji do korzystania z odpowiednich dostawców:

* Instrukcje [serwisu Facebook](xref:security/authentication/facebook-logins)
* Instrukcje dla usługi [Twitter](xref:security/authentication/twitter-logins)
* Instrukcje [Google](xref:security/authentication/google-logins)
* Instrukcje [firmy Microsoft](xref:security/authentication/microsoft-logins)
* [Inne](xref:security/authentication/otherlogins) instrukcje dotyczące dostawcy

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a>Opcjonalnie Ustaw hasło

Gdy zarejestrujesz się przy użyciu zewnętrznego dostawcy logowania, nie masz hasła zarejestrowanego w aplikacji. Pozwala to uniknąć tworzenia i zapamiętywania hasła dla lokacji, ale również od zewnętrznego dostawcy logowania. Jeśli dostawca logowania zewnętrznego jest niedostępny, nie będzie można zalogować się do witryny sieci Web.

Aby utworzyć hasło i zalogować się przy użyciu poczty e-mail, która została ustawiona w procesie logowania z zewnętrznymi dostawcami:

* Wybierz link **Hello &lt; email alias &gt; ** w prawym górnym rogu, aby przejść do widoku **zarządzania** .

![Widok zarządzania aplikacjami sieci Web](index/_static/pass1a.png)

* Wybierz pozycję **Utwórz**

![Ustawianie strony hasła](index/_static/pass2a.png)

* Ustaw prawidłowe hasło i możesz go użyć do zalogowania się za pomocą poczty e-mail.

## <a name="next-steps"></a>Następne kroki

* Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/10563) w usłudze GitHub, aby uzyskać informacje na temat dostosowywania przycisków logowania.
* W tym artykule wprowadzono uwierzytelnianie zewnętrzne i wyjaśniono wymagania wstępne wymagane do dodania zewnętrznych logowań do aplikacji ASP.NET Core.
* Odwołuje się do stron specyficznych dla dostawcy, aby skonfigurować logowania dla dostawców wymaganych przez aplikację.
* Możesz chcieć utrzymać dodatkowe dane dotyczące użytkownika oraz tokeny dostępu i odświeżania. Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/additional-claims>.
