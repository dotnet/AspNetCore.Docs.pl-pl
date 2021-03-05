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
ms.openlocfilehash: eefd16dca126c9bce1efa2cdc90d4201bac09b91
ms.sourcegitcommit: 3982ff9dabb5b12aeb0a61cde2686b5253364f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102193822"
---
## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="common-errors"></a>Typowe błędy

* Nieporozumiena konfiguracja aplikacji lub Identity dostawcy (IP)

  Najczęstsze błędy są spowodowane przez niepoprawną konfigurację. Poniżej przedstawiono kilka przykładów:
  
  * W zależności od wymagań scenariusza brakujące lub nieprawidłowe uprawnienia, wystąpienie, identyfikator dzierżawy, domena dzierżawy, identyfikator klienta lub identyfikator URI przekierowania uniemożliwiają aplikacji uwierzytelnianie klientów.
  * Nieprawidłowy zakres tokenów dostępu uniemożliwia klientom dostęp do punktów końcowych interfejsu API sieci Web serwera.
  * Nieprawidłowe lub brakujące uprawnienia interfejsu API serwera uniemożliwiają klientom dostęp do punktów końcowych interfejsu API sieci Web serwera.
  
  W sekcjach konfiguracji wskazówek tego artykułu przedstawiono przykłady poprawnej konfiguracji. Uważnie Sprawdź każdą sekcję artykułu w poszukiwaniu nieodpowiedniej konfiguracji aplikacji i adresu IP.
  
  Jeśli konfiguracja jest prawidłowa:
  
  * Analizuj Dzienniki aplikacji.
  * Sprawdź ruch sieciowy między aplikacją kliencką a aplikacją IP lub serwerem za pomocą narzędzi deweloperskich w przeglądarce. Często dokładny komunikat o błędzie lub komunikat informujący o tym, co jest przyczyną problemu, jest zwracany do klienta przez aplikację IP lub serwer po wykonaniu żądania. Wskazówki dotyczące narzędzi deweloperskich znajdują się w następujących artykułach:

    * [Google Chrome](https://developers.google.com/web/tools/chrome-devtools/network) (dokumentacja firmy Google)
    * [Microsoft Edge](/microsoft-edge/devtools-guide-chromium/network/)
    * [Mozilla Firefox](https://developer.mozilla.org/docs/Tools/Network_Monitor) (dokumentacja firmy Mozilla)

  * Dekodowanie zawartości tokenu sieci Web JSON (JWT) używanego do uwierzytelniania klienta lub uzyskiwania dostępu do internetowego interfejsu API serwera w zależności od tego, gdzie występuje problem. Aby uzyskać więcej informacji, zobacz [Sprawdzanie zawartości tokenu sieci Web JSON (JWT)](#inspect-the-content-of-a-json-web-token-jwt).
  
  Zespół zawiera reaguje na informacje zwrotne i usterki w artykułach (Otwórz problem z tej sekcji opinii o **stronie** ), ale nie jest w stanie zapewnić pomocy technicznej. Dostępne są kilka publicznych forów pomocy technicznej, które ułatwiają rozwiązywanie problemów z aplikacją. Zalecamy następujące czynności:
  
  * [Stack Overflow (tag: `blazor` )](https://stackoverflow.com/questions/tagged/blazor)
  * [ASP.NET Core zespołu zapasowego](http://tattoocoder.com/aspnet-slack-sign-up/)
  * [Blazor Warunkom](https://gitter.im/aspnet/Blazor)
  
  W przypadku niezwiązanych z zabezpieczeniami, niewrażliwych i niepoufnych raportów o błędach platformy, [Otwórz problem z jednostką produktu ASP.NET Core](https://github.com/dotnet/aspnetcore/issues). Nie otwieraj problemu z jednostką produktu do momentu dokładnego zbadania przyczyny problemu i nie można jej rozwiązać samodzielnie i pomocy społeczności na forum pomocy technicznej publicznej. Jednostka produktu nie może rozwiązywać problemów z poszczególnymi aplikacjami, które są uszkodzone ze względu na prostą nieprawidłową konfigurację lub przypadki użycia obejmujące usługi innych firm. Jeśli raport jest poufny lub poufny lub zawiera opis potencjalnej luki w zabezpieczeniach produktu, którego mogą wykorzystać osoby atakujące, zobacz [Raportowanie problemów z zabezpieczeniami i błędów (repozytorium dotnet/Aspnetcore GitHub)](https://github.com/dotnet/aspnetcore/blob/main/CONTRIBUTING.md#reporting-security-issues-and-bugs).

::: moniker range=">= aspnetcore-5.0"

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

### <a name="app-upgrades"></a>Uaktualnienia aplikacji

Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji. W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych. Większość tych problemów można naprawić, wykonując następujące instrukcje:

1. Wyczyść pamięć podręczną pakietów NuGet systemu lokalnego, wykonując [`dotnet nuget locals all --clear`](/dotnet/core/tools/dotnet-nuget-locals) ją z poziomu powłoki poleceń.
1. Usuń projekt `bin` i `obj` foldery.
1. Przywróć i skompiluj projekt.
1. Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.

> [!NOTE]
> Użycie wersji pakietu niezgodnej z platformą docelową aplikacji nie jest obsługiwane. Aby uzyskać informacje na temat pakietu, użyj [galerii NuGet](https://www.nuget.org) lub [Eksploratora pakietów FuGet](https://www.fuget.org).

### <a name="run-the-server-app"></a>Uruchom aplikację serwera

Podczas testowania i rozwiązywania problemów z hostowaną Blazor aplikacją upewnij się, że aplikacja jest uruchamiana z **`Server`** projektu. Na przykład w programie Visual Studio upewnij się, że projekt serwera został wyróżniony w **Eksplorator rozwiązań** przed uruchomieniem aplikacji, korzystając z następujących metod:

* Wybierz przycisk **Run** (Uruchom).
* Użyj **debugowania**  >  **Rozpocznij debugowanie** z menu.
* Naciśnij klawisz <kbd>F5</kbd>.

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>Sprawdzanie zawartości tokenu sieci Web JSON (JWT)

Aby zdekodować token sieci Web JSON (JWT), użyj narzędzia [JWT.MS](https://jwt.ms/) firmy Microsoft. Wartości w interfejsie użytkownika nigdy nie opuszczają przeglądarki.

Przykładowy zakodowany token JWT (skrócony na potrzeby wyświetlania):

> eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1j ... bQdHBHGcQQRbW7Wmo6SWYG4V_bU55Ug_PW4pLPr20tTS8Ct7_uwy9DWrzCMzpD-EiwT5IjXwlGX3IXVjHIlX50IVIydBoPQtadvT7saKo1G5Jmutgq41o-dmz6-yBMKV2_nXA25Q

Przykładowa JWT zdekodowana przez narzędzie dla aplikacji, która uwierzytelnia się na platformie Azure AAD B2C:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1610059429,
  "nbf": 1610055829,
  "ver": "1.0",
  "iss": "https://mysiteb2c.b2clogin.com/5cc15ea8-a296-4aa3-97e4-226dcc9ad298/v2.0/",
  "sub": "5ee963fb-24d6-4d72-a1b6-889c6e2c7438",
  "aud": "70bde375-fce3-4b82-984a-b247d823a03f",
  "nonce": "b2641f54-8dc4-42ca-97ea-7f12ff4af871",
  "iat": 1610055829,
  "auth_time": 1610055822,
  "idp": "idp.com",
  "tfp": "B2C_1_signupsignin"
}.[Signature]
```
