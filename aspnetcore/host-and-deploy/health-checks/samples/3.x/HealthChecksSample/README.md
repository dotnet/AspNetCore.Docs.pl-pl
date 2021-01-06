# <a name="aspnet-core-health-check-sample"></a>Przykład ASP.NET Core Sprawdzanie kondycji

Ten przykład ilustruje sposób korzystania z sprawdzania kondycji oprogramowania i usług. Ten przykład ilustruje scenariusz opisany w temacie [Sprawdzanie kondycji w ASP.NET Core](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) tematu.

Aby uruchomić przykładową aplikację dla scenariusza opisanego w temacie, użyj polecenia [dotnet Run](https://docs.microsoft.com/dotnet/core/tools/dotnet-run) z folderu projektu w powłoce poleceń. Przekaż przełącznik dla scenariusza, który jest przeszukany. Aplikacja jest domyślnie ustawiona na `basic` konfigurację, gdy nie podano przełącznika `dotnet run` .

| Scenariusz                                               | Polecenie przykładowej aplikacji               | Opis |
| ------------------------------------------------------ | -------------------------------- | ----------- |
| Podstawowa sonda kondycji (domyślnie)                           | `dotnet run --scenario basic`    | Potwierdza, że aplikacja może przetwarzać żądania HTTP. |
| Sonda bazy danych                                         | `dotnet run --scenario db`       | Sprawdza SQL Server połączenie z bazą danych. Instrukcje można znaleźć w sekcji [sondowanie bazy danych](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#database-probe) w temacie. |
| Sondy gotowości/na żywo                              | `dotnet run --scenario liveness` | Sprawdza, czy stan aktywnej aplikacji (*Live*), a następnie aplikację, która zostanie przygotowana na żywo (*gotowość*). |
| Sonda oparta na pomiarach (pamięć)/<br>niestandardowy moduł zapisujący odpowiedzi | `dotnet run --scenario writer`   | Sprawdza użycie pamięci i zapisuje niestandardowy kod JSON po sprawdzeniu punktu końcowego kondycji. |
| Filtruj według portu                                         | `dotnet run --scenario port`     | Filtruje kontrolę kondycji w danym porcie. Instrukcje można znaleźć w sekcji [filtrowanie według portu](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#filter-by-port) w temacie. |

Scenariusze sondowania bazy danych i filtru portów wymagają dodatkowej konfiguracji. Aby uzyskać szczegółowe informacje, zobacz temat [Sprawdzanie kondycji](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) .
