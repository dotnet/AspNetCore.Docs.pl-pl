---
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
ms.openlocfilehash: 4e7c0e9b0a164e0181af5d6baaedf0669c1c06aa
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552531"
---
## <a name="troubleshoot"></a>Rozwiązywanie problemów

::: moniker range=">= aspnetcore-5.0"

### <a name="common-errors"></a>Typowe błędy

* Nieautoryzowany klient dla usługi AAD

  > Informacja: uwierzytelnianie Microsoft. AspNetCore. Authorization. DefaultAuthorizationService [2] nie powiodło się. Te wymagania nie zostały spełnione: DenyAnonymousAuthorizationRequirement: wymaga uwierzytelnionego użytkownika.

  Błąd wywołania zwrotnego logowania z usługi AAD:

  * Błąd: `unauthorized_client`
  * Zharmonizowan `AADB2C90058: The provided application is not configured to allow public clients.`

  Aby rozwiązać ten problem:

  1. W Azure Portal uzyskaj dostęp do [manifestu aplikacji](/azure/active-directory/develop/reference-app-manifest).
  1. Ustaw [ `allowPublicClient` atrybut](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) na `null` lub `true` .

::: moniker-end

### <a name="cookies-and-site-data"></a>Cookies i dane lokacji

Cookiedane i lokacje mogą być utrwalane między aktualizacjami aplikacji i zakłócają testowanie i rozwiązywanie problemów. W przypadku wprowadzania zmian w kodzie aplikacji należy wyczyścić następujące zmiany dotyczące konta użytkownika i konfiguracji aplikacji dostawcy:

* Logowanie użytkownika cookie
* Aplikacje cookie s
* Buforowane i przechowywane dane lokacji

Jednym z metod zapobiegania powstawaniu cookie danych i wymawianiu przez proces testowania i rozwiązywania problemów jest:

* Konfigurowanie przeglądarki
  * Użyj przeglądarki do testowania, który można skonfigurować, aby usunąć wszystkie cookie i dane witryny za każdym razem, gdy przeglądarka zostanie zamknięta.
  * Upewnij się, że przeglądarka została ZAMKNIĘTA ręcznie lub przez IDE w celu dowolnych zmian w konfiguracji aplikacji, użytkownika testowego lub dostawcy.
* Użyj polecenia niestandardowego, aby otworzyć przeglądarkę w trybie incognito lub prywatnym w programie Visual Studio:
  * Otwórz okno dialogowe **Przeglądaj z programem** Visual Studio. 
  * Wybierz przycisk **Add** (Dodaj).
  * Podaj ścieżkę do przeglądarki w polu **program** . Następujące ścieżki plików wykonywalnych są typowymi lokalizacjami instalacji dla systemu Windows 10. Jeśli przeglądarka jest zainstalowana w innej lokalizacji lub nie korzystasz z systemu Windows 10, podaj ścieżkę do pliku wykonywalnego przeglądarki.
    * Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
    * Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`
    * Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`
  * W polu **argumenty** Podaj opcję wiersza polecenia, która jest wykorzystywana przez przeglądarkę do otwierania w trybie incognito lub prywatnym. Niektóre przeglądarki wymagają adresu URL aplikacji.
    * Microsoft Edge: Użyj `-inprivate` .
    * Google Chrome: Użyj `--incognito --new-window {URL}` , gdzie symbol zastępczy `{URL}` jest adresem URL do otwarcia (na przykład `https://localhost:5001` ).
    * Mozilla Firefox: Użyj `-private -url {URL}` , gdzie symbol zastępczy `{URL}` jest adresem URL do otwarcia (na przykład `https://localhost:5001` ).
  * Podaj nazwę w polu **przyjazna nazwa** . Na przykład `Firefox Auth Testing`.
  * Wybierz przycisk **OK**.
  * Aby uniknąć konieczności wybierania profilu przeglądarki dla każdej iteracji testowania w aplikacji, Ustaw profil jako domyślny przy użyciu przycisku **Ustaw jako domyślny** .
  * Upewnij się, że w środowisku IDE jest ZAMKNIĘTA przeglądarka, aby wprowadzić zmiany w konfiguracji aplikacji, użytkownika testowego lub dostawcy.

### <a name="run-the-server-app"></a>Uruchom aplikację serwera

Podczas testowania i rozwiązywania problemów z hostowaną Blazor aplikacją upewnij się, że aplikacja jest uruchamiana z **`Server`** projektu. Na przykład w programie Visual Studio upewnij się, że projekt serwera został wyróżniony w **Eksplorator rozwiązań** przed uruchomieniem aplikacji, korzystając z następujących metod:

* Wybierz przycisk **Run** (Uruchom).
* Użyj **debugowania**  >  **Rozpocznij debugowanie** z menu.
* Naciśnij klawisz <kbd>F5</kbd>.

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>Sprawdzanie zawartości tokenu sieci Web JSON (JWT)

Aby zdekodować token sieci Web JSON (JWT), użyj narzędzia [JWT.MS](https://jwt.ms/) firmy Microsoft. Wartości w interfejsie użytkownika nigdy nie opuszczają przeglądarki.
