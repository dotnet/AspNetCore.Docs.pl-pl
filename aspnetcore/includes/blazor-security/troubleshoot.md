## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="cookies-and-site-data"></a>Pliki cookie i dane lokacji

Pliki cookie i dane lokacji mogą być utrwalane między aktualizacjami aplikacji i zakłócają testowanie i rozwiązywanie problemów. W przypadku wprowadzania zmian w kodzie aplikacji należy wyczyścić następujące zmiany dotyczące konta użytkownika i konfiguracji aplikacji dostawcy:

* Pliki cookie logowania użytkownika
* Pliki cookie aplikacji
* Buforowane i przechowywane dane lokacji

Jednym z metod zapobiegania występowaniu plików cookie i danych lokacji z zakłócania testowania i rozwiązywania problemów jest:

* Konfigurowanie przeglądarki
  * Użyj przeglądarki do testowania, który można skonfigurować, aby usunąć wszystkie pliki cookie i dane z lokacji za każdym razem, gdy przeglądarka zostanie zamknięta.
  * Upewnij się, że przeglądarka została ZAMKNIĘTA ręcznie lub przez IDE między każdą zmianą aplikacji, użytkownika testowego lub konfiguracji dostawcy.
* Użyj polecenia niestandardowego, aby otworzyć przeglądarkę w trybie incognito lub prywatnym w programie Visual Studio:
  * Otwórz okno dialogowe **Przeglądaj z programem** Visual Studio. **Run**
  * Wybierz przycisk **Add** (Dodaj).
  * Podaj ścieżkę do przeglądarki w polu **program** .
  * W polu **argumenty** Podaj opcję wiersza polecenia, która jest wykorzystywana przez przeglądarkę do otwierania w trybie incognito lub prywatnym i adres URL aplikacji. Przykład:
    * Google Chrome &ndash;`--incognito --new-window https://localhost:5001`
    * Mozilla Firefox &ndash;`-private -url https://localhost:5001`
  * Podaj nazwę w polu **przyjazna nazwa** . Na przykład `Firefox Auth Testing`.
  * Wybierz przycisk **OK**.
  * Aby uniknąć konieczności wybierania profilu przeglądarki dla każdej iteracji testowania w aplikacji, Ustaw profil jako domyślny przy użyciu przycisku **Ustaw jako domyślny** .
  * Upewnij się, że przeglądarka jest zamknięta przez środowisko IDE między wszystkimi zmianami aplikacji, użytkownika testowego lub konfiguracji dostawcy.

### <a name="run-the-server-app"></a>Uruchom aplikację serwera

Podczas testowania i rozwiązywania problemów z hostowaną aplikacją Blazor upewnij się, że aplikacja jest uruchamiana z projektu **serwera** . Na przykład w programie Visual Studio upewnij się, że projekt serwera został wyróżniony w **Eksplorator rozwiązań** przed uruchomieniem aplikacji, korzystając z następujących metod:

* Wybierz przycisk **Uruchom**.
*  > Użyj **debugowania****Rozpocznij debugowanie** z menu.
* Naciśnij klawisz <kbd>F5</kbd>.
