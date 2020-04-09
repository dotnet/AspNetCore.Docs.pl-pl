---
title: Uwierzytelnianie Facebooka, Google i zewnętrznego dostawcy w ASP.NET Core
author: rick-anderson
description: W tym samouczku pokazano, jak utworzyć aplikację core ASP.NET przy użyciu OAuth 2.0 z zewnętrznymi dostawcami uwierzytelniania.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/authentication/social/index
ms.openlocfilehash: c698edbd85d665509366287b1dcad08e276e71cc
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78668043"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a>Uwierzytelnianie Facebooka, Google i zewnętrznego dostawcy w ASP.NET Core

Autorstwa [Valeriy Novytskyy](https://github.com/01binary) i [Rick Anderson](https://twitter.com/RickAndMSFT)

W tym samouczku pokazano, jak utworzyć ASP.NET core 3.0 aplikacji, która umożliwia użytkownikom zalogować się przy użyciu OAuth 2.0 z poświadczeniami od zewnętrznych dostawców uwierzytelniania.

[Dostawcy usług Facebook,](xref:security/authentication/facebook-logins) [Twitter,](xref:security/authentication/twitter-logins) [Google](xref:security/authentication/google-logins)i [Microsoft](xref:security/authentication/microsoft-logins) są omówione w poniższych sekcjach i korzystają z projektu startowego utworzonego w tym artykule. Inni dostawcy są dostępni w pakietach innych firm, takich jak [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) i [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).

Umożliwienie użytkownikom logowania się przy użyciu istniejących poświadczeń:

* Jest wygodny dla użytkowników.
* Przenosi wiele zawiłości zarządzania procesem logowania na stronę trzecią.

Przykłady, w jaki sposób logowania społecznościowe mogą napędzać ruch i konwersje klientów, można znaleźć w studiach przypadku [na Facebooku](https://www.facebook.com/unsupportedbrowser) i [Twitterze.](https://dev.twitter.com/resources/case-studies)

## <a name="create-a-new-aspnet-core-project"></a>Tworzenie nowego projektu ASP.NET core

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Tworzenie nowego projektu.
* Wybierz **ASP.NET Core Web Application** i **Dalej**.
* Podaj **nazwę projektu** i potwierdź lub zmień **lokalizację**. Wybierz **pozycję Utwórz**.
* Wybierz najnowszą wersję ASP.NET Core w rozwijanej **(ASP.NET Core {X.Y}),** a następnie wybierz opcję **Aplikacja sieci Web**.
* W obszarze **Uwierzytelnianie**wybierz **pozycję Zmień** i ustaw uwierzytelnianie na Indywidualne **konta użytkowników**. Kliknij przycisk **OK**.
* W oknie **Tworzenie nowej ASP.NET podstawowej aplikacji sieci Web** wybierz pozycję **Utwórz**.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Kod programu Visual Studio / Visual Studio dla komputerów Mac](#tab/visual-studio-code+visual-studio-mac)

* Otwórz terminal.  W programie Visual Studio Code można otworzyć [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Zmień katalogi`cd`( ) na folder, który będzie zawierał projekt.

* W systemie Windows uruchom następujące polecenie:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  W przypadku systemów macOS i Linux uruchom następujące polecenie:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * Polecenie `dotnet new` tworzy nowy projekt Razor Pages w folderze *WebApp1.*
  * `-au Individual`tworzy kod do indywidualnego uwierzytelniania.
  * `-uld`używa LocalDB, lekka wersja programu SQL Server Express dla systemu Windows. Pomiń `-uld` użycie funkcji SQLite.
  * Polecenie `code` otwiera folder *WebApp1* w nowym wystąpieniu programu Visual Studio Code.

---

## <a name="apply-migrations"></a>Stosowanie migracji

* Uruchom aplikację i wybierz **łącze Zarejestruj.**
* Wprowadź adres e-mail i hasło dla nowego konta, a następnie wybierz pozycję **Zarejestruj**.
* Postępuj zgodnie z instrukcjami, aby zastosować migracje.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>Używanie programu SecretManager do przechowywania tokenów przypisanych przez dostawców logowania

Dostawcy logowania społecznościowego **przypisują** identyfikator aplikacji i tokeny **klucza tajnego aplikacji** podczas procesu rejestracji. Dokładne nazwy tokenów różnią się w zależności od dostawcy. Tokeny te reprezentują poświadczenia używane przez aplikację do uzyskiwania dostępu do interfejsu API. Tokeny stanowią "tajemnice", które mogą być połączone z konfiguracją aplikacji za pomocą [Secret Manager](xref:security/app-secrets#secret-manager). Secret Manager jest bezpieczniejszą alternatywą dla przechowywania tokenów w pliku konfiguracyjnym, takich jak *appsettings.json*.

> [!IMPORTANT]
> Secret Manager służy wyłącznie do celów programisty. Wpisy tajne testów i produkcji platformy Azure można przechowywać i chronić za pomocą [dostawcy konfiguracji usługi Azure Key Vault.](xref:security/key-vault-configuration)

Wykonaj kroki opisane w [bezpieczne przechowywanie wpisów tajnych aplikacji w rozwoju w ASP.NET tematu Core](xref:security/app-secrets) do przechowywania tokenów przypisanych przez każdego dostawcę logowania poniżej.

## <a name="setup-login-providers-required-by-your-application"></a>Konfigurowanie dostawców logowania wymaganych przez aplikację

Użyj następujących tematów, aby skonfigurować aplikację do korzystania z odpowiednich dostawców:

* Instrukcje [na Facebooku](xref:security/authentication/facebook-logins)
* Instrukcje [twitterowe](xref:security/authentication/twitter-logins)
* Instrukcje [Google](xref:security/authentication/google-logins)
* Instrukcje [firmy Microsoft](xref:security/authentication/microsoft-logins)
* [Inne](xref:security/authentication/otherlogins) instrukcje dla dostawców

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a>Opcjonalnie ustaw hasło

Po zarejestrowaniu się u zewnętrznego dostawcy logowania nie masz hasła zarejestrowanego w aplikacji. To łagodzi cię od tworzenia i zapamiętywania hasła do witryny, ale także uzależnia cię od zewnętrznego dostawcy logowania. Jeśli zewnętrzny dostawca logowania jest niedostępny, nie będzie można zalogować się do witryny sieci Web.

Aby utworzyć hasło i zalogować się przy użyciu poczty e-mail ustawionej podczas procesu logowania u zewnętrznych dostawców:

* Wybierz **łącze &lt;&gt; Hello email alias** w prawym górnym rogu, aby przejść do widoku **Zarządzaj.**

![Aplikacja sieci Web Zarządzaj widokiem](index/_static/pass1a.png)

* Wybierz **pozycję Utwórz**

![Ustawianie strony hasła](index/_static/pass2a.png)

* Ustaw prawidłowe hasło i możesz użyć go do zalogowania się za pomocą poczty e-mail.

## <a name="next-steps"></a>Następne kroki

* Zobacz [ten problem GitHub,](https://github.com/dotnet/AspNetCore.Docs/issues/10563) aby uzyskać informacje na temat dostosowywania przycisków logowania.
* W tym artykule wprowadzono uwierzytelnianie zewnętrzne i wyjaśniono wymagania wstępne wymagane do dodawania zewnętrznych loginów do aplikacji ASP.NET Core.
* Strony specyficzne dla dostawcy odwołań do konfigurowania loginów dla dostawców wymaganych przez aplikację.
* Można utrwalić dodatkowe dane dotyczące użytkownika i ich tokenów dostępu i odświeżania. Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/additional-claims>.
