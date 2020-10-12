## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="cookies-and-site-data"></a>Pliki cookie i dane lokacji

Pliki cookie i dane lokacji mogą być utrwalane między aktualizacjami aplikacji i zakłócają testowanie i rozwiązywanie problemów. W przypadku wprowadzania zmian w kodzie aplikacji należy wyczyścić następujące zmiany dotyczące konta użytkownika i konfiguracji aplikacji dostawcy:

* Pliki cookie logowania użytkownika
* Pliki cookie aplikacji
* Buforowane i przechowywane dane lokacji

Jednym z metod zapobiegania występowaniu plików cookie i danych lokacji z zakłócania testowania i rozwiązywania problemów jest:

* Konfigurowanie przeglądarki
  * Użyj przeglądarki do testowania, który można skonfigurować, aby usunąć wszystkie pliki cookie i dane z lokacji za każdym razem, gdy przeglądarka zostanie zamknięta.
  * Upewnij się, że przeglądarka została ZAMKNIĘTA ręcznie lub przez IDE w celu dowolnych zmian w konfiguracji aplikacji, użytkownika testowego lub dostawcy.
* Użyj polecenia niestandardowego, aby otworzyć przeglądarkę w trybie incognito lub prywatnym w programie Visual Studio:
  * Otwórz okno dialogowe **Przeglądaj z programem** Visual Studio. **Run**
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

Podczas testowania i rozwiązywania problemów z hostowaną aplikacją Blazor upewnij się, że aplikacja jest uruchamiana z **`Server`** projektu. Na przykład w programie Visual Studio upewnij się, że projekt serwera został wyróżniony w **Eksplorator rozwiązań** przed uruchomieniem aplikacji, korzystając z następujących metod:

* Wybierz przycisk **Run** (Uruchom).
* Użyj **debugowania**  >  **Rozpocznij debugowanie** z menu.
* Naciśnij klawisz <kbd>F5</kbd>.

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>Sprawdzanie zawartości tokenu sieci Web JSON (JWT)

Aby zdekodować token sieci Web JSON (JWT), użyj narzędzia [JWT.MS](https://jwt.ms/) firmy Microsoft. Wartości w interfejsie użytkownika nigdy nie opuszczają przeglądarki.
