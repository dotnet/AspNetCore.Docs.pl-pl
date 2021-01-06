---
title: Tworzenie i przekierowywanie dzienników przy użyciu modułu ASP.NET Core
author: rick-anderson
description: Skonfiguruj usługi IIS i moduł ASP.NET Core do przechwytywania dzienników i informacji diagnostycznych.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: b866be130a93491bce7c5c7e08045de961ff91b2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057443"
---
# <a name="log-creation-and-redirection"></a><span data-ttu-id="2686f-103">Tworzenie i przekierowywanie dzienników</span><span class="sxs-lookup"><span data-stu-id="2686f-103">Log creation and redirection</span></span>

<span data-ttu-id="2686f-104">Moduł ASP.NET Core przekierowuje dane wyjściowe z konsoli stdout i stderr do dysku, jeśli `stdoutLogEnabled` `stdoutLogFile` atrybuty i `aspNetCore` elementu są ustawione.</span><span class="sxs-lookup"><span data-stu-id="2686f-104">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="2686f-105">Wszystkie foldery w `stdoutLogFile` ścieżce są tworzone przez moduł po utworzeniu pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2686f-105">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="2686f-106">Pula aplikacji musi mieć dostęp do zapisu w lokalizacji, w której zapisano dzienniki (Użyj `IIS AppPool\{APP POOL NAME}` do udostępnienia uprawnienia do zapisu, gdzie symbol zastępczy `{APP POOL NAME}` to nazwa puli aplikacji).</span><span class="sxs-lookup"><span data-stu-id="2686f-106">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="2686f-107">Dzienniki nie są obracane, chyba że zostanie wykonane odtwarzanie procesów/ponowne uruchomienie.</span><span class="sxs-lookup"><span data-stu-id="2686f-107">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="2686f-108">Ponosisz odpowiedzialność dostawcy usług hostingowych, aby ograniczyć ilość miejsca na dysku zużywanej przez dzienniki.</span><span class="sxs-lookup"><span data-stu-id="2686f-108">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="2686f-109">Użycie dziennika stdout jest zalecane tylko w przypadku rozwiązywania problemów z uruchamianiem aplikacji w usługach IIS lub w przypadku korzystania z [obsługi usług IIS w czasie projektowania w programie Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), a nie podczas debugowania lokalnego i uruchamiania aplikacji przy użyciu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2686f-109">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="2686f-110">Nie używaj dziennika stdout w celu uzyskania ogólnych celów rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2686f-110">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="2686f-111">Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki.</span><span class="sxs-lookup"><span data-stu-id="2686f-111">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2686f-112">Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.</span><span class="sxs-lookup"><span data-stu-id="2686f-112">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="2686f-113">Sygnatura czasowa i rozszerzenie pliku są dodawane automatycznie podczas tworzenia pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2686f-113">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="2686f-114">Nazwa pliku dziennika jest złożona przez dołączenie sygnatury czasowej, identyfikatora procesu i rozszerzenia pliku ( `.log` ) do ostatniego segmentu `stdoutLogFile` ścieżki (zwykle `stdout` ) rozdzielanej znakami podkreślenia.</span><span class="sxs-lookup"><span data-stu-id="2686f-114">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="2686f-115">Jeśli `stdoutLogFile` ścieżka kończy się na `stdout` , dziennik aplikacji o identyfikatorze PID 1934 utworzony w dniu 2/5/2018 o 19:42:32 ma nazwę pliku `stdout_20180205194132_1934.log` .</span><span class="sxs-lookup"><span data-stu-id="2686f-115">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="2686f-116">Jeśli `stdoutLogEnabled` ma wartość false, błędy występujące podczas uruchamiania aplikacji są przechwytywane i emitowane do dziennika zdarzeń do 30 KB.</span><span class="sxs-lookup"><span data-stu-id="2686f-116">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="2686f-117">Po uruchomieniu wszystkie dodatkowe dzienniki są odrzucane.</span><span class="sxs-lookup"><span data-stu-id="2686f-117">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="2686f-118">Poniższy przykładowy `aspNetCore` element konfiguruje rejestrowanie stdout w ścieżce względnej `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="2686f-118">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="2686f-119">Upewnij się, że tożsamość użytkownika puli aplikacji ma uprawnienia do zapisu w podanej ścieżce.</span><span class="sxs-lookup"><span data-stu-id="2686f-119">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="2686f-120">W przypadku publikowania aplikacji dla Azure App Service wdrożenia zestaw SDK sieci Web ustawia `stdoutLogFile` wartość na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="2686f-120">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2686f-121">`%home`Zmienna środowiskowa jest wstępnie zdefiniowana dla aplikacji hostowanych przez Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="2686f-121">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="2686f-122">Aby utworzyć reguły filtru rejestrowania, zobacz sekcje [Konfiguracja](xref:fundamentals/logging/index#log-filtering) i [filtrowanie dzienników](xref:fundamentals/logging/index#log-filtering) w dokumentacji rejestrowania ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2686f-122">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="2686f-123">Aby uzyskać więcej informacji na temat formatów ścieżki, zobacz [formaty ścieżki plików w systemach Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="2686f-123">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="2686f-124">Udoskonalone dzienniki diagnostyczne</span><span class="sxs-lookup"><span data-stu-id="2686f-124">Enhanced diagnostic logs</span></span>

<span data-ttu-id="2686f-125">Moduł ASP.NET Core można skonfigurować w celu udostępnienia dzienników diagnostyki rozszerzonej.</span><span class="sxs-lookup"><span data-stu-id="2686f-125">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="2686f-126">Dodaj `<handlerSettings>` element do `<aspNetCore>` elementu w `web.config` .</span><span class="sxs-lookup"><span data-stu-id="2686f-126">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="2686f-127">Ustawienie w `debugLevel` celu `TRACE` ujawniania większej wierności informacji diagnostycznych:</span><span class="sxs-lookup"><span data-stu-id="2686f-127">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="2686f-128">Wszystkie foldery w ścieżce ( `logs` w poprzednim przykładzie) są tworzone przez moduł po utworzeniu pliku dziennika.</span><span class="sxs-lookup"><span data-stu-id="2686f-128">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="2686f-129">Pula aplikacji musi mieć dostęp do zapisu w lokalizacji, w której zapisano dzienniki (Użyj `IIS AppPool\{APP POOL NAME}` do udostępnienia uprawnienia do zapisu, gdzie symbol zastępczy `{APP POOL NAME}` to nazwa puli aplikacji).</span><span class="sxs-lookup"><span data-stu-id="2686f-129">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="2686f-130">Wartości poziomu debugowania ( `debugLevel` ) mogą zawierać zarówno poziom, jak i lokalizację.</span><span class="sxs-lookup"><span data-stu-id="2686f-130">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="2686f-131">Poziomy (w kolejności od najmniejszej do największej szczegółowości):</span><span class="sxs-lookup"><span data-stu-id="2686f-131">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="2686f-132">ERROR</span><span class="sxs-lookup"><span data-stu-id="2686f-132">ERROR</span></span>
* <span data-ttu-id="2686f-133">OSTRZEŻENIE</span><span class="sxs-lookup"><span data-stu-id="2686f-133">WARNING</span></span>
* <span data-ttu-id="2686f-134">INFORMACJE</span><span class="sxs-lookup"><span data-stu-id="2686f-134">INFO</span></span>
* <span data-ttu-id="2686f-135">TRACE</span><span class="sxs-lookup"><span data-stu-id="2686f-135">TRACE</span></span>

<span data-ttu-id="2686f-136">Lokalizacje (wiele lokalizacji jest dozwolonych):</span><span class="sxs-lookup"><span data-stu-id="2686f-136">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="2686f-137">KONSOLI</span><span class="sxs-lookup"><span data-stu-id="2686f-137">CONSOLE</span></span>
* <span data-ttu-id="2686f-138">ELEMENCIE</span><span class="sxs-lookup"><span data-stu-id="2686f-138">EVENTLOG</span></span>
* <span data-ttu-id="2686f-139">PLIK</span><span class="sxs-lookup"><span data-stu-id="2686f-139">FILE</span></span>

<span data-ttu-id="2686f-140">Ustawienia programu obsługi można także zapewnić za pomocą zmiennych środowiskowych:</span><span class="sxs-lookup"><span data-stu-id="2686f-140">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="2686f-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Ścieżka do pliku dziennika debugowania.</span><span class="sxs-lookup"><span data-stu-id="2686f-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="2686f-142">(Domyślnie: `aspnetcore-debug.log` )</span><span class="sxs-lookup"><span data-stu-id="2686f-142">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="2686f-143">`ASPNETCORE_MODULE_DEBUG`: Debugowanie ustawienia poziomu.</span><span class="sxs-lookup"><span data-stu-id="2686f-143">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="2686f-144">**Nie** pozostawiaj włączonej rejestracji debugowania w ramach wdrożenia dłużej niż jest to wymagane, aby rozwiązać problem.</span><span class="sxs-lookup"><span data-stu-id="2686f-144">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="2686f-145">Rozmiar dziennika nie jest ograniczony.</span><span class="sxs-lookup"><span data-stu-id="2686f-145">The size of the log isn't limited.</span></span> <span data-ttu-id="2686f-146">Pozostawienie włączonego dziennika debugowania może spowodować wyczerpanie dostępnego miejsca na dysku i awarię serwera lub usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="2686f-146">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="2686f-147">Zapoznaj się [z tematem `web.config` konfiguracja modułu ASP.NET Core z](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) przykładem `aspNetCore` elementu w `web.config` pliku.</span><span class="sxs-lookup"><span data-stu-id="2686f-147">See [Configuration of ASP.NET Core Module with `web.config`](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>
