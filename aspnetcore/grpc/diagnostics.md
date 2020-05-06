---
title: Rejestrowanie i Diagnostyka w programie gRPC na platformie .NET
author: jamesnk
description: Dowiedz się, jak zbierać diagnostykę z aplikacji gRPC na platformie .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/diagnostics
ms.openlocfilehash: 15f68ced99bdaea9ce53db801a4b2a3bfef2f8dd
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774681"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="6a8a1-103">Rejestrowanie i Diagnostyka w programie gRPC na platformie .NET</span><span class="sxs-lookup"><span data-stu-id="6a8a1-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="6a8a1-104">Przez [Kuba Kowalski-króla](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="6a8a1-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="6a8a1-105">Ten artykuł zawiera wskazówki dotyczące zbierania diagnostyki z aplikacji gRPC w celu ułatwienia rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="6a8a1-106">Omawiane tematy to m.in.:</span><span class="sxs-lookup"><span data-stu-id="6a8a1-106">Topics covered include:</span></span>

* <span data-ttu-id="6a8a1-107">**Rejestrowanie** dzienników strukturalnych Zapisano do [rejestrowania w programie .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="6a8a1-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="6a8a1-108"><xref:Microsoft.Extensions.Logging.ILogger>Program jest używany przez platformy aplikacji do pisania dzienników oraz przez użytkowników w celu ich własnego rejestrowania w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="6a8a1-109">**Śledzenie** — zdarzenia związane z operacją zapisaną `DiaganosticSource` przy `Activity`użyciu i.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="6a8a1-110">Ślady ze źródła diagnostycznego są często używane do zbierania danych telemetrycznych aplikacji według bibliotek takich jak [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) i [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span><span class="sxs-lookup"><span data-stu-id="6a8a1-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="6a8a1-111">**Metryki** — reprezentacja miar danych przez przedziały czasu, na przykład żądania na sekundę.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="6a8a1-112">Metryki są emitowane `EventCounter` przy użyciu i mogą być obserwowane przy użyciu narzędzia wiersza polecenia usługi [dotnet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) lub z [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span><span class="sxs-lookup"><span data-stu-id="6a8a1-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="6a8a1-113">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="6a8a1-113">Logging</span></span>

<span data-ttu-id="6a8a1-114">usługi gRPC i dzienniki zapisu klienta gRPC przy użyciu funkcji [rejestrowania w programie .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="6a8a1-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="6a8a1-115">Dzienniki są dobrym miejscem do uruchomienia, gdy zachodzi potrzeba debugowania nieoczekiwanego zachowania w aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="6a8a1-116">Rejestrowanie usług gRPC Services</span><span class="sxs-lookup"><span data-stu-id="6a8a1-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="6a8a1-117">Dzienniki po stronie serwera mogą zawierać poufne informacje z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="6a8a1-118">**Nigdy nie** Publikuj nieprzetworzonych dzienników z aplikacji produkcyjnych na forach publicznych, takich jak GitHub.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="6a8a1-119">Ponieważ usługi gRPC Services są hostowane na ASP.NET Core, korzysta z systemu rejestrowania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="6a8a1-120">W konfiguracji domyślnej gRPC rejestruje bardzo mało informacji, ale można je skonfigurować.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="6a8a1-121">Szczegółowe informacje na temat konfigurowania rejestrowania ASP.NET Core można znaleźć w dokumentacji dotyczącej [rejestrowania ASP.NET Core](xref:fundamentals/logging/index#configuration) .</span><span class="sxs-lookup"><span data-stu-id="6a8a1-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="6a8a1-122">gRPC dodaje dzienniki w `Grpc` kategorii.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="6a8a1-123">Aby włączyć szczegółowe dzienniki z gRPC, `Grpc` Skonfiguruj prefiksy do `Debug` poziomu w pliku *appSettings. JSON* , dodając następujące elementy do `LogLevel` podsekcji w: `Logging`</span><span class="sxs-lookup"><span data-stu-id="6a8a1-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="6a8a1-124">Można to również skonfigurować w *Startup.cs* z `ConfigureLogging`:</span><span class="sxs-lookup"><span data-stu-id="6a8a1-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="6a8a1-125">Jeśli nie korzystasz z konfiguracji opartej na notacji JSON, ustaw w systemie konfiguracji następującą wartość konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="6a8a1-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="6a8a1-126">Zapoznaj się z dokumentacją systemu konfiguracyjnego, aby określić sposób określania zagnieżdżonych wartości konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="6a8a1-127">Na przykład w przypadku używania zmiennych środowiskowych zamiast `_` `:` (na przykład `Logging__LogLevel__Grpc`) są używane dwa znaki.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="6a8a1-128">Zalecamy użycie `Debug` poziomu podczas zbierania bardziej szczegółowych informacji diagnostycznych dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="6a8a1-129">Na `Trace` poziomie powstaje Diagnostyka bardzo niskiego poziomu i jest rzadko wymagana do diagnozowania problemów w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="6a8a1-130">Przykładowe dane wyjściowe rejestrowania</span><span class="sxs-lookup"><span data-stu-id="6a8a1-130">Sample logging output</span></span>

<span data-ttu-id="6a8a1-131">Oto przykład danych wyjściowych konsoli na `Debug` poziomie usługi gRPC:</span><span class="sxs-lookup"><span data-stu-id="6a8a1-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

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

### <a name="access-server-side-logs"></a><span data-ttu-id="6a8a1-132">Dostęp do dzienników po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="6a8a1-132">Access server-side logs</span></span>

<span data-ttu-id="6a8a1-133">Sposób dostępu do dzienników po stronie serwera zależy od środowiska, w którym jest uruchomiony program.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="6a8a1-134">Jako Aplikacja konsolowa</span><span class="sxs-lookup"><span data-stu-id="6a8a1-134">As a console app</span></span>

<span data-ttu-id="6a8a1-135">Jeśli używasz programu w aplikacji konsolowej, [Rejestrator konsoli](xref:fundamentals/logging/index#console-provider) powinien być domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="6a8a1-136">Dzienniki gRPC będą wyświetlane w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="6a8a1-137">Inne środowiska</span><span class="sxs-lookup"><span data-stu-id="6a8a1-137">Other environments</span></span>

<span data-ttu-id="6a8a1-138">Jeśli aplikacja jest wdrażana w innym środowisku (na przykład Docker, Kubernetes lub Windows Service), zobacz <xref:fundamentals/logging/index> , aby uzyskać więcej informacji na temat konfigurowania dostawców rejestrowania odpowiednie dla danego środowiska.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="6a8a1-139">Rejestrowanie klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="6a8a1-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="6a8a1-140">Dzienniki po stronie klienta mogą zawierać poufne informacje z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="6a8a1-141">**Nigdy nie** Publikuj nieprzetworzonych dzienników z aplikacji produkcyjnych na forach publicznych, takich jak GitHub.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="6a8a1-142">Aby pobrać dzienniki z klienta .NET, można ustawić `GrpcChannelOptions.LoggerFactory` właściwość podczas tworzenia kanału klienta.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="6a8a1-143">Jeśli wywołujesz usługę gRPC z aplikacji ASP.NET Core, fabryka rejestratorów może zostać rozpoznana z iniekcji zależności (DI):</span><span class="sxs-lookup"><span data-stu-id="6a8a1-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="6a8a1-144">Alternatywnym sposobem włączenia rejestrowania klienta jest użycie [fabryki klienta gRPC](xref:grpc/clientfactory) do utworzenia klienta programu.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="6a8a1-145">Klient gRPC zarejestrowany w fabryce klienta i rozwiązany przez program DI automatycznie użyje skonfigurowanego rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="6a8a1-146">Jeśli aplikacja nie używa funkcji DI, można utworzyć nowe `ILoggerFactory` wystąpienie za pomocą [LoggerFactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span><span class="sxs-lookup"><span data-stu-id="6a8a1-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="6a8a1-147">Aby uzyskać dostęp do tej metody, Dodaj pakiet [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="6a8a1-148">gRPC zakresy dziennika klienta</span><span class="sxs-lookup"><span data-stu-id="6a8a1-148">gRPC client log scopes</span></span>

<span data-ttu-id="6a8a1-149">Klient gRPC dodaje [zakres rejestrowania](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) do dzienników wykonywanych podczas wywołania gRPC.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-149">The gRPC client adds a [logging scope](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="6a8a1-150">Zakres zawiera metadane dotyczące wywołania gRPC:</span><span class="sxs-lookup"><span data-stu-id="6a8a1-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="6a8a1-151">**GrpcMethodType** — typ metody gRPC.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="6a8a1-152">Możliwe wartości to nazwy z `Grpc.Core.MethodType` wyliczenia, np. jednoargumentowe</span><span class="sxs-lookup"><span data-stu-id="6a8a1-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="6a8a1-153">**GrpcUri** — WZGLĘDNY identyfikator URI metody gRPC, np./Greet. Greeter/SayHellos</span><span class="sxs-lookup"><span data-stu-id="6a8a1-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="6a8a1-154">Przykładowe dane wyjściowe rejestrowania</span><span class="sxs-lookup"><span data-stu-id="6a8a1-154">Sample logging output</span></span>

<span data-ttu-id="6a8a1-155">Oto przykład danych wyjściowych konsoli na `Debug` poziomie klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="6a8a1-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

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

## <a name="tracing"></a><span data-ttu-id="6a8a1-156">Śledzenie</span><span class="sxs-lookup"><span data-stu-id="6a8a1-156">Tracing</span></span>

<span data-ttu-id="6a8a1-157">usługi gRPC oraz klient gRPC dostarczają informacji o wywołaniach gRPC za pomocą [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) i [działania](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span><span class="sxs-lookup"><span data-stu-id="6a8a1-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="6a8a1-158">.NET gRPC używa działania do reprezentowania wywołania gRPC.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="6a8a1-159">Zdarzenia śledzenia są zapisywane w źródle diagnostycznym na początek i zatrzymanie działania wywołania gRPC.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="6a8a1-160">Śledzenie nie przechwytuje informacji o wysyłaniu komunikatów przez okres istnienia wywołań przesyłania strumieniowego gRPC.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="6a8a1-161">Śledzenie usługi gRPC</span><span class="sxs-lookup"><span data-stu-id="6a8a1-161">gRPC service tracing</span></span>

<span data-ttu-id="6a8a1-162">usługi gRPC Services są hostowane na ASP.NET Core które raportują zdarzenia dotyczące przychodzących żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="6a8a1-163">gRPC określone metadane są dodawane do istniejącej diagnostyki żądania HTTP, która zapewnia ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="6a8a1-164">Nazwa źródła diagnostyki to `Microsoft.AspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="6a8a1-165">Nazwa działania to `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="6a8a1-166">Nazwa metody gRPC wywoływanej przez wywołanie gRPC jest dodawana jako tag o nazwie `grpc.method`.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="6a8a1-167">Kod stanu wywołania gRPC po zakończeniu zostanie dodany jako tag o nazwie `grpc.status_code`.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="6a8a1-168">Śledzenie klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="6a8a1-168">gRPC client tracing</span></span>

<span data-ttu-id="6a8a1-169">Klient .NET gRPC używa `HttpClient` do wykonywania wywołań gRPC.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="6a8a1-170">Mimo `HttpClient` że program nie zapisuje zdarzeń diagnostycznych, klient .NET gRPC udostępnia niestandardowe źródło diagnostyki, działanie i zdarzenia, dzięki czemu można zbierać kompletne informacje o wywołaniu gRPC.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="6a8a1-171">Nazwa źródła diagnostyki to `Grpc.Net.Client`.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="6a8a1-172">Nazwa działania to `Grpc.Net.Client.GrpcOut`.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="6a8a1-173">Nazwa metody gRPC wywoływanej przez wywołanie gRPC jest dodawana jako tag o nazwie `grpc.method`.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="6a8a1-174">Kod stanu wywołania gRPC po zakończeniu zostanie dodany jako tag o nazwie `grpc.status_code`.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="6a8a1-175">Zbieranie śledzenia</span><span class="sxs-lookup"><span data-stu-id="6a8a1-175">Collecting tracing</span></span>

<span data-ttu-id="6a8a1-176">Najprostszym sposobem korzystania `DiagnosticSource` z programu jest skonfigurowanie biblioteki telemetrii, takiej jak [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) lub [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="6a8a1-177">Biblioteka będzie przetwarzać informacje o wywołaniach gRPC wraz z inną telemetrią aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="6a8a1-178">Śledzenie można wyświetlać w zarządzanej usłudze, takiej jak Application Insights, lub można uruchomić własny system śledzenia rozproszonego.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="6a8a1-179">OpenTelemetry obsługuje eksportowanie danych śledzenia do [Jaeger](https://www.jaegertracing.io/) i [Zipkin](https://zipkin.io/).</span><span class="sxs-lookup"><span data-stu-id="6a8a1-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="6a8a1-180">`DiagnosticSource`może zużywać zdarzenia śledzenia w kodzie `DiagnosticListener`przy użyciu.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="6a8a1-181">Aby uzyskać informacje o nasłuchiwaniu źródła diagnostycznego z kodem, zobacz [Podręcznik użytkownika DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span><span class="sxs-lookup"><span data-stu-id="6a8a1-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="6a8a1-182">Biblioteki telemetrii nie przechwytuje obecnie danych `Grpc.Net.Client.GrpcOut` telemetrycznych gRPC.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="6a8a1-183">Działa w celu usprawnienia bibliotek telemetrycznych przechwytujących to śledzenie.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="6a8a1-184">Metryki</span><span class="sxs-lookup"><span data-stu-id="6a8a1-184">Metrics</span></span>

<span data-ttu-id="6a8a1-185">Metryki są reprezentacją miar danych przez przedziały czasu, na przykład żądania na sekundę.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="6a8a1-186">Dane metryk umożliwiają obserwację stanu aplikacji na wysokim poziomie.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="6a8a1-187">Metryki programu .NET gRPC są emitowane przy użyciu `EventCounter`.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="6a8a1-188">metryki usługi gRPC</span><span class="sxs-lookup"><span data-stu-id="6a8a1-188">gRPC service metrics</span></span>

<span data-ttu-id="6a8a1-189">metryki serwera gRPC są raportowane w `Grpc.AspNetCore.Server` źródle zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="6a8a1-190">Nazwa</span><span class="sxs-lookup"><span data-stu-id="6a8a1-190">Name</span></span>                      | <span data-ttu-id="6a8a1-191">Opis</span><span class="sxs-lookup"><span data-stu-id="6a8a1-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="6a8a1-192">Łączna liczba wywołań</span><span class="sxs-lookup"><span data-stu-id="6a8a1-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="6a8a1-193">Bieżące wywołania</span><span class="sxs-lookup"><span data-stu-id="6a8a1-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="6a8a1-194">Łączna liczba nieudanych wywołań</span><span class="sxs-lookup"><span data-stu-id="6a8a1-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="6a8a1-195">Przekroczono łączny termin ostateczny wywołań</span><span class="sxs-lookup"><span data-stu-id="6a8a1-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="6a8a1-196">Całkowita liczba wysłanych komunikatów</span><span class="sxs-lookup"><span data-stu-id="6a8a1-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="6a8a1-197">Całkowita liczba odebranych komunikatów</span><span class="sxs-lookup"><span data-stu-id="6a8a1-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="6a8a1-198">Łączna liczba wywołań niezaimplementowanych</span><span class="sxs-lookup"><span data-stu-id="6a8a1-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="6a8a1-199">ASP.NET Core również udostępnia własne metryki dotyczące `Microsoft.AspNetCore.Hosting` źródła zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="6a8a1-200">metryki klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="6a8a1-200">gRPC client metrics</span></span>

<span data-ttu-id="6a8a1-201">metryki klienta gRPC są raportowane w `Grpc.Net.Client` źródle zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="6a8a1-202">Nazwa</span><span class="sxs-lookup"><span data-stu-id="6a8a1-202">Name</span></span>                      | <span data-ttu-id="6a8a1-203">Opis</span><span class="sxs-lookup"><span data-stu-id="6a8a1-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="6a8a1-204">Łączna liczba wywołań</span><span class="sxs-lookup"><span data-stu-id="6a8a1-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="6a8a1-205">Bieżące wywołania</span><span class="sxs-lookup"><span data-stu-id="6a8a1-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="6a8a1-206">Łączna liczba nieudanych wywołań</span><span class="sxs-lookup"><span data-stu-id="6a8a1-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="6a8a1-207">Przekroczono łączny termin ostateczny wywołań</span><span class="sxs-lookup"><span data-stu-id="6a8a1-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="6a8a1-208">Całkowita liczba wysłanych komunikatów</span><span class="sxs-lookup"><span data-stu-id="6a8a1-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="6a8a1-209">Całkowita liczba odebranych komunikatów</span><span class="sxs-lookup"><span data-stu-id="6a8a1-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="6a8a1-210">Obserwuj metryki</span><span class="sxs-lookup"><span data-stu-id="6a8a1-210">Observe metrics</span></span>

<span data-ttu-id="6a8a1-211">[dotnet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) to narzędzie do monitorowania wydajności dla monitorowania kondycji ad hoc i badania wydajności pierwszego poziomu.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="6a8a1-212">Monitoruj aplikację .NET przy użyciu nazwy `Grpc.AspNetCore.Server` dostawcy `Grpc.Net.Client` lub.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

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

<span data-ttu-id="6a8a1-213">Innym sposobem na obserwowanie metryk gRPC jest przechwycenie danych licznika przy Application Insights użyciu [pakietu Microsoft. ApplicationInsights. EventCounterCollector](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span><span class="sxs-lookup"><span data-stu-id="6a8a1-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="6a8a1-214">Po zakończeniu instalacji Application Insights zbiera typowe liczniki platformy .NET w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="6a8a1-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="6a8a1-215">Liczniki gRPC nie są zbierane domyślnie, ale usługi App Insights można [dostosować w celu uwzględnienia dodatkowych liczników](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span><span class="sxs-lookup"><span data-stu-id="6a8a1-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="6a8a1-216">Określ gRPC Liczniki usługi Application Insights do zebrania w *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6a8a1-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="6a8a1-217">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="6a8a1-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
