---
title: Narzędzia diagnostyki wydajności
author: mjrousos
description: Przydatne narzędzia do diagnozowania problemów z wydajnością w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
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
uid: performance/diagnostic-tools
ms.openlocfilehash: 53c613b208c142e1323e593d0c57a42de3150621
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018068"
---
# <a name="performance-diagnostic-tools"></a>narzędzia diagnostyczne wydajności

Według [Jan Rousos](https://github.com/mjrousos)

W tym artykule wymieniono narzędzia służące do diagnozowania problemów z wydajnością w ASP.NET Core.

## <a name="visual-studio-diagnostic-tools"></a>narzędzia diagnostyczne programu Visual Studio

[Narzędzia profilowania i diagnostyki](/visualstudio/profiling) wbudowane w program Visual Studio są dobrym miejscem do rozpoczęcia badania problemów z wydajnością. Te narzędzia są zaawansowane i wygodne do użycia w środowisku deweloperskim programu Visual Studio. Narzędzia umożliwiają analizę użycia procesora CPU, użycie pamięci i zdarzeń wydajności w aplikacjach ASP.NET Core. Wbudowana, sprawia, że profilowanie jest łatwe w czasie projektowania.

Więcej informacji można znaleźć w [dokumentacji programu Visual Studio](/visualstudio/profiling/profiling-overview).

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) zapewnia szczegółowe dane dotyczące wydajności dla aplikacji. Application Insights automatycznie zbiera dane dotyczące stawek odpowiedzi, stawek niepowodzeń, czasów odpowiedzi zależności i nie tylko. Application Insights obsługuje rejestrowanie zdarzeń niestandardowych i metryk specyficznych dla aplikacji.

Usługa Azure Application Insights zapewnia wiele sposobów uzyskania wglądu w monitorowane aplikacje:

- [Mapa aplikacji](/azure/application-insights/app-insights-app-map) — umożliwia wychwycenie wąskich gardeł wydajności lub odporności na awarie we wszystkich składnikach aplikacji rozproszonych.
- [Eksplorator metryk platformy Azure](/azure/azure-monitor/platform/metrics-getting-started) to składnik Microsoft Azure Portal, który umożliwia Wykreślanie wykresów, wizualne skorelowanie trendów oraz badanie skoków i wartości DIP w ramach metryk.
- [Blok wydajności w portalu Application Insights](/azure/application-insights/app-insights-tutorial-performance):

  - Przedstawia szczegóły wydajności dla różnych operacji w monitorowanej aplikacji.
  - Umożliwia przechodzenie do szczegółów pojedynczej operacji w celu sprawdzenia wszystkich części/zależności, które przyczyniają się do długiego czasu trwania.
  - Profiler można wywołać z tego miejsca, aby zbierać dane śledzenia wydajności na żądanie.

- [Usługa Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) umożliwia regularne i na żądanie profilowania aplikacji .NET.  Azure Portal przedstawia przechwycone dane śledzenia wydajności za pomocą stosów wywołań i ścieżek aktywnych. Pliki śledzenia można także pobrać w celu uzyskania dokładniejszej analizy przy użyciu narzędzia PerfView.

Application Insights można używać w środowiskach różnych:

- Zoptymalizowane pod kątem pracy na platformie Azure.
- Działa w środowisku produkcyjnym, opracowywaniu i przemieszczania.
- Działa lokalnie z [programu Visual Studio](/azure/application-insights/app-insights-visual-studio) lub w innych środowiskach hostingu.

Aby uzyskać więcej informacji, zobacz [Application Insights ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="perfview"></a>Narzędzia PerfView

[Narzędzia PerfView](https://github.com/Microsoft/perfview) to narzędzie do analizy wydajności utworzone przez zespół .NET przeznaczony do diagnozowania problemów z wydajnością programu .NET. Narzędzia PerfView umożliwia analizę użycia procesora CPU, pamięci i zachowania GC, zdarzeń wydajności i czasu zegara ściany.

Możesz dowiedzieć się więcej na temat narzędzia PerfView oraz jak zacząć korzystać z [samouczków wideo narzędzia PerfView](https://channel9.msdn.com/Series/PerfView-Tutorial) lub odczytując Podręcznik użytkownika dostępny w narzędziu lub [witrynie GitHub](https://github.com/Microsoft/perfview).

## <a name="windows-performance-toolkit"></a>Zestaw narzędzi wydajności systemu Windows

[Zestaw narzędzi wydajności systemu Windows](/windows-hardware/test/wpt/) (WPT) składa się z dwóch składników: Windows Performance Recorder (WP) i Windows Performance ANALYZER (WPA). Narzędzia te tworzą szczegółowe profile wydajności systemów operacyjnych i aplikacji systemu Windows. WPT ma bogatsze sposoby wizualizacji danych, ale gromadzenie danych jest mniej wydajne niż narzędzia PerfView.

## <a name="perfcollect"></a>PerfCollect

Chociaż narzędzia PerfView to przydatne narzędzie do analizy wydajności dla scenariuszy platformy .NET, działa ono tylko w systemie Windows, więc nie można używać go do zbierania śladów z ASP.NET Core aplikacji działających w środowiskach systemu Linux.

[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) to skrypt bash, który używa natywnych narzędzi profilowania systemu Linux ([perf](https://perf.wiki.kernel.org/index.php/Main_Page) i [LTTng](https://lttng.org/)) do zbierania śladów w systemie Linux, które mogą być analizowane przez narzędzia PerfView. PerfCollect jest przydatne w przypadku wyświetlenia problemów z wydajnością w środowiskach systemu Linux, w których nie można bezpośrednio użyć narzędzia PerfView. Zamiast tego PerfCollect może zbierać ślady z aplikacji .NET Core, które są następnie analizowane na komputerze z systemem Windows przy użyciu narzędzia PerfView.

Więcej informacji o sposobie instalowania i rozpoczynania pracy z usługą PerfCollect jest dostępnych [w witrynie GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).

## <a name="other-third-party-performance-tools"></a>Inne narzędzia do oceny wydajności innych firm

Poniżej wymieniono niektóre narzędzia do oceny wydajności innych firm, które są przydatne podczas badania wydajności aplikacji .NET Core.

- [MiniProfiler](https://miniprofiler.com/)
- dotTrace i dotMemory z JetBrains
- VTune z firmy Intel
