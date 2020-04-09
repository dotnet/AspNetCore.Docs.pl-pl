---
title: Rejestrowanie i diagnostyka w gRPC na .NET
author: jamesnk
description: Dowiedz się, jak zbierać diagnostykę z aplikacji gRPC w serwisie .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 131144bf7a2c637eb2c1a1d5c54990dd4d429502
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417517"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>Rejestrowanie i diagnostyka w gRPC na .NET

Przez [James Newton-King](https://twitter.com/jamesnk)

Ten artykuł zawiera wskazówki dotyczące zbierania diagnostyki z aplikacji gRPC, aby pomóc w rozwiązywaniu problemów. Omawiane tematy to m.in.:

* **Rejestrowanie** - Dzienniki strukturalne zapisywane w [dzienniku .NET Core](xref:fundamentals/logging/index). <xref:Microsoft.Extensions.Logging.ILogger>jest używany przez struktury aplikacji do pisania dzienników i przez użytkowników do własnego logowania w aplikacji.
* **Śledzenie** — zdarzenia związane z operacją napisaną przy użyciu `DiaganosticSource` i `Activity`. Ślady ze źródła diagnostycznego są często używane do zbierania danych telemetrycznych aplikacji przez biblioteki, takie jak [Usługa Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) i [OpenTelemetry.](https://github.com/open-telemetry/opentelemetry-dotnet)
* **Metryki** — reprezentacja miar danych w odstępach czasu, na przykład żądania na sekundę. Metryki są emitowane za pomocą `EventCounter` i można je obserwować za pomocą narzędzia wiersza polecenia [liczników dotnet](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) lub usługi Application [Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).

## <a name="logging"></a>Rejestrowanie

usługi gRPC i logi zapisu klienta gRPC przy użyciu [rejestrowania .NET Core](xref:fundamentals/logging/index). Dzienniki są dobrym miejscem do rozpoczęcia, gdy trzeba debugować nieoczekiwane zachowanie w aplikacjach.

### <a name="grpc-services-logging"></a>Rejestrowanie usług gRPC

> [!WARNING]
> Dzienniki po stronie serwera mogą zawierać poufne informacje z aplikacji. **Nigdy nie** publikuj nieprzetworzonych dzienników z aplikacji produkcyjnych na forach publicznych, takich jak GitHub.

Ponieważ usługi gRPC są hostowane w ASP.NET Core, korzysta z systemu rejestrowania ASP.NET Core. W konfiguracji domyślnej gRPC rejestruje bardzo mało informacji, ale można to skonfigurować. Szczegółowe informacje na temat konfigurowania rejestrowania ASP.NET Core można znaleźć w dokumentacji dotyczącej [rejestrowania ASP.NET](xref:fundamentals/logging/index#configuration) Core.

gRPC dodaje dzienniki `Grpc` w kategorii. Aby włączyć szczegółowe dzienniki z `Grpc` gRPC, skonfiguruj prefiksy do `Debug` poziomu w pliku *appsettings.json,* dodając następujące elementy do `LogLevel` podsekcji w: `Logging`

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

Można to również skonfigurować w *Startup.cs* za pomocą: `ConfigureLogging`

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

Jeśli konfiguracja oparta na JSON nie jest obsługiwana, ustaw następującą wartość konfiguracji w systemie konfiguracyjnym:

* `Logging:LogLevel:Grpc` = `Debug`

Sprawdź dokumentację systemu konfiguracji, aby określić sposób określania zagnieżdżonych wartości konfiguracyjnych. Na przykład podczas korzystania ze `_` zmiennych środowiskowych zamiast `:` (na przykład `Logging__LogLevel__Grpc`) używane są dwa znaki.

Zalecamy używanie `Debug` poziomu podczas zbierania bardziej szczegółowych diagnostyki aplikacji. Poziom `Trace` tworzy diagnostykę bardzo niskiego poziomu i rzadko jest potrzebny do diagnozowania problemów w aplikacji.

#### <a name="sample-logging-output"></a>Przykładowe dane wyjściowe rejestrowania

Oto przykład wyjścia konsoli na `Debug` poziomie usługi gRPC:

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a>Dostęp do dzienników po stronie serwera

Sposób uzyskiwania dostępu do dzienników po stronie serwera zależy od środowiska, w którym jest uruchomiona.

#### <a name="as-a-console-app"></a>Jako aplikacja konsoli

Jeśli korzystasz z aplikacji konsoli, [rejestrator konsoli](xref:fundamentals/logging/index#console-provider) powinien być domyślnie włączony. W konsoli pojawią się dzienniki gRPC.

#### <a name="other-environments"></a>Inne środowiska

Jeśli aplikacja jest wdrażana w innym środowisku (na przykład Docker, Kubernetes lub Windows Service), zobacz <xref:fundamentals/logging/index> więcej informacji na temat konfigurowania dostawców rejestrowania odpowiednich dla środowiska.

### <a name="grpc-client-logging"></a>Rejestrowanie klienta gRPC

> [!WARNING]
> Dzienniki po stronie klienta mogą zawierać poufne informacje z aplikacji. **Nigdy nie** publikuj nieprzetworzonych dzienników z aplikacji produkcyjnych na forach publicznych, takich jak GitHub.

Aby uzyskać dzienniki z klienta .NET, `GrpcChannelOptions.LoggerFactory` można ustawić właściwość podczas tworzenia kanału klienta. Jeśli wywołujesz usługę gRPC z aplikacji ASP.NET Core, fabrykę rejestratora można rozwiązać z iniekcji zależności (DI):

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

Alternatywnym sposobem włączenia rejestrowania klienta jest użycie [fabryki klienta gRPC](xref:grpc/clientfactory) do utworzenia klienta. Klient gRPC zarejestrowany w fabryce klienta i rozwiązany z DI automatycznie użyje skonfigurowanego rejestrowania aplikacji.

Jeśli aplikacja nie używa DI, możesz utworzyć `ILoggerFactory` nowe wystąpienie za pomocą [pliku LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*). Aby uzyskać dostęp do tej metody, dodaj pakiet [rejestrowania Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) do aplikacji.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>Zakresy dziennika klienta gRPC

Klient gRPC dodaje [zakres rejestrowania](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) do dzienników wykonanych podczas wywołania gRPC. Zakres zawiera metadane związane z wywołaniem gRPC:

* **GrpcMethodType** - Typ metody gRPC. Możliwe wartości to `Grpc.Core.MethodType` nazwy z wyliczenia, np.
* **GrpcUri** — względny identyfikator URI metody gRPC, np. Greeter/SayHellos

#### <a name="sample-logging-output"></a>Przykładowe dane wyjściowe rejestrowania

Oto przykład wyjścia konsoli na `Debug` poziomie klienta gRPC:

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a>Śledzenie

Usługi gRPC i klient gRPC dostarczają informacji o wywołaniach gRPC za pomocą [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) i [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).

* .NET gRPC używa działania do reprezentowania wywołania gRPC.
* Śledzenie zdarzeń są zapisywane do źródła diagnostycznego na początku i zatrzymaniu działania wywołania gRPC.
* Śledzenie nie przechwytuje informacji o tym, kiedy wiadomości są wysyłane przez cały okres istnienia połączeń strumieniowych gRPC.

### <a name="grpc-service-tracing"></a>Śledzenie usług gRPC

Usługi gRPC są hostowane w ASP.NET Core, który zgłasza zdarzenia dotyczące przychodzących żądań HTTP. Metadane specyficzne dla gRPC są dodawane do istniejącej diagnostyki żądań HTTP, która ASP.NET zapewnia Core.

* Nazwa źródła `Microsoft.AspNetCore`diagnostycznego to .
* Nazwa działania `Microsoft.AspNetCore.Hosting.HttpRequestIn`to .
  * Nazwa metody gRPC wywoływanej przez wywołanie gRPC jest dodawana jako znacznik o nazwie `grpc.method`.
  * Kod stanu wywołania gRPC po jego zakończeniu jest dodawany jako tag o nazwie `grpc.status_code`.

### <a name="grpc-client-tracing"></a>Śledzenie klienta gRPC

Klient gRPC .NET `HttpClient` używa do wykonywania wywołań gRPC. Mimo `HttpClient` że zapisuje zdarzenia diagnostyczne, klient gRPC .NET udostępnia niestandardowe źródło diagnostyczne, działanie i zdarzenia, dzięki czemu można zbierać pełne informacje o wywołaniu gRPC.

* Nazwa źródła `Grpc.Net.Client`diagnostycznego to .
* Nazwa działania `Grpc.Net.Client.GrpcOut`to .
  * Nazwa metody gRPC wywoływanej przez wywołanie gRPC jest dodawana jako znacznik o nazwie `grpc.method`.
  * Kod stanu wywołania gRPC po jego zakończeniu jest dodawany jako tag o nazwie `grpc.status_code`.

### <a name="collecting-tracing"></a>Zbieranie śledzenia

Najprostszym sposobem użycia `DiagnosticSource` jest skonfigurowanie biblioteki telemetryczne, takich jak [Usługa Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) lub [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) w aplikacji. Biblioteka będzie przetwarzać informacje o wywołaniach gRPC wraz z innymi danymi telemetrycznymi aplikacji.

Śledzenie można wyświetlać w usłudze zarządzanej, takiej jak usługa Application Insights, lub można uruchomić własny rozproszony system śledzenia. OpenTelemetry obsługuje eksportowanie danych śledzenia do [Jaeger](https://www.jaegertracing.io/) i [Zipkin](https://zipkin.io/).

`DiagnosticSource`może korzystać ze śledzenia `DiagnosticListener`zdarzeń w kodzie przy użyciu programu . Aby uzyskać informacje na temat nasłuchiwania źródła diagnostycznego z kodem, zobacz [diagnosticsource podręcznik użytkownika](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).

> [!NOTE]
> Biblioteki telemetryczne nie przechwytują `Grpc.Net.Client.GrpcOut` obecnie danych telemetrycznych specyficznych dla gRPC. Trwają prace nad ulepszeniem bibliotek telemetrycznych przechwytujących to śledzenie.

## <a name="metrics"></a>Metryki

Metryki to reprezentacja miar danych w odstępach czasu, na przykład żądania na sekundę. Dane metryk umożliwia obserwację stanu aplikacji na wysokim poziomie. Metryki .NET gRPC są `EventCounter`emitowane przy użyciu .

### <a name="grpc-service-metrics"></a>Metryki usług gRPC

Metryki serwera gRPC `Grpc.AspNetCore.Server` są zgłaszane w źródle zdarzeń.

| Nazwa                      | Opis                   |
| --------------------------|-------------------------------|
| `total-calls`             | Łączna liczba połączeń                   |
| `current-calls`           | Bieżące połączenia                 |
| `calls-failed`            | Łączna liczba połączeń nie powiodła się            |
| `calls-deadline-exceeded` | Przekroczono łączny termin nawiązywanie połączeń |
| `messages-sent`           | Łączna liczba wysłanych wiadomości           |
| `messages-received`       | Łączna liczba odebranych wiadomości       |
| `calls-unimplemented`     | Łączna liczba połączeń nieuwzwolonych     |

ASP.NET Core udostępnia również własne metryki `Microsoft.AspNetCore.Hosting` w źródle zdarzeń.

### <a name="grpc-client-metrics"></a>Metryki klienta gRPC

Metryki klienta gRPC `Grpc.Net.Client` są raportowane w źródle zdarzeń.

| Nazwa                      | Opis                   |
| --------------------------|-------------------------------|
| `total-calls`             | Łączna liczba połączeń                   |
| `current-calls`           | Bieżące połączenia                 |
| `calls-failed`            | Łączna liczba połączeń nie powiodła się            |
| `calls-deadline-exceeded` | Przekroczono łączny termin nawiązywanie połączeń |
| `messages-sent`           | Łączna liczba wysłanych wiadomości           |
| `messages-received`       | Łączna liczba odebranych wiadomości       |

### <a name="observe-metrics"></a>Obserwowanie danych

[liczniki dotnet to](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) narzędzie do monitorowania wydajności do doraźnego monitorowania kondycji i badania wydajności pierwszego poziomu. Monitoruj aplikację platformy `Grpc.AspNetCore.Server` .NET z nazwą dostawcy lub `Grpc.Net.Client` jako jej nazwę.

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

Innym sposobem obserwowania metryk gRPC jest przechwytywanie danych liczników przy użyciu [pakietu Microsoft.ApplicationInsights.EventCounterCollector](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)aplikacji aplikacji. Po skonfigurowaniu usługa Application Insights zbiera typowe liczniki platformy .NET w czasie wykonywania. Liczniki gRPC nie są zbierane domyślnie, ale App Insights można [dostosować, aby uwzględnić dodatkowe liczniki](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).

Określ liczniki gRPC dla usługi Application Insight do zebrania w *Startup.cs:*

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
