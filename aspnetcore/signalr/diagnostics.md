---
title: Rejestrowanie i Diagnostyka w ASP.NET CoreSignalR
author: anurse
description: Dowiedz się, jak zbierać diagnostykę z SignalR aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
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
ms.openlocfilehash: 61733fe4fbcd7b94662404a39a288ff2ce75ec53
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021838"
---
# <a name="logging-and-diagnostics-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="56d2d-103">Rejestrowanie i Diagnostyka w ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="56d2d-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="56d2d-104">Według [Andrew Stanton-pielęgniarki](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="56d2d-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="56d2d-105">Ten artykuł zawiera wskazówki dotyczące zbierania danych diagnostycznych z SignalR aplikacji ASP.NET Core w celu ułatwienia rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="56d2d-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="56d2d-106">Rejestrowanie po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="56d2d-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="56d2d-107">Dzienniki po stronie serwera mogą zawierać poufne informacje z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="56d2d-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="56d2d-108">**Nigdy nie** Publikuj nieprzetworzonych dzienników z aplikacji produkcyjnych na forach publicznych, takich jak GitHub.</span><span class="sxs-lookup"><span data-stu-id="56d2d-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="56d2d-109">Ponieważ SignalR jest częścią ASP.NET Core, używa systemu rejestrowania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="56d2d-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="56d2d-110">W konfiguracji domyślnej SignalR dzienniki są bardzo mało informacji, ale można je skonfigurować.</span><span class="sxs-lookup"><span data-stu-id="56d2d-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="56d2d-111">Szczegółowe informacje na temat konfigurowania rejestrowania ASP.NET Core można znaleźć w dokumentacji dotyczącej [rejestrowania ASP.NET Core](xref:fundamentals/logging/index#configuration) .</span><span class="sxs-lookup"><span data-stu-id="56d2d-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="56d2d-112">SignalRużywa dwóch kategorii rejestratora:</span><span class="sxs-lookup"><span data-stu-id="56d2d-112">SignalR uses two logger categories:</span></span>

* <span data-ttu-id="56d2d-113">`Microsoft.AspNetCore.SignalR`: W przypadku dzienników związanych z protokołami centrów, aktywowanie centrów, wywoływanie metod i innych działań związanych z centrum.</span><span class="sxs-lookup"><span data-stu-id="56d2d-113">`Microsoft.AspNetCore.SignalR`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="56d2d-114">`Microsoft.AspNetCore.Http.Connections`: W przypadku dzienników związanych z transportami, takich jak obiekty WebSockets, długie sondowanie, zdarzenia wysłane przez serwer i infrastruktura niskiego poziomu SignalR .</span><span class="sxs-lookup"><span data-stu-id="56d2d-114">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="56d2d-115">Aby włączyć szczegółowe dzienniki SignalR , należy skonfigurować obie powyższe prefiksy na `Debug` poziomie w *appsettings.jsna* pliku, dodając następujące elementy do `LogLevel` podsekcji w `Logging` :</span><span class="sxs-lookup"><span data-stu-id="56d2d-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="56d2d-116">Możesz również skonfigurować ten kod w kodzie w `CreateWebHostBuilder` metodzie:</span><span class="sxs-lookup"><span data-stu-id="56d2d-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="56d2d-117">Jeśli nie korzystasz z konfiguracji opartej na notacji JSON, ustaw następujące wartości konfiguracji w systemie konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="56d2d-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="56d2d-118">Zapoznaj się z dokumentacją systemu konfiguracyjnego, aby określić sposób określania zagnieżdżonych wartości konfiguracyjnych.</span><span class="sxs-lookup"><span data-stu-id="56d2d-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="56d2d-119">Na przykład w przypadku używania zmiennych środowiskowych `_` zamiast `:` (na przykład) są używane dwa znaki `Logging__LogLevel__Microsoft.AspNetCore.SignalR` .</span><span class="sxs-lookup"><span data-stu-id="56d2d-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="56d2d-120">Zalecamy użycie `Debug` poziomu podczas zbierania bardziej szczegółowych informacji diagnostycznych dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="56d2d-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="56d2d-121">Na `Trace` poziomie powstaje Diagnostyka bardzo niskiego poziomu i jest rzadko wymagana do diagnozowania problemów w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="56d2d-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="56d2d-122">Dostęp do dzienników po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="56d2d-122">Access server-side logs</span></span>

<span data-ttu-id="56d2d-123">Sposób dostępu do dzienników po stronie serwera zależy od środowiska, w którym jest uruchomiony program.</span><span class="sxs-lookup"><span data-stu-id="56d2d-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="56d2d-124">Jako Aplikacja konsolowa poza usługami IIS</span><span class="sxs-lookup"><span data-stu-id="56d2d-124">As a console app outside IIS</span></span>

<span data-ttu-id="56d2d-125">Jeśli używasz programu w aplikacji konsolowej, [Rejestrator konsoli](xref:fundamentals/logging/index#console) powinien być domyślnie włączony.</span><span class="sxs-lookup"><span data-stu-id="56d2d-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="56d2d-126">SignalRDzienniki będą wyświetlane w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="56d2d-126">SignalR logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="56d2d-127">W IIS Express z programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56d2d-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="56d2d-128">Program Visual Studio Wyświetla dane wyjściowe dziennika w oknie **danych wyjściowych** .</span><span class="sxs-lookup"><span data-stu-id="56d2d-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="56d2d-129">Wybierz opcję listy rozwijanej **ASP.NET Core serwera sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="56d2d-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="56d2d-130">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="56d2d-130">Azure App Service</span></span>

<span data-ttu-id="56d2d-131">Włącz opcję **Rejestrowanie aplikacji (system plików)** w sekcji **dzienniki diagnostyki** w portalu Azure App Service i skonfiguruj **poziom** na `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="56d2d-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="56d2d-132">Dzienniki powinny być dostępne w usłudze **przesyłania strumieniowego dzienników** oraz w dziennikach w systemie plików App Service.</span><span class="sxs-lookup"><span data-stu-id="56d2d-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="56d2d-133">Aby uzyskać więcej informacji, zobacz [przesyłanie strumieniowe dzienników Azure](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="56d2d-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="56d2d-134">Inne środowiska</span><span class="sxs-lookup"><span data-stu-id="56d2d-134">Other environments</span></span>

<span data-ttu-id="56d2d-135">Jeśli aplikacja jest wdrażana w innym środowisku (na przykład Docker, Kubernetes lub Windows Service), zobacz, <xref:fundamentals/logging/index> Aby uzyskać więcej informacji na temat konfigurowania dostawców rejestrowania odpowiednie dla danego środowiska.</span><span class="sxs-lookup"><span data-stu-id="56d2d-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="56d2d-136">Rejestrowanie klientów JavaScript</span><span class="sxs-lookup"><span data-stu-id="56d2d-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="56d2d-137">Dzienniki po stronie klienta mogą zawierać poufne informacje z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="56d2d-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="56d2d-138">**Nigdy nie** Publikuj nieprzetworzonych dzienników z aplikacji produkcyjnych na forach publicznych, takich jak GitHub.</span><span class="sxs-lookup"><span data-stu-id="56d2d-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="56d2d-139">Korzystając z klienta JavaScript, można skonfigurować opcje rejestrowania za pomocą `configureLogging` metody w `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="56d2d-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="56d2d-140">Aby całkowicie wyłączyć rejestrowanie, określ `signalR.LogLevel.None` w `configureLogging` metodzie.</span><span class="sxs-lookup"><span data-stu-id="56d2d-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="56d2d-141">W poniższej tabeli przedstawiono poziomy dziennika dostępne dla klienta JavaScript.</span><span class="sxs-lookup"><span data-stu-id="56d2d-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="56d2d-142">Ustawienie poziomu dziennika na jedną z tych wartości umożliwia rejestrowanie na tym poziomie i wszystkich poziomów powyżej niego w tabeli.</span><span class="sxs-lookup"><span data-stu-id="56d2d-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="56d2d-143">Poziom</span><span class="sxs-lookup"><span data-stu-id="56d2d-143">Level</span></span> | <span data-ttu-id="56d2d-144">Opis</span><span class="sxs-lookup"><span data-stu-id="56d2d-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="56d2d-145">Żadne komunikaty nie są rejestrowane.</span><span class="sxs-lookup"><span data-stu-id="56d2d-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="56d2d-146">Komunikaty wskazujące niepowodzenie w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="56d2d-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="56d2d-147">Komunikaty wskazujące niepowodzenie w bieżącej operacji.</span><span class="sxs-lookup"><span data-stu-id="56d2d-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="56d2d-148">Komunikaty wskazujące na problem niekrytyczny.</span><span class="sxs-lookup"><span data-stu-id="56d2d-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="56d2d-149">Komunikaty informacyjne.</span><span class="sxs-lookup"><span data-stu-id="56d2d-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="56d2d-150">Komunikaty diagnostyczne przydatne do debugowania.</span><span class="sxs-lookup"><span data-stu-id="56d2d-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="56d2d-151">Bardzo szczegółowe komunikaty diagnostyczne przeznaczone do diagnozowania określonych problemów.</span><span class="sxs-lookup"><span data-stu-id="56d2d-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="56d2d-152">Po skonfigurowaniu szczegółowości dzienniki zostaną zapisane w konsoli przeglądarki (lub w standardowym wyjściu w aplikacji NodeJS).</span><span class="sxs-lookup"><span data-stu-id="56d2d-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="56d2d-153">Jeśli chcesz wysłać dzienniki do niestandardowego systemu rejestrowania, możesz dostarczyć obiekt JavaScript implementujący `ILogger` interfejs.</span><span class="sxs-lookup"><span data-stu-id="56d2d-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="56d2d-154">Jedyną metodą, która musi zostać wdrożona `log` , jest, która pobiera poziom zdarzenia i komunikat skojarzony ze zdarzeniem.</span><span class="sxs-lookup"><span data-stu-id="56d2d-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="56d2d-155">Przykład:</span><span class="sxs-lookup"><span data-stu-id="56d2d-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="56d2d-156">Rejestrowanie klienta platformy .NET</span><span class="sxs-lookup"><span data-stu-id="56d2d-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="56d2d-157">Dzienniki po stronie klienta mogą zawierać poufne informacje z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="56d2d-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="56d2d-158">**Nigdy nie** Publikuj nieprzetworzonych dzienników z aplikacji produkcyjnych na forach publicznych, takich jak GitHub.</span><span class="sxs-lookup"><span data-stu-id="56d2d-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="56d2d-159">Aby pobrać dzienniki z klienta .NET, można użyć `ConfigureLogging` metody z `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="56d2d-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="56d2d-160">Działa tak samo jak `ConfigureLogging` Metoda w systemach `WebHostBuilder` i `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="56d2d-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="56d2d-161">Można skonfigurować tych samych dostawców rejestrowania, których używasz w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="56d2d-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="56d2d-162">Należy jednak ręcznie zainstalować i włączyć pakiety NuGet dla poszczególnych dostawców rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="56d2d-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

<span data-ttu-id="56d2d-163">Aby dodać rejestrowanie klienta platformy .NET do Blazor WebAssembly aplikacji, zobacz <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging> .</span><span class="sxs-lookup"><span data-stu-id="56d2d-163">To add .NET client logging to a Blazor WebAssembly app, see <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging>.</span></span>

### <a name="console-logging"></a><span data-ttu-id="56d2d-164">Rejestrowanie konsoli</span><span class="sxs-lookup"><span data-stu-id="56d2d-164">Console logging</span></span>

<span data-ttu-id="56d2d-165">Aby włączyć rejestrowanie konsoli, Dodaj pakiet [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) .</span><span class="sxs-lookup"><span data-stu-id="56d2d-165">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="56d2d-166">Następnie użyj metody, `AddConsole` Aby skonfigurować Rejestrator konsoli:</span><span class="sxs-lookup"><span data-stu-id="56d2d-166">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="56d2d-167">Rejestrowanie okna danych wyjściowych debugowania</span><span class="sxs-lookup"><span data-stu-id="56d2d-167">Debug output window logging</span></span>

<span data-ttu-id="56d2d-168">Możesz również skonfigurować dzienniki, aby przejść do okna **danych wyjściowych** w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="56d2d-168">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="56d2d-169">Zainstaluj pakiet [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) i Użyj `AddDebug` metody:</span><span class="sxs-lookup"><span data-stu-id="56d2d-169">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="56d2d-170">Inni dostawcy rejestrowania</span><span class="sxs-lookup"><span data-stu-id="56d2d-170">Other logging providers</span></span>

<span data-ttu-id="56d2d-171">SignalRobsługuje innych dostawców rejestrowania, takich jak Serilog, SEQ, NLog lub dowolny inny system rejestrowania, który integruje się z programem `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="56d2d-171">SignalR supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="56d2d-172">Jeśli system rejestrowania zapewnia, możesz `ILoggerProvider` zarejestrować go za pomocą `AddProvider` :</span><span class="sxs-lookup"><span data-stu-id="56d2d-172">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="56d2d-173">Szczegółowość kontroli</span><span class="sxs-lookup"><span data-stu-id="56d2d-173">Control verbosity</span></span>

<span data-ttu-id="56d2d-174">Jeśli rejestrujesz się z innych miejsc w aplikacji, zmiana poziomu domyślnego na wartość `Debug` może być zbyt pełna.</span><span class="sxs-lookup"><span data-stu-id="56d2d-174">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="56d2d-175">Możesz użyć filtru, aby skonfigurować poziom rejestrowania dla SignalR dzienników.</span><span class="sxs-lookup"><span data-stu-id="56d2d-175">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="56d2d-176">Można to zrobić w kodzie w taki sam sposób jak na serwerze:</span><span class="sxs-lookup"><span data-stu-id="56d2d-176">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="56d2d-177">Ślady sieci</span><span class="sxs-lookup"><span data-stu-id="56d2d-177">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="56d2d-178">Śledzenie sieci zawiera pełną zawartość każdej wiadomości wysyłanej przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="56d2d-178">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="56d2d-179">**Nigdy nie** Publikuj nieprzetworzonych danych śledzenia sieci z aplikacji produkcyjnych na forach publicznych, takich jak GitHub.</span><span class="sxs-lookup"><span data-stu-id="56d2d-179">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="56d2d-180">W przypadku wystąpienia problemu śledzenie sieci może czasami dostarczyć wiele przydatnych informacji.</span><span class="sxs-lookup"><span data-stu-id="56d2d-180">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="56d2d-181">Jest to szczególnie przydatne, jeśli zamierzasz rozwiązać problem z naszym narzędziem do śledzenia problemów.</span><span class="sxs-lookup"><span data-stu-id="56d2d-181">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="56d2d-182">Zbieranie danych śledzenia sieci za pomocą programu Fiddler (preferowana opcja)</span><span class="sxs-lookup"><span data-stu-id="56d2d-182">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="56d2d-183">Ta metoda działa w przypadku wszystkich aplikacji.</span><span class="sxs-lookup"><span data-stu-id="56d2d-183">This method works for all apps.</span></span>

<span data-ttu-id="56d2d-184">Programu Fiddler to bardzo zaawansowane narzędzie do zbierania śladów HTTP.</span><span class="sxs-lookup"><span data-stu-id="56d2d-184">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="56d2d-185">Zainstaluj ją z [Telerik.com/Fiddler](https://www.telerik.com/fiddler), uruchom ją, a następnie uruchom aplikację i Odtwórz problem.</span><span class="sxs-lookup"><span data-stu-id="56d2d-185">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="56d2d-186">Programu Fiddler jest dostępny dla systemu Windows, a dostępne wersje beta dla macOS i Linux.</span><span class="sxs-lookup"><span data-stu-id="56d2d-186">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="56d2d-187">Jeśli łączysz się przy użyciu protokołu HTTPS, należy wykonać kilka dodatkowych kroków, aby programu Fiddler można było odszyfrować ruch HTTPS.</span><span class="sxs-lookup"><span data-stu-id="56d2d-187">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="56d2d-188">Aby uzyskać więcej informacji, zobacz [dokumentację programu Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="56d2d-188">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="56d2d-189">Po zebraniu śledzenia można wyeksportować śledzenie, wybierając pozycję **plik**  >  **Zapisz**  >  **wszystkie sesje** z paska menu.</span><span class="sxs-lookup"><span data-stu-id="56d2d-189">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Eksportowanie wszystkich sesji z programu Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="56d2d-191">Zbieraj dane śledzenia sieci z tcpdump (tylko macOS i Linux)</span><span class="sxs-lookup"><span data-stu-id="56d2d-191">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="56d2d-192">Ta metoda działa w przypadku wszystkich aplikacji.</span><span class="sxs-lookup"><span data-stu-id="56d2d-192">This method works for all apps.</span></span>

<span data-ttu-id="56d2d-193">Można zbierać pierwotne ślady TCP przy użyciu tcpdump, uruchamiając następujące polecenie z poziomu powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="56d2d-193">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="56d2d-194">Jeśli wystąpi błąd uprawnień, może być konieczne lub nastąpić odtworzenie `root` prefiksu polecenia `sudo` :</span><span class="sxs-lookup"><span data-stu-id="56d2d-194">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="56d2d-195">Zamień na `[interface]` interfejs sieciowy, który ma być przechwytywany.</span><span class="sxs-lookup"><span data-stu-id="56d2d-195">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="56d2d-196">Zwykle jest to coś podobnego `/dev/eth0` (dla standardowego interfejsu Ethernet) lub `/dev/lo0` (dla ruchu hosta lokalnego).</span><span class="sxs-lookup"><span data-stu-id="56d2d-196">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="56d2d-197">Aby uzyskać więcej informacji, zobacz `tcpdump` stronę Man w systemie hosta.</span><span class="sxs-lookup"><span data-stu-id="56d2d-197">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="56d2d-198">Zbieranie danych śledzenia sieci w przeglądarce</span><span class="sxs-lookup"><span data-stu-id="56d2d-198">Collect a network trace in the browser</span></span>

<span data-ttu-id="56d2d-199">Ta metoda działa tylko w przypadku aplikacji opartych na przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="56d2d-199">This method only works for browser-based apps.</span></span>

<span data-ttu-id="56d2d-200">Większość przeglądarek Narzędzia deweloperskie ma kartę sieciową, która umożliwia przechwytywanie aktywności sieciowej między przeglądarką a serwerem.</span><span class="sxs-lookup"><span data-stu-id="56d2d-200">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="56d2d-201">Jednak te ślady nie obejmują komunikatów o zdarzeniach dotyczących protokołu WebSocket i wysyłanych przez serwer.</span><span class="sxs-lookup"><span data-stu-id="56d2d-201">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="56d2d-202">Jeśli są używane te transporty, należy użyć narzędzia, takiego jak programu Fiddler lub TcpDump (opisane poniżej).</span><span class="sxs-lookup"><span data-stu-id="56d2d-202">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="56d2d-203">Microsoft Edge i Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="56d2d-203">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="56d2d-204">(Instrukcje są takie same dla przeglądarki Edge i Internet Explorer)</span><span class="sxs-lookup"><span data-stu-id="56d2d-204">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="56d2d-205">Naciśnij klawisz F12, aby otworzyć narzędzia deweloperskie</span><span class="sxs-lookup"><span data-stu-id="56d2d-205">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="56d2d-206">Kliknij kartę Sieć</span><span class="sxs-lookup"><span data-stu-id="56d2d-206">Click the Network Tab</span></span>
3. <span data-ttu-id="56d2d-207">Odśwież stronę (w razie konieczności) i Odtwórz problem</span><span class="sxs-lookup"><span data-stu-id="56d2d-207">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="56d2d-208">Kliknij ikonę Zapisz na pasku narzędzi, aby wyeksportować śledzenie jako plik "HAR":</span><span class="sxs-lookup"><span data-stu-id="56d2d-208">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Ikona Zapisz na karcie Sieć narzędzi deweloperskich Microsoft Edge](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="56d2d-210">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="56d2d-210">Google Chrome</span></span>

1. <span data-ttu-id="56d2d-211">Naciśnij klawisz F12, aby otworzyć narzędzia deweloperskie</span><span class="sxs-lookup"><span data-stu-id="56d2d-211">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="56d2d-212">Kliknij kartę Sieć</span><span class="sxs-lookup"><span data-stu-id="56d2d-212">Click the Network Tab</span></span>
3. <span data-ttu-id="56d2d-213">Odśwież stronę (w razie konieczności) i Odtwórz problem</span><span class="sxs-lookup"><span data-stu-id="56d2d-213">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="56d2d-214">Kliknij prawym przyciskiem myszy w dowolnym miejscu na liście żądań i wybierz polecenie "Zapisz jako HAR z zawartością":</span><span class="sxs-lookup"><span data-stu-id="56d2d-214">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Opcja "Zapisz jako HAR z zawartością" w karcie Sieć narzędzi deweloperskich Google Chrome](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="56d2d-216">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="56d2d-216">Mozilla Firefox</span></span>

1. <span data-ttu-id="56d2d-217">Naciśnij klawisz F12, aby otworzyć narzędzia deweloperskie</span><span class="sxs-lookup"><span data-stu-id="56d2d-217">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="56d2d-218">Kliknij kartę Sieć</span><span class="sxs-lookup"><span data-stu-id="56d2d-218">Click the Network Tab</span></span>
3. <span data-ttu-id="56d2d-219">Odśwież stronę (w razie konieczności) i Odtwórz problem</span><span class="sxs-lookup"><span data-stu-id="56d2d-219">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="56d2d-220">Kliknij prawym przyciskiem myszy w dowolnym miejscu na liście żądań i wybierz pozycję "Zapisz wszystko jako HAR"</span><span class="sxs-lookup"><span data-stu-id="56d2d-220">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Opcja "Zapisz wszystko jako HAR" w obszarze Mozilla Firefox dev Tools Network karta](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="56d2d-222">Dołączanie plików diagnostycznych do problemów z usługą GitHub</span><span class="sxs-lookup"><span data-stu-id="56d2d-222">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="56d2d-223">Pliki diagnostyczne można dołączać do problemów z usługą GitHub, zmieniając ich nazwy, aby zawierały `.txt` rozszerzenie, a następnie przeciągać i upuszczać je na ten problem.</span><span class="sxs-lookup"><span data-stu-id="56d2d-223">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="56d2d-224">Nie należy wklejać zawartości plików dziennika ani śladów sieci do problemu w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="56d2d-224">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="56d2d-225">Te dzienniki i ślady mogą być bardzo duże, a usługi GitHub zwykle obcinają je.</span><span class="sxs-lookup"><span data-stu-id="56d2d-225">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Przeciąganie plików dziennika do problemu z usługą GitHub](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a><span data-ttu-id="56d2d-227">Metryki</span><span class="sxs-lookup"><span data-stu-id="56d2d-227">Metrics</span></span>

<span data-ttu-id="56d2d-228">Metryki to reprezentacja danych miar w przedziale czasu.</span><span class="sxs-lookup"><span data-stu-id="56d2d-228">Metrics is a representation of data measures over intervals of time.</span></span> <span data-ttu-id="56d2d-229">Na przykład żądania na sekundę.</span><span class="sxs-lookup"><span data-stu-id="56d2d-229">For example, requests per second.</span></span> <span data-ttu-id="56d2d-230">Dane metryk umożliwiają obserwację stanu aplikacji na wysokim poziomie.</span><span class="sxs-lookup"><span data-stu-id="56d2d-230">Metrics data allows observation of the state of an app at a high level.</span></span> <span data-ttu-id="56d2d-231">Metryki programu .NET gRPC są emitowane przy użyciu <xref:System.Diagnostics.Tracing.EventCounter> .</span><span class="sxs-lookup"><span data-stu-id="56d2d-231">.NET gRPC metrics are emitted using <xref:System.Diagnostics.Tracing.EventCounter>.</span></span>

### <a name="no-locsignalr-server-metrics"></a><span data-ttu-id="56d2d-232">SignalRmetryki serwera</span><span class="sxs-lookup"><span data-stu-id="56d2d-232">SignalR server metrics</span></span>

<span data-ttu-id="56d2d-233">SignalRmetryki serwera są raportowane w <xref:Microsoft.AspNetCore.Http.Connections> źródle zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="56d2d-233">SignalR server metrics are reported on the <xref:Microsoft.AspNetCore.Http.Connections> event source.</span></span>

| <span data-ttu-id="56d2d-234">Nazwa</span><span class="sxs-lookup"><span data-stu-id="56d2d-234">Name</span></span>                    | <span data-ttu-id="56d2d-235">Opis</span><span class="sxs-lookup"><span data-stu-id="56d2d-235">Description</span></span>                 |
|-------------------------|-----------------------------|
| `connections-started`   | <span data-ttu-id="56d2d-236">Łączna liczba rozpoczętych połączeń</span><span class="sxs-lookup"><span data-stu-id="56d2d-236">Total connections started</span></span>   |
| `connections-stopped`   | <span data-ttu-id="56d2d-237">Łączna liczba zatrzymanych połączeń</span><span class="sxs-lookup"><span data-stu-id="56d2d-237">Total connections stopped</span></span>   |
| `connections-timed-out` | <span data-ttu-id="56d2d-238">Całkowita liczba limitów połączeń</span><span class="sxs-lookup"><span data-stu-id="56d2d-238">Total connections timed out</span></span> |
| `current-connections`   | <span data-ttu-id="56d2d-239">Bieżące połączenia</span><span class="sxs-lookup"><span data-stu-id="56d2d-239">Current connections</span></span>         |
| `connections-duration`  | <span data-ttu-id="56d2d-240">Średni czas trwania połączenia</span><span class="sxs-lookup"><span data-stu-id="56d2d-240">Average connection duration</span></span> |

### <a name="observe-metrics"></a><span data-ttu-id="56d2d-241">Obserwuj metryki</span><span class="sxs-lookup"><span data-stu-id="56d2d-241">Observe metrics</span></span>

<span data-ttu-id="56d2d-242">[dotnet-Counters](/dotnet/core/diagnostics/dotnet-counters) to narzędzie do monitorowania wydajności dla monitorowania kondycji ad hoc i badania wydajności pierwszego poziomu.</span><span class="sxs-lookup"><span data-stu-id="56d2d-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="56d2d-243">Monitoruj aplikację .NET za pomocą `Microsoft.AspNetCore.Http.Connections` nazwy dostawcy.</span><span class="sxs-lookup"><span data-stu-id="56d2d-243">Monitor a .NET app with `Microsoft.AspNetCore.Http.Connections` as the provider name.</span></span> <span data-ttu-id="56d2d-244">Przykład:</span><span class="sxs-lookup"><span data-stu-id="56d2d-244">For example:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="56d2d-245">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="56d2d-245">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
