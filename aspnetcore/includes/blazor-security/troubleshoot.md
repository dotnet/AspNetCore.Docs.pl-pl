## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="cookies-and-site-data"></a>Pliki cookie i dane witryny

Pliki cookie i dane witryny mogą utrzymywać się w aktualizacjach aplikacji i zakłócać testowanie i rozwiązywanie problemów. Podczas wprowadzania zmian w kodzie aplikacji, zmian w konfiguracji konta użytkownika w u dostawcy lub dostawcy wyczyść następujące elementy:

* Pliki cookie logowania użytkownika
* Pliki cookie aplikacji
* Buforowane i przechowywane dane lokacji

Jednym z podejść zapobiegających zakłócaniu testowania i rozwiązywania problemów utrzymujących się plików cookie i danych witryny jest:

* Użyj przeglądarki do testowania, które można skonfigurować, aby usunąć wszystkie pliki cookie i dane witryny za każdym razem, gdy przeglądarka jest zamknięta.
* Zamknij przeglądarkę między wszelkimi zmianami w aplikacji, testuj użytkownika lub konfigurację dostawcy.

### <a name="run-the-server-app"></a>Uruchamianie aplikacji Serwer

Podczas testowania i rozwiązywania problemów z hostowanymi aplikacjami Blazor upewnij się, że korzystasz z aplikacji z projektu **serwera.** Na przykład w programie Visual Studio upewnij się, że projekt serwera jest wyróżniony w **Eksploratorze rozwiązań** przed uruchomieniem aplikacji przy pomocy dowolnej z następujących metod:

* Wybierz przycisk **Uruchom**.
* Użyj **debugowania** > **start debugowania** z menu.
* Naciśnij klawisz <kbd>F5</kbd>.
