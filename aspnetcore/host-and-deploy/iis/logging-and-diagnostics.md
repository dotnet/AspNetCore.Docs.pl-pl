---
title: Tworzenie i przekierowywanie dzienników przy użyciu modułu ASP.NET Core
author: rick-anderson
description: Skonfiguruj usługi IIS i moduł ASP.NET Core do przechwytywania dzienników i informacji diagnostycznych.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
ms.openlocfilehash: 9af2311dd1f42cce3547c8215af22d2613453510
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755312"
---
# <a name="log-creation-and-redirection"></a>Tworzenie i przekierowywanie dzienników

Moduł ASP.NET Core przekierowuje dane wyjściowe z konsoli stdout i stderr do dysku, jeśli `stdoutLogEnabled` `stdoutLogFile` atrybuty i `aspNetCore` elementu są ustawione. Wszystkie foldery w `stdoutLogFile` ścieżce są tworzone przez moduł po utworzeniu pliku dziennika. Pula aplikacji musi mieć dostęp do zapisu w lokalizacji, w której zapisano dzienniki (Użyj `IIS AppPool\{APP POOL NAME}` do udostępnienia uprawnienia do zapisu, gdzie symbol zastępczy `{APP POOL NAME}` to nazwa puli aplikacji).

Dzienniki nie są obracane, chyba że zostanie wykonane odtwarzanie procesów/ponowne uruchomienie. Ponosisz odpowiedzialność dostawcy usług hostingowych, aby ograniczyć ilość miejsca na dysku zużywanej przez dzienniki.

Użycie dziennika stdout jest zalecane tylko w przypadku rozwiązywania problemów z uruchamianiem aplikacji w usługach IIS lub w przypadku korzystania z [obsługi usług IIS w czasie projektowania w programie Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), a nie podczas debugowania lokalnego i uruchamiania aplikacji przy użyciu IIS Express.

Nie używaj dziennika stdout w celu uzyskania ogólnych celów rejestrowania aplikacji. Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.

Sygnatura czasowa i rozszerzenie pliku są dodawane automatycznie podczas tworzenia pliku dziennika. Nazwa pliku dziennika jest złożona przez dołączenie sygnatury czasowej, identyfikatora procesu i rozszerzenia pliku ( `.log` ) do ostatniego segmentu `stdoutLogFile` ścieżki (zwykle `stdout` ) rozdzielanej znakami podkreślenia. Jeśli `stdoutLogFile` ścieżka kończy się na `stdout` , dziennik aplikacji o identyfikatorze PID 1934 utworzony w dniu 2/5/2018 o 19:42:32 ma nazwę pliku `stdout_20180205194132_1934.log` .

Jeśli `stdoutLogEnabled` ma wartość false, błędy występujące podczas uruchamiania aplikacji są przechwytywane i emitowane do dziennika zdarzeń do 30 KB. Po uruchomieniu wszystkie dodatkowe dzienniki są odrzucane.

Poniższy przykładowy `aspNetCore` element konfiguruje rejestrowanie stdout w ścieżce względnej `.\log\` . Upewnij się, że tożsamość użytkownika puli aplikacji ma uprawnienia do zapisu w podanej ścieżce.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

W przypadku publikowania aplikacji dla Azure App Service wdrożenia zestaw SDK sieci Web ustawia `stdoutLogFile` wartość na `\\?\%home%\LogFiles\stdout` . `%home`Zmienna środowiskowa jest wstępnie zdefiniowana dla aplikacji hostowanych przez Azure App Service.

Aby utworzyć reguły filtru rejestrowania, zobacz sekcje [Konfiguracja](xref:fundamentals/logging/index#log-filtering) i [filtrowanie dzienników](xref:fundamentals/logging/index#log-filtering) w dokumentacji rejestrowania ASP.NET Core.

Aby uzyskać więcej informacji na temat formatów ścieżki, zobacz [formaty ścieżki plików w systemach Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Udoskonalone dzienniki diagnostyczne

Moduł ASP.NET Core można skonfigurować w celu udostępnienia dzienników diagnostyki rozszerzonej. Dodaj `<handlerSettings>` element do `<aspNetCore>` elementu w `web.config` . Ustawienie w `debugLevel` celu `TRACE` ujawniania większej wierności informacji diagnostycznych:

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

Wszystkie foldery w ścieżce ( `logs` w poprzednim przykładzie) są tworzone przez moduł po utworzeniu pliku dziennika. Pula aplikacji musi mieć dostęp do zapisu w lokalizacji, w której zapisano dzienniki (Użyj `IIS AppPool\{APP POOL NAME}` do udostępnienia uprawnienia do zapisu, gdzie symbol zastępczy `{APP POOL NAME}` to nazwa puli aplikacji).

Wartości poziomu debugowania ( `debugLevel` ) mogą zawierać zarówno poziom, jak i lokalizację.

Poziomy (w kolejności od najmniejszej do największej szczegółowości):

* ERROR
* OSTRZEŻENIE
* INFORMACJE
* TRACE

Lokalizacje (wiele lokalizacji jest dozwolonych):

* KONSOLI
* ELEMENCIE
* PLIK

Ustawienia programu obsługi można także zapewnić za pomocą zmiennych środowiskowych:

* `ASPNETCORE_MODULE_DEBUG_FILE`: Ścieżka do pliku dziennika debugowania. (Domyślnie: `aspnetcore-debug.log` )
* `ASPNETCORE_MODULE_DEBUG`: Debugowanie ustawienia poziomu.

> [!WARNING]
> **Nie** pozostawiaj włączonej rejestracji debugowania w ramach wdrożenia dłużej niż jest to wymagane, aby rozwiązać problem. Rozmiar dziennika nie jest ograniczony. Pozostawienie włączonego dziennika debugowania może spowodować wyczerpanie dostępnego miejsca na dysku i awarię serwera lub usługi App Service.

Zapoznaj się z tematem [Konfiguracja przy użyciu `web.config` ](xref:host-and-deploy/iis/web-config#configuration-with-webconfig) programu, aby zapoznać się z przykładem `aspNetCore` elementu w `web.config` pliku.
