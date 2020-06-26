---
title: Narzędzia diagnostyki wydajności
author: mjrousos
description: Przydatne narzędzia do diagnozowania problemów z wydajnością w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/diagnostic-tools
ms.openlocfilehash: 631dedddecfe3b443e38c6f5a1a182f98c79f0ad
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399598"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="eb27d-103">narzędzia diagnostyczne wydajności</span><span class="sxs-lookup"><span data-stu-id="eb27d-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="eb27d-104">Według [Jan Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="eb27d-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="eb27d-105">W tym artykule wymieniono narzędzia służące do diagnozowania problemów z wydajnością w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eb27d-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="eb27d-106">narzędzia diagnostyczne programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb27d-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="eb27d-107">[Narzędzia profilowania i diagnostyki](/visualstudio/profiling) wbudowane w program Visual Studio są dobrym miejscem do rozpoczęcia badania problemów z wydajnością.</span><span class="sxs-lookup"><span data-stu-id="eb27d-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="eb27d-108">Te narzędzia są zaawansowane i wygodne do użycia w środowisku deweloperskim programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="eb27d-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="eb27d-109">Narzędzia umożliwiają analizę użycia procesora CPU, użycie pamięci i zdarzeń wydajności w aplikacjach ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eb27d-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span> <span data-ttu-id="eb27d-110">Wbudowana, sprawia, że profilowanie jest łatwe w czasie projektowania.</span><span class="sxs-lookup"><span data-stu-id="eb27d-110">Being built-in makes profiling easy at development time.</span></span>

<span data-ttu-id="eb27d-111">Więcej informacji można znaleźć w [dokumentacji programu Visual Studio](/visualstudio/profiling/profiling-overview).</span><span class="sxs-lookup"><span data-stu-id="eb27d-111">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="eb27d-112">Application Insights</span><span class="sxs-lookup"><span data-stu-id="eb27d-112">Application Insights</span></span>

<span data-ttu-id="eb27d-113">[Application Insights](/azure/application-insights/app-insights-overview) zapewnia szczegółowe dane dotyczące wydajności dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="eb27d-113">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="eb27d-114">Application Insights automatycznie zbiera dane dotyczące stawek odpowiedzi, stawek niepowodzeń, czasów odpowiedzi zależności i nie tylko.</span><span class="sxs-lookup"><span data-stu-id="eb27d-114">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="eb27d-115">Application Insights obsługuje rejestrowanie zdarzeń niestandardowych i metryk specyficznych dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="eb27d-115">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="eb27d-116">Usługa Azure Application Insights zapewnia wiele sposobów uzyskania wglądu w monitorowane aplikacje:</span><span class="sxs-lookup"><span data-stu-id="eb27d-116">Azure Application Insights provides multiple ways to give insights on monitored apps:</span></span>

- <span data-ttu-id="eb27d-117">[Mapa aplikacji](/azure/application-insights/app-insights-app-map) — umożliwia wychwycenie wąskich gardeł wydajności lub odporności na awarie we wszystkich składnikach aplikacji rozproszonych.</span><span class="sxs-lookup"><span data-stu-id="eb27d-117">[Application Map](/azure/application-insights/app-insights-app-map) – helps spot performance bottlenecks or failure hot-spots across all components of distributed apps.</span></span>
- <span data-ttu-id="eb27d-118">[Eksplorator metryk platformy Azure](/azure/azure-monitor/platform/metrics-getting-started) to składnik Microsoft Azure Portal, który umożliwia Wykreślanie wykresów, wizualne skorelowanie trendów oraz badanie skoków i wartości DIP w ramach metryk.</span><span class="sxs-lookup"><span data-stu-id="eb27d-118">[Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) is a component of the Microsoft Azure portal that allows plotting charts, visually correlating trends, and investigating spikes and dips in metrics' values.</span></span>
- <span data-ttu-id="eb27d-119">[Blok wydajności w portalu Application Insights](/azure/application-insights/app-insights-tutorial-performance):</span><span class="sxs-lookup"><span data-stu-id="eb27d-119">[Performance blade in Application Insights portal](/azure/application-insights/app-insights-tutorial-performance):</span></span>

  - <span data-ttu-id="eb27d-120">Przedstawia szczegóły wydajności dla różnych operacji w monitorowanej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="eb27d-120">Shows performance details for different operations in the monitored app.</span></span>
  - <span data-ttu-id="eb27d-121">Umożliwia przechodzenie do szczegółów pojedynczej operacji w celu sprawdzenia wszystkich części/zależności, które przyczyniają się do długiego czasu trwania.</span><span class="sxs-lookup"><span data-stu-id="eb27d-121">Allows drilling into a single operation to check all parts/dependencies that contribute to a long duration.</span></span>
  - <span data-ttu-id="eb27d-122">Profiler można wywołać z tego miejsca, aby zbierać dane śledzenia wydajności na żądanie.</span><span class="sxs-lookup"><span data-stu-id="eb27d-122">Profiler can be invoked from here to collect performance traces on-demand.</span></span>

- <span data-ttu-id="eb27d-123">[Usługa Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) umożliwia regularne i na żądanie profilowania aplikacji .NET.</span><span class="sxs-lookup"><span data-stu-id="eb27d-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) allows regular and on-demand profiling of .NET apps.</span></span>  <span data-ttu-id="eb27d-124">Azure Portal przedstawia przechwycone dane śledzenia wydajności za pomocą stosów wywołań i ścieżek aktywnych.</span><span class="sxs-lookup"><span data-stu-id="eb27d-124">Azure portal shows captured performance traces with call stacks and hot paths.</span></span> <span data-ttu-id="eb27d-125">Pliki śledzenia można także pobrać w celu uzyskania dokładniejszej analizy przy użyciu narzędzia PerfView.</span><span class="sxs-lookup"><span data-stu-id="eb27d-125">The trace files can also be downloaded for deeper analysis using PerfView.</span></span>

<span data-ttu-id="eb27d-126">Application Insights można używać w środowiskach różnych:</span><span class="sxs-lookup"><span data-stu-id="eb27d-126">Application Insights can be used in a variety environments:</span></span>

- <span data-ttu-id="eb27d-127">Zoptymalizowane pod kątem pracy na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="eb27d-127">Optimized to work in Azure.</span></span>
- <span data-ttu-id="eb27d-128">Działa w środowisku produkcyjnym, opracowywaniu i przemieszczania.</span><span class="sxs-lookup"><span data-stu-id="eb27d-128">Works in production, development, and staging.</span></span>
- <span data-ttu-id="eb27d-129">Działa lokalnie z [programu Visual Studio](/azure/application-insights/app-insights-visual-studio) lub w innych środowiskach hostingu.</span><span class="sxs-lookup"><span data-stu-id="eb27d-129">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="eb27d-130">Aby uzyskać więcej informacji, zobacz [Application Insights ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="eb27d-130">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="eb27d-131">Narzędzia PerfView</span><span class="sxs-lookup"><span data-stu-id="eb27d-131">PerfView</span></span>

<span data-ttu-id="eb27d-132">[Narzędzia PerfView](https://github.com/Microsoft/perfview) to narzędzie do analizy wydajności utworzone przez zespół .NET przeznaczony do diagnozowania problemów z wydajnością programu .NET.</span><span class="sxs-lookup"><span data-stu-id="eb27d-132">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="eb27d-133">Narzędzia PerfView umożliwia analizę użycia procesora CPU, pamięci i zachowania GC, zdarzeń wydajności i czasu zegara ściany.</span><span class="sxs-lookup"><span data-stu-id="eb27d-133">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="eb27d-134">Możesz dowiedzieć się więcej na temat narzędzia PerfView oraz jak zacząć korzystać z [samouczków wideo narzędzia PerfView](https://channel9.msdn.com/Series/PerfView-Tutorial) lub odczytując Podręcznik użytkownika dostępny w narzędziu lub [witrynie GitHub](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="eb27d-134">You can learn more about PerfView and how to get started with [PerfView video tutorials](https://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="windows-performance-toolkit"></a><span data-ttu-id="eb27d-135">Zestaw narzędzi wydajności systemu Windows</span><span class="sxs-lookup"><span data-stu-id="eb27d-135">Windows Performance Toolkit</span></span>

<span data-ttu-id="eb27d-136">[Zestaw narzędzi wydajności systemu Windows](/windows-hardware/test/wpt/) (WPT) składa się z dwóch składników: Windows Performance Recorder (WP) i Windows Performance ANALYZER (WPA).</span><span class="sxs-lookup"><span data-stu-id="eb27d-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) consists of two components: Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA).</span></span> <span data-ttu-id="eb27d-137">Narzędzia te tworzą szczegółowe profile wydajności systemów operacyjnych i aplikacji systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="eb27d-137">The tools produce in-depth performance profiles of Windows operating systems and apps.</span></span> <span data-ttu-id="eb27d-138">WPT ma bogatsze sposoby wizualizacji danych, ale gromadzenie danych jest mniej wydajne niż narzędzia PerfView.</span><span class="sxs-lookup"><span data-stu-id="eb27d-138">WPT has richer ways of visualizing data, but its data collecting is less powerful than PerfView's.</span></span>

## <a name="perfcollect"></a><span data-ttu-id="eb27d-139">PerfCollect</span><span class="sxs-lookup"><span data-stu-id="eb27d-139">PerfCollect</span></span>

<span data-ttu-id="eb27d-140">Chociaż narzędzia PerfView to przydatne narzędzie do analizy wydajności dla scenariuszy platformy .NET, działa ono tylko w systemie Windows, więc nie można używać go do zbierania śladów z ASP.NET Core aplikacji działających w środowiskach systemu Linux.</span><span class="sxs-lookup"><span data-stu-id="eb27d-140">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="eb27d-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) to skrypt bash, który używa natywnych narzędzi profilowania systemu Linux ([perf](https://perf.wiki.kernel.org/index.php/Main_Page) i [LTTng](https://lttng.org/)) do zbierania śladów w systemie Linux, które mogą być analizowane przez narzędzia PerfView.</span><span class="sxs-lookup"><span data-stu-id="eb27d-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="eb27d-142">PerfCollect jest przydatne w przypadku wyświetlenia problemów z wydajnością w środowiskach systemu Linux, w których nie można bezpośrednio użyć narzędzia PerfView.</span><span class="sxs-lookup"><span data-stu-id="eb27d-142">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="eb27d-143">Zamiast tego PerfCollect może zbierać ślady z aplikacji .NET Core, które są następnie analizowane na komputerze z systemem Windows przy użyciu narzędzia PerfView.</span><span class="sxs-lookup"><span data-stu-id="eb27d-143">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="eb27d-144">Więcej informacji o sposobie instalowania i rozpoczynania pracy z usługą PerfCollect jest dostępnych [w witrynie GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span><span class="sxs-lookup"><span data-stu-id="eb27d-144">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>

## <a name="other-third-party-performance-tools"></a><span data-ttu-id="eb27d-145">Inne narzędzia do oceny wydajności innych firm</span><span class="sxs-lookup"><span data-stu-id="eb27d-145">Other Third-party Performance Tools</span></span>

<span data-ttu-id="eb27d-146">Poniżej wymieniono niektóre narzędzia do oceny wydajności innych firm, które są przydatne podczas badania wydajności aplikacji .NET Core.</span><span class="sxs-lookup"><span data-stu-id="eb27d-146">The following lists some third-party performance tools that are useful in performance investigation of .NET Core applications.</span></span>

- [<span data-ttu-id="eb27d-147">MiniProfiler</span><span class="sxs-lookup"><span data-stu-id="eb27d-147">MiniProfiler</span></span>](https://miniprofiler.com/)
- <span data-ttu-id="eb27d-148">dotTrace i dotMemory z JetBrains</span><span class="sxs-lookup"><span data-stu-id="eb27d-148">dotTrace and dotMemory from JetBrains</span></span>
- <span data-ttu-id="eb27d-149">VTune z firmy Intel</span><span class="sxs-lookup"><span data-stu-id="eb27d-149">VTune from Intel</span></span>
