---
title: Rejestrowanie i Diagnostyka w ASP.NET CoreSignalR
author: anurse
description: Dowiedz się, jak zbierać diagnostykę z SignalR aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: devx-track-csharp, signalr
ms.date: 06/12/2020
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
uid: signalr/diagnostics
ms.openlocfilehash: 922b2ca0aa7933e1010db7ca319631766ffbf753
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130538"
---
# <a name="logging-and-diagnostics-in-aspnet-core-no-locsignalr"></a>Rejestrowanie i Diagnostyka w ASP.NET CoreSignalR

Według [Andrew Stanton-pielęgniarki](https://twitter.com/anurse)

Ten artykuł zawiera wskazówki dotyczące zbierania danych diagnostycznych z SignalR aplikacji ASP.NET Core w celu ułatwienia rozwiązywania problemów.

## <a name="server-side-logging"></a>Rejestrowanie po stronie serwera

> [!WARNING]
> Dzienniki po stronie serwera mogą zawierać poufne informacje z aplikacji. **Nigdy nie** Publikuj nieprzetworzonych dzienników z aplikacji produkcyjnych na forach publicznych, takich jak GitHub.

Ponieważ SignalR jest częścią ASP.NET Core, używa systemu rejestrowania ASP.NET Core. W konfiguracji domyślnej SignalR dzienniki są bardzo mało informacji, ale można je skonfigurować. Szczegółowe informacje na temat konfigurowania rejestrowania ASP.NET Core można znaleźć w dokumentacji dotyczącej [rejestrowania ASP.NET Core](xref:fundamentals/logging/index#configuration) .

SignalRużywa dwóch kategorii rejestratora:

* `Microsoft.AspNetCore.SignalR`: W przypadku dzienników związanych z protokołami centrów, aktywowanie centrów, wywoływanie metod i innych działań związanych z centrum.
* `Microsoft.AspNetCore.Http.Connections`: W przypadku dzienników związanych z transportami, takich jak obiekty WebSockets, długie sondowanie, zdarzenia wysłane przez serwer i infrastruktura niskiego poziomu SignalR .

Aby włączyć szczegółowe dzienniki SignalR , należy skonfigurować obie powyższe prefiksy na `Debug` poziomie w *appsettings.jsna* pliku, dodając następujące elementy do `LogLevel` podsekcji w `Logging` :

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

Możesz również skonfigurować ten kod w kodzie w `CreateWebHostBuilder` metodzie:

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

Jeśli nie korzystasz z konfiguracji opartej na notacji JSON, ustaw następujące wartości konfiguracji w systemie konfiguracji:

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

Zapoznaj się z dokumentacją systemu konfiguracyjnego, aby określić sposób określania zagnieżdżonych wartości konfiguracyjnych. Na przykład w przypadku używania zmiennych środowiskowych `_` zamiast `:` (na przykład) są używane dwa znaki `Logging__LogLevel__Microsoft.AspNetCore.SignalR` .

Zalecamy użycie `Debug` poziomu podczas zbierania bardziej szczegółowych informacji diagnostycznych dla aplikacji. Na `Trace` poziomie powstaje Diagnostyka bardzo niskiego poziomu i jest rzadko wymagana do diagnozowania problemów w aplikacji.

## <a name="access-server-side-logs"></a>Dostęp do dzienników po stronie serwera

Sposób dostępu do dzienników po stronie serwera zależy od środowiska, w którym jest uruchomiony program.

### <a name="as-a-console-app-outside-iis"></a>Jako Aplikacja konsolowa poza usługami IIS

Jeśli używasz programu w aplikacji konsolowej, [Rejestrator konsoli](xref:fundamentals/logging/index#console) powinien być domyślnie włączony. SignalRDzienniki będą wyświetlane w konsoli programu.

### <a name="within-iis-express-from-visual-studio"></a>W IIS Express z programu Visual Studio

Program Visual Studio Wyświetla dane wyjściowe dziennika w oknie **danych wyjściowych** . Wybierz opcję listy rozwijanej **ASP.NET Core serwera sieci Web** .

### <a name="azure-app-service"></a>Azure App Service

Włącz opcję **Rejestrowanie aplikacji (system plików)** w sekcji **dzienniki diagnostyki** w portalu Azure App Service i skonfiguruj **poziom** na `Verbose` . Dzienniki powinny być dostępne w usłudze **przesyłania strumieniowego dzienników** oraz w dziennikach w systemie plików App Service. Aby uzyskać więcej informacji, zobacz [przesyłanie strumieniowe dzienników Azure](xref:fundamentals/logging/index#azure-log-streaming).

### <a name="other-environments"></a>Inne środowiska

Jeśli aplikacja jest wdrażana w innym środowisku (na przykład Docker, Kubernetes lub Windows Service), zobacz, <xref:fundamentals/logging/index> Aby uzyskać więcej informacji na temat konfigurowania dostawców rejestrowania odpowiednie dla danego środowiska.

## <a name="javascript-client-logging"></a>Rejestrowanie klientów JavaScript

> [!WARNING]
> Dzienniki po stronie klienta mogą zawierać poufne informacje z aplikacji. **Nigdy nie** Publikuj nieprzetworzonych dzienników z aplikacji produkcyjnych na forach publicznych, takich jak GitHub.

Korzystając z klienta JavaScript, można skonfigurować opcje rejestrowania za pomocą `configureLogging` metody w `HubConnectionBuilder` :

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.

W poniższej tabeli przedstawiono poziomy dziennika dostępne dla klienta JavaScript. Ustawienie poziomu dziennika na jedną z tych wartości umożliwia rejestrowanie na tym poziomie i wszystkich poziomów powyżej niego w tabeli.

| Poziom | Opis |
| ----- | ----------- |
| `None` | Żadne komunikaty nie są rejestrowane. |
| `Critical` | Komunikaty wskazujące niepowodzenie w całej aplikacji. |
| `Error` | Komunikaty wskazujące niepowodzenie w bieżącej operacji. |
| `Warning` | Komunikaty wskazujące na problem niekrytyczny. |
| `Information` | Komunikaty informacyjne. |
| `Debug` | Komunikaty diagnostyczne przydatne do debugowania. |
| `Trace` | Bardzo szczegółowe komunikaty diagnostyczne przeznaczone do diagnozowania określonych problemów. |

Po skonfigurowaniu szczegółowości dzienniki zostaną zapisane w konsoli przeglądarki (lub w standardowym wyjściu w aplikacji NodeJS).

Jeśli chcesz wysłać dzienniki do niestandardowego systemu rejestrowania, możesz dostarczyć obiekt JavaScript implementujący `ILogger` interfejs. Jedyną metodą, która musi zostać wdrożona `log` , jest, która pobiera poziom zdarzenia i komunikat skojarzony ze zdarzeniem. Na przykład:

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>Rejestrowanie klienta platformy .NET

> [!WARNING]
> Dzienniki po stronie klienta mogą zawierać poufne informacje z aplikacji. **Nigdy nie** Publikuj nieprzetworzonych dzienników z aplikacji produkcyjnych na forach publicznych, takich jak GitHub.

Aby pobrać dzienniki z klienta .NET, można użyć `ConfigureLogging` metody z `HubConnectionBuilder` . Działa tak samo jak `ConfigureLogging` Metoda w systemach `WebHostBuilder` i `HostBuilder` . Można skonfigurować tych samych dostawców rejestrowania, których używasz w ASP.NET Core. Należy jednak ręcznie zainstalować i włączyć pakiety NuGet dla poszczególnych dostawców rejestrowania.

Aby dodać rejestrowanie klienta platformy .NET do Blazor WebAssembly aplikacji, zobacz <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging> .

### <a name="console-logging"></a>Rejestrowanie konsoli

Aby włączyć rejestrowanie konsoli, Dodaj pakiet [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) . Następnie użyj metody, `AddConsole` Aby skonfigurować Rejestrator konsoli:

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>Rejestrowanie okna danych wyjściowych debugowania

Możesz również skonfigurować dzienniki, aby przejść do okna **danych wyjściowych** w programie Visual Studio. Zainstaluj pakiet [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) i Użyj `AddDebug` metody:

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>Inni dostawcy rejestrowania

SignalRobsługuje innych dostawców rejestrowania, takich jak Serilog, SEQ, NLog lub dowolny inny system rejestrowania, który integruje się z programem `Microsoft.Extensions.Logging` . Jeśli system rejestrowania zapewnia, możesz `ILoggerProvider` zarejestrować go za pomocą `AddProvider` :

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>Szczegółowość kontroli

Jeśli rejestrujesz się z innych miejsc w aplikacji, zmiana poziomu domyślnego na wartość `Debug` może być zbyt pełna. Możesz użyć filtru, aby skonfigurować poziom rejestrowania dla SignalR dzienników. Można to zrobić w kodzie w taki sam sposób jak na serwerze:

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>Ślady sieci

> [!WARNING]
> Śledzenie sieci zawiera pełną zawartość każdej wiadomości wysyłanej przez aplikację. **Nigdy nie** Publikuj nieprzetworzonych danych śledzenia sieci z aplikacji produkcyjnych na forach publicznych, takich jak GitHub.

W przypadku wystąpienia problemu śledzenie sieci może czasami dostarczyć wiele przydatnych informacji. Jest to szczególnie przydatne, jeśli zamierzasz rozwiązać problem z naszym narzędziem do śledzenia problemów.

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Zbieranie danych śledzenia sieci za pomocą programu Fiddler (preferowana opcja)

Ta metoda działa w przypadku wszystkich aplikacji.

Programu Fiddler to bardzo zaawansowane narzędzie do zbierania śladów HTTP. Zainstaluj ją z [Telerik.com/Fiddler](https://www.telerik.com/fiddler), uruchom ją, a następnie uruchom aplikację i Odtwórz problem. Programu Fiddler jest dostępny dla systemu Windows, a dostępne wersje beta dla macOS i Linux.

Jeśli łączysz się przy użyciu protokołu HTTPS, należy wykonać kilka dodatkowych kroków, aby programu Fiddler można było odszyfrować ruch HTTPS. Aby uzyskać więcej informacji, zobacz [dokumentację programu Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

Po zebraniu śledzenia można wyeksportować śledzenie, wybierając pozycję **plik**  >  **Zapisz**  >  **wszystkie sesje** z paska menu.

![Eksportowanie wszystkich sesji z programu Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Zbieraj dane śledzenia sieci z tcpdump (tylko macOS i Linux)

Ta metoda działa w przypadku wszystkich aplikacji.

Można zbierać pierwotne ślady TCP przy użyciu tcpdump, uruchamiając następujące polecenie z poziomu powłoki poleceń. Jeśli wystąpi błąd uprawnień, może być konieczne lub nastąpić odtworzenie `root` prefiksu polecenia `sudo` :

```console
tcpdump -i [interface] -w trace.pcap
```

Zamień na `[interface]` interfejs sieciowy, który ma być przechwytywany. Zwykle jest to coś podobnego `/dev/eth0` (dla standardowego interfejsu Ethernet) lub `/dev/lo0` (dla ruchu hosta lokalnego). Aby uzyskać więcej informacji, zobacz `tcpdump` stronę Man w systemie hosta.

## <a name="collect-a-network-trace-in-the-browser"></a>Zbieranie danych śledzenia sieci w przeglądarce

Ta metoda działa tylko w przypadku aplikacji opartych na przeglądarce.

Większość przeglądarek Narzędzia deweloperskie ma kartę sieciową, która umożliwia przechwytywanie aktywności sieciowej między przeglądarką a serwerem. Jednak te ślady nie obejmują komunikatów o zdarzeniach dotyczących protokołu WebSocket i wysyłanych przez serwer. Jeśli są używane te transporty, należy użyć narzędzia, takiego jak programu Fiddler lub TcpDump (opisane poniżej).

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge i Internet Explorer

(Instrukcje są takie same dla przeglądarki Edge i Internet Explorer)

1. Naciśnij klawisz F12, aby otworzyć narzędzia deweloperskie
2. Kliknij kartę Sieć
3. Odśwież stronę (w razie konieczności) i Odtwórz problem
4. Kliknij ikonę Zapisz na pasku narzędzi, aby wyeksportować śledzenie jako plik "HAR":

![Ikona Zapisz na karcie Sieć narzędzi deweloperskich Microsoft Edge](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. Naciśnij klawisz F12, aby otworzyć narzędzia deweloperskie
2. Kliknij kartę Sieć
3. Odśwież stronę (w razie konieczności) i Odtwórz problem
4. Kliknij prawym przyciskiem myszy w dowolnym miejscu na liście żądań i wybierz polecenie "Zapisz jako HAR z zawartością":

![Opcja "Zapisz jako HAR z zawartością" w karcie Sieć narzędzi deweloperskich Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. Naciśnij klawisz F12, aby otworzyć narzędzia deweloperskie
2. Kliknij kartę Sieć
3. Odśwież stronę (w razie konieczności) i Odtwórz problem
4. Kliknij prawym przyciskiem myszy w dowolnym miejscu na liście żądań i wybierz pozycję "Zapisz wszystko jako HAR"

![Opcja "Zapisz wszystko jako HAR" w obszarze Mozilla Firefox dev Tools Network karta](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>Dołączanie plików diagnostycznych do problemów z usługą GitHub

Pliki diagnostyczne można dołączać do problemów z usługą GitHub, zmieniając ich nazwy, aby zawierały `.txt` rozszerzenie, a następnie przeciągać i upuszczać je na ten problem.

> [!NOTE]
> Nie należy wklejać zawartości plików dziennika ani śladów sieci do problemu w usłudze GitHub. Te dzienniki i ślady mogą być bardzo duże, a usługi GitHub zwykle obcinają je.

![Przeciąganie plików dziennika do problemu z usługą GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a>Metryki

Metryki to reprezentacja danych miar w przedziale czasu. Na przykład żądania na sekundę. Dane metryk umożliwiają obserwację stanu aplikacji na wysokim poziomie. Metryki programu .NET gRPC są emitowane przy użyciu <xref:System.Diagnostics.Tracing.EventCounter> .

### <a name="no-locsignalr-server-metrics"></a>SignalRmetryki serwera

SignalRmetryki serwera są raportowane w <xref:Microsoft.AspNetCore.Http.Connections> źródle zdarzeń.

| Nazwa                    | Opis                 |
|-------------------------|-----------------------------|
| `connections-started`   | Łączna liczba rozpoczętych połączeń   |
| `connections-stopped`   | Łączna liczba zatrzymanych połączeń   |
| `connections-timed-out` | Całkowita liczba limitów połączeń |
| `current-connections`   | Bieżące połączenia         |
| `connections-duration`  | Średni czas trwania połączenia |

### <a name="observe-metrics"></a>Obserwuj metryki

[dotnet-Counters](/dotnet/core/diagnostics/dotnet-counters) to narzędzie do monitorowania wydajności dla monitorowania kondycji ad hoc i badania wydajności pierwszego poziomu. Monitoruj aplikację .NET za pomocą `Microsoft.AspNetCore.Http.Connections` nazwy dostawcy. Na przykład:

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
