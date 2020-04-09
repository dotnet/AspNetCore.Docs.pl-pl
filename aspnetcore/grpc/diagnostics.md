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
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="4e802-103">Rejestrowanie i diagnostyka w gRPC na .NET</span><span class="sxs-lookup"><span data-stu-id="4e802-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="4e802-104">Przez [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="4e802-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="4e802-105">Ten artykuł zawiera wskazówki dotyczące zbierania diagnostyki z aplikacji gRPC, aby pomóc w rozwiązywaniu problemów.</span><span class="sxs-lookup"><span data-stu-id="4e802-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="4e802-106">Omawiane tematy to m.in.:</span><span class="sxs-lookup"><span data-stu-id="4e802-106">Topics covered include:</span></span>

* <span data-ttu-id="4e802-107">**Rejestrowanie** - Dzienniki strukturalne zapisywane w [dzienniku .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="4e802-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="4e802-108"><xref:Microsoft.Extensions.Logging.ILogger>jest używany przez struktury aplikacji do pisania dzienników i przez użytkowników do własnego logowania w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4e802-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="4e802-109">**Śledzenie** — zdarzenia związane z operacją napisaną przy użyciu `DiaganosticSource` i `Activity`.</span><span class="sxs-lookup"><span data-stu-id="4e802-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="4e802-110">Ślady ze źródła diagnostycznego są często używane do zbierania danych telemetrycznych aplikacji przez biblioteki, takie jak [Usługa Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) i [OpenTelemetry.](https://github.com/open-telemetry/opentelemetry-dotnet)</span><span class="sxs-lookup"><span data-stu-id="4e802-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="4e802-111">**Metryki** — reprezentacja miar danych w odstępach czasu, na przykład żądania na sekundę.</span><span class="sxs-lookup"><span data-stu-id="4e802-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="4e802-112">Metryki są emitowane za pomocą `EventCounter` i można je obserwować za pomocą narzędzia wiersza polecenia [liczników dotnet](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) lub usługi Application [Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span><span class="sxs-lookup"><span data-stu-id="4e802-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="4e802-113">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="4e802-113">Logging</span></span>

<span data-ttu-id="4e802-114">usługi gRPC i logi zapisu klienta gRPC przy użyciu [rejestrowania .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="4e802-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="4e802-115">Dzienniki są dobrym miejscem do rozpoczęcia, gdy trzeba debugować nieoczekiwane zachowanie w aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="4e802-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="4e802-116">Rejestrowanie usług gRPC</span><span class="sxs-lookup"><span data-stu-id="4e802-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="4e802-117">Dzienniki po stronie serwera mogą zawierać poufne informacje z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4e802-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="4e802-118">**Nigdy nie** publikuj nieprzetworzonych dzienników z aplikacji produkcyjnych na forach publicznych, takich jak GitHub.</span><span class="sxs-lookup"><span data-stu-id="4e802-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="4e802-119">Ponieważ usługi gRPC są hostowane w ASP.NET Core, korzysta z systemu rejestrowania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4e802-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="4e802-120">W konfiguracji domyślnej gRPC rejestruje bardzo mało informacji, ale można to skonfigurować.</span><span class="sxs-lookup"><span data-stu-id="4e802-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="4e802-121">Szczegółowe informacje na temat konfigurowania rejestrowania ASP.NET Core można znaleźć w dokumentacji dotyczącej [rejestrowania ASP.NET](xref:fundamentals/logging/index#configuration) Core.</span><span class="sxs-lookup"><span data-stu-id="4e802-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="4e802-122">gRPC dodaje dzienniki `Grpc` w kategorii.</span><span class="sxs-lookup"><span data-stu-id="4e802-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="4e802-123">Aby włączyć szczegółowe dzienniki z `Grpc` gRPC, skonfiguruj prefiksy do `Debug` poziomu w pliku *appsettings.json,* dodając następujące elementy do `LogLevel` podsekcji w: `Logging`</span><span class="sxs-lookup"><span data-stu-id="4e802-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="4e802-124">Można to również skonfigurować w *Startup.cs* za pomocą: `ConfigureLogging`</span><span class="sxs-lookup"><span data-stu-id="4e802-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="4e802-125">Jeśli konfiguracja oparta na JSON nie jest obsługiwana, ustaw następującą wartość konfiguracji w systemie konfiguracyjnym:</span><span class="sxs-lookup"><span data-stu-id="4e802-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="4e802-126">Sprawdź dokumentację systemu konfiguracji, aby określić sposób określania zagnieżdżonych wartości konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="4e802-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="4e802-127">Na przykład podczas korzystania ze `_` zmiennych środowiskowych zamiast `:` (na przykład `Logging__LogLevel__Grpc`) używane są dwa znaki.</span><span class="sxs-lookup"><span data-stu-id="4e802-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="4e802-128">Zalecamy używanie `Debug` poziomu podczas zbierania bardziej szczegółowych diagnostyki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4e802-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="4e802-129">Poziom `Trace` tworzy diagnostykę bardzo niskiego poziomu i rzadko jest potrzebny do diagnozowania problemów w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4e802-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="4e802-130">Przykładowe dane wyjściowe rejestrowania</span><span class="sxs-lookup"><span data-stu-id="4e802-130">Sample logging output</span></span>

<span data-ttu-id="4e802-131">Oto przykład wyjścia konsoli na `Debug` poziomie usługi gRPC:</span><span class="sxs-lookup"><span data-stu-id="4e802-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

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

### <a name="access-server-side-logs"></a><span data-ttu-id="4e802-132">Dostęp do dzienników po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="4e802-132">Access server-side logs</span></span>

<span data-ttu-id="4e802-133">Sposób uzyskiwania dostępu do dzienników po stronie serwera zależy od środowiska, w którym jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="4e802-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="4e802-134">Jako aplikacja konsoli</span><span class="sxs-lookup"><span data-stu-id="4e802-134">As a console app</span></span>

<span data-ttu-id="4e802-135">Jeśli korzystasz z aplikacji konsoli, [rejestrator konsoli](xref:fundamentals/logging/index#console-provider) powinien być domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="4e802-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="4e802-136">W konsoli pojawią się dzienniki gRPC.</span><span class="sxs-lookup"><span data-stu-id="4e802-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="4e802-137">Inne środowiska</span><span class="sxs-lookup"><span data-stu-id="4e802-137">Other environments</span></span>

<span data-ttu-id="4e802-138">Jeśli aplikacja jest wdrażana w innym środowisku (na przykład Docker, Kubernetes lub Windows Service), zobacz <xref:fundamentals/logging/index> więcej informacji na temat konfigurowania dostawców rejestrowania odpowiednich dla środowiska.</span><span class="sxs-lookup"><span data-stu-id="4e802-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="4e802-139">Rejestrowanie klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="4e802-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="4e802-140">Dzienniki po stronie klienta mogą zawierać poufne informacje z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4e802-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="4e802-141">**Nigdy nie** publikuj nieprzetworzonych dzienników z aplikacji produkcyjnych na forach publicznych, takich jak GitHub.</span><span class="sxs-lookup"><span data-stu-id="4e802-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="4e802-142">Aby uzyskać dzienniki z klienta .NET, `GrpcChannelOptions.LoggerFactory` można ustawić właściwość podczas tworzenia kanału klienta.</span><span class="sxs-lookup"><span data-stu-id="4e802-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="4e802-143">Jeśli wywołujesz usługę gRPC z aplikacji ASP.NET Core, fabrykę rejestratora można rozwiązać z iniekcji zależności (DI):</span><span class="sxs-lookup"><span data-stu-id="4e802-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="4e802-144">Alternatywnym sposobem włączenia rejestrowania klienta jest użycie [fabryki klienta gRPC](xref:grpc/clientfactory) do utworzenia klienta.</span><span class="sxs-lookup"><span data-stu-id="4e802-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="4e802-145">Klient gRPC zarejestrowany w fabryce klienta i rozwiązany z DI automatycznie użyje skonfigurowanego rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4e802-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="4e802-146">Jeśli aplikacja nie używa DI, możesz utworzyć `ILoggerFactory` nowe wystąpienie za pomocą [pliku LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span><span class="sxs-lookup"><span data-stu-id="4e802-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="4e802-147">Aby uzyskać dostęp do tej metody, dodaj pakiet [rejestrowania Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4e802-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="4e802-148">Zakresy dziennika klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="4e802-148">gRPC client log scopes</span></span>

<span data-ttu-id="4e802-149">Klient gRPC dodaje [zakres rejestrowania](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) do dzienników wykonanych podczas wywołania gRPC.</span><span class="sxs-lookup"><span data-stu-id="4e802-149">The gRPC client adds a [logging scope](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="4e802-150">Zakres zawiera metadane związane z wywołaniem gRPC:</span><span class="sxs-lookup"><span data-stu-id="4e802-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="4e802-151">**GrpcMethodType** - Typ metody gRPC.</span><span class="sxs-lookup"><span data-stu-id="4e802-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="4e802-152">Możliwe wartości to `Grpc.Core.MethodType` nazwy z wyliczenia, np.</span><span class="sxs-lookup"><span data-stu-id="4e802-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="4e802-153">**GrpcUri** — względny identyfikator URI metody gRPC, np. Greeter/SayHellos</span><span class="sxs-lookup"><span data-stu-id="4e802-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="4e802-154">Przykładowe dane wyjściowe rejestrowania</span><span class="sxs-lookup"><span data-stu-id="4e802-154">Sample logging output</span></span>

<span data-ttu-id="4e802-155">Oto przykład wyjścia konsoli na `Debug` poziomie klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="4e802-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

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

## <a name="tracing"></a><span data-ttu-id="4e802-156">Śledzenie</span><span class="sxs-lookup"><span data-stu-id="4e802-156">Tracing</span></span>

<span data-ttu-id="4e802-157">Usługi gRPC i klient gRPC dostarczają informacji o wywołaniach gRPC za pomocą [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) i [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span><span class="sxs-lookup"><span data-stu-id="4e802-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="4e802-158">.NET gRPC używa działania do reprezentowania wywołania gRPC.</span><span class="sxs-lookup"><span data-stu-id="4e802-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="4e802-159">Śledzenie zdarzeń są zapisywane do źródła diagnostycznego na początku i zatrzymaniu działania wywołania gRPC.</span><span class="sxs-lookup"><span data-stu-id="4e802-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="4e802-160">Śledzenie nie przechwytuje informacji o tym, kiedy wiadomości są wysyłane przez cały okres istnienia połączeń strumieniowych gRPC.</span><span class="sxs-lookup"><span data-stu-id="4e802-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="4e802-161">Śledzenie usług gRPC</span><span class="sxs-lookup"><span data-stu-id="4e802-161">gRPC service tracing</span></span>

<span data-ttu-id="4e802-162">Usługi gRPC są hostowane w ASP.NET Core, który zgłasza zdarzenia dotyczące przychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="4e802-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="4e802-163">Metadane specyficzne dla gRPC są dodawane do istniejącej diagnostyki żądań HTTP, która ASP.NET zapewnia Core.</span><span class="sxs-lookup"><span data-stu-id="4e802-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="4e802-164">Nazwa źródła `Microsoft.AspNetCore`diagnostycznego to .</span><span class="sxs-lookup"><span data-stu-id="4e802-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="4e802-165">Nazwa działania `Microsoft.AspNetCore.Hosting.HttpRequestIn`to .</span><span class="sxs-lookup"><span data-stu-id="4e802-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="4e802-166">Nazwa metody gRPC wywoływanej przez wywołanie gRPC jest dodawana jako znacznik o nazwie `grpc.method`.</span><span class="sxs-lookup"><span data-stu-id="4e802-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="4e802-167">Kod stanu wywołania gRPC po jego zakończeniu jest dodawany jako tag o nazwie `grpc.status_code`.</span><span class="sxs-lookup"><span data-stu-id="4e802-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="4e802-168">Śledzenie klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="4e802-168">gRPC client tracing</span></span>

<span data-ttu-id="4e802-169">Klient gRPC .NET `HttpClient` używa do wykonywania wywołań gRPC.</span><span class="sxs-lookup"><span data-stu-id="4e802-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="4e802-170">Mimo `HttpClient` że zapisuje zdarzenia diagnostyczne, klient gRPC .NET udostępnia niestandardowe źródło diagnostyczne, działanie i zdarzenia, dzięki czemu można zbierać pełne informacje o wywołaniu gRPC.</span><span class="sxs-lookup"><span data-stu-id="4e802-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="4e802-171">Nazwa źródła `Grpc.Net.Client`diagnostycznego to .</span><span class="sxs-lookup"><span data-stu-id="4e802-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="4e802-172">Nazwa działania `Grpc.Net.Client.GrpcOut`to .</span><span class="sxs-lookup"><span data-stu-id="4e802-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="4e802-173">Nazwa metody gRPC wywoływanej przez wywołanie gRPC jest dodawana jako znacznik o nazwie `grpc.method`.</span><span class="sxs-lookup"><span data-stu-id="4e802-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="4e802-174">Kod stanu wywołania gRPC po jego zakończeniu jest dodawany jako tag o nazwie `grpc.status_code`.</span><span class="sxs-lookup"><span data-stu-id="4e802-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="4e802-175">Zbieranie śledzenia</span><span class="sxs-lookup"><span data-stu-id="4e802-175">Collecting tracing</span></span>

<span data-ttu-id="4e802-176">Najprostszym sposobem użycia `DiagnosticSource` jest skonfigurowanie biblioteki telemetryczne, takich jak [Usługa Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) lub [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4e802-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="4e802-177">Biblioteka będzie przetwarzać informacje o wywołaniach gRPC wraz z innymi danymi telemetrycznymi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4e802-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="4e802-178">Śledzenie można wyświetlać w usłudze zarządzanej, takiej jak usługa Application Insights, lub można uruchomić własny rozproszony system śledzenia.</span><span class="sxs-lookup"><span data-stu-id="4e802-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="4e802-179">OpenTelemetry obsługuje eksportowanie danych śledzenia do [Jaeger](https://www.jaegertracing.io/) i [Zipkin](https://zipkin.io/).</span><span class="sxs-lookup"><span data-stu-id="4e802-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="4e802-180">`DiagnosticSource`może korzystać ze śledzenia `DiagnosticListener`zdarzeń w kodzie przy użyciu programu .</span><span class="sxs-lookup"><span data-stu-id="4e802-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="4e802-181">Aby uzyskać informacje na temat nasłuchiwania źródła diagnostycznego z kodem, zobacz [diagnosticsource podręcznik użytkownika](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span><span class="sxs-lookup"><span data-stu-id="4e802-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="4e802-182">Biblioteki telemetryczne nie przechwytują `Grpc.Net.Client.GrpcOut` obecnie danych telemetrycznych specyficznych dla gRPC.</span><span class="sxs-lookup"><span data-stu-id="4e802-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="4e802-183">Trwają prace nad ulepszeniem bibliotek telemetrycznych przechwytujących to śledzenie.</span><span class="sxs-lookup"><span data-stu-id="4e802-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="4e802-184">Metryki</span><span class="sxs-lookup"><span data-stu-id="4e802-184">Metrics</span></span>

<span data-ttu-id="4e802-185">Metryki to reprezentacja miar danych w odstępach czasu, na przykład żądania na sekundę.</span><span class="sxs-lookup"><span data-stu-id="4e802-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="4e802-186">Dane metryk umożliwia obserwację stanu aplikacji na wysokim poziomie.</span><span class="sxs-lookup"><span data-stu-id="4e802-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="4e802-187">Metryki .NET gRPC są `EventCounter`emitowane przy użyciu .</span><span class="sxs-lookup"><span data-stu-id="4e802-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="4e802-188">Metryki usług gRPC</span><span class="sxs-lookup"><span data-stu-id="4e802-188">gRPC service metrics</span></span>

<span data-ttu-id="4e802-189">Metryki serwera gRPC `Grpc.AspNetCore.Server` są zgłaszane w źródle zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="4e802-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="4e802-190">Nazwa</span><span class="sxs-lookup"><span data-stu-id="4e802-190">Name</span></span>                      | <span data-ttu-id="4e802-191">Opis</span><span class="sxs-lookup"><span data-stu-id="4e802-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="4e802-192">Łączna liczba połączeń</span><span class="sxs-lookup"><span data-stu-id="4e802-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="4e802-193">Bieżące połączenia</span><span class="sxs-lookup"><span data-stu-id="4e802-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="4e802-194">Łączna liczba połączeń nie powiodła się</span><span class="sxs-lookup"><span data-stu-id="4e802-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="4e802-195">Przekroczono łączny termin nawiązywanie połączeń</span><span class="sxs-lookup"><span data-stu-id="4e802-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="4e802-196">Łączna liczba wysłanych wiadomości</span><span class="sxs-lookup"><span data-stu-id="4e802-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="4e802-197">Łączna liczba odebranych wiadomości</span><span class="sxs-lookup"><span data-stu-id="4e802-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="4e802-198">Łączna liczba połączeń nieuwzwolonych</span><span class="sxs-lookup"><span data-stu-id="4e802-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="4e802-199">ASP.NET Core udostępnia również własne metryki `Microsoft.AspNetCore.Hosting` w źródle zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="4e802-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="4e802-200">Metryki klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="4e802-200">gRPC client metrics</span></span>

<span data-ttu-id="4e802-201">Metryki klienta gRPC `Grpc.Net.Client` są raportowane w źródle zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="4e802-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="4e802-202">Nazwa</span><span class="sxs-lookup"><span data-stu-id="4e802-202">Name</span></span>                      | <span data-ttu-id="4e802-203">Opis</span><span class="sxs-lookup"><span data-stu-id="4e802-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="4e802-204">Łączna liczba połączeń</span><span class="sxs-lookup"><span data-stu-id="4e802-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="4e802-205">Bieżące połączenia</span><span class="sxs-lookup"><span data-stu-id="4e802-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="4e802-206">Łączna liczba połączeń nie powiodła się</span><span class="sxs-lookup"><span data-stu-id="4e802-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="4e802-207">Przekroczono łączny termin nawiązywanie połączeń</span><span class="sxs-lookup"><span data-stu-id="4e802-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="4e802-208">Łączna liczba wysłanych wiadomości</span><span class="sxs-lookup"><span data-stu-id="4e802-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="4e802-209">Łączna liczba odebranych wiadomości</span><span class="sxs-lookup"><span data-stu-id="4e802-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="4e802-210">Obserwowanie danych</span><span class="sxs-lookup"><span data-stu-id="4e802-210">Observe metrics</span></span>

<span data-ttu-id="4e802-211">[liczniki dotnet to](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) narzędzie do monitorowania wydajności do doraźnego monitorowania kondycji i badania wydajności pierwszego poziomu.</span><span class="sxs-lookup"><span data-stu-id="4e802-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="4e802-212">Monitoruj aplikację platformy `Grpc.AspNetCore.Server` .NET z nazwą dostawcy lub `Grpc.Net.Client` jako jej nazwę.</span><span class="sxs-lookup"><span data-stu-id="4e802-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

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

<span data-ttu-id="4e802-213">Innym sposobem obserwowania metryk gRPC jest przechwytywanie danych liczników przy użyciu [pakietu Microsoft.ApplicationInsights.EventCounterCollector](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)aplikacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4e802-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="4e802-214">Po skonfigurowaniu usługa Application Insights zbiera typowe liczniki platformy .NET w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="4e802-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="4e802-215">Liczniki gRPC nie są zbierane domyślnie, ale App Insights można [dostosować, aby uwzględnić dodatkowe liczniki](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span><span class="sxs-lookup"><span data-stu-id="4e802-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="4e802-216">Określ liczniki gRPC dla usługi Application Insight do zebrania w *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="4e802-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="4e802-217">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="4e802-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
