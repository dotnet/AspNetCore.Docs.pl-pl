# <a name="aspnet-core-health-check-sample"></a>Przykład badania kondycji rdzenia ASP.NET

W tym przykładzie przedstawiono użycie oprogramowania pośredniczącego i usług health check. W tym przykładzie przedstawiono scenariusz opisany w [health kontrole w ASP.NET tematu Core.](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks)

Aby uruchomić przykładową aplikację dla scenariusza opisanego w temacie, użyj polecenia [dotnet run](https://docs.microsoft.com/dotnet/core/tools/dotnet-run) z folderu projektu w powłoce poleceń. Przekaż przełącznik dla badanego scenariusza. Aplikacja domyślnie ma `basic` konfigurację, gdy przełącznik `dotnet run`nie jest dostarczany do .

| Scenariusz                                               | Polecenie Przykładowa aplikacja               | Opis |
| ------------------------------------------------------ | -------------------------------- | ----------- |
| Podstawowa sonda kondycji (domyślnie)                           | `dotnet run --scenario basic`    | Potwierdza, że aplikacja może przetwarzać żądania HTTP. |
| Sonda bazy danych                                         | `dotnet run --scenario db`       | Sprawdza połączenie bazy danych programu SQL Server. Zobacz [sekcji sondy bazy danych](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#database-probe) w temacie, aby uzyskać instrukcje. |
| Sondy gotowości/żywotności                              | `dotnet run --scenario liveness` | Przeprowadza kontrole stanu żywej aplikacji *(żywo)* w porównaniu z aplikacją przygotowującą się do transmisji na żywo *(gotowość).* |
| Sonda oparta na metryce (pamięć)/<br>moduł zapisujący odpowiedzi niestandardowe | `dotnet run --scenario writer`   | Sprawdza użycie pamięci i zapisuje niestandardowe JSON, gdy punkt końcowy kondycji jest zaznaczone. |
| Filtrowanie według portów                                         | `dotnet run --scenario port`     | Filtruje kontrole kondycji do danego portu. Instrukcje można znaleźć w sekcji [Filtruj według portów](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#filter-by-port) w temacie. |

Scenariusze sondy bazy danych i filtru portów wymagają dodatkowej konfiguracji. Zobacz [Health sprawdza](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) temat, aby uzyskać szczegółowe informacje.
