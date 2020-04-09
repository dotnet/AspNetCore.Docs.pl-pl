---
title: Moduł ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować moduł ASP.NET Core do obsługi aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 298d424557600735668217e1ef07ace606dac60b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667301"
---
# <a name="aspnet-core-module"></a>Moduł ASP.NET Core

[Przez Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti)i [Justin Kotalik](https://github.com/jkotalik)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core Module to natywny moduł IIS, który podłącza się do potoku IIS do:

* Hostuj aplikację ASP.NET Core wewnątrz procesu roboczego`w3wp.exe`usługi IIS ( ), zwanego [modelem hostingu w trakcie.](#in-process-hosting-model)
* Przesyłanie dalej żądań sieci web do wewnętrznej bazy danych ASP.NET aplikacji Core z [systemem serwera Kestrel,](xref:fundamentals/servers/kestrel)zwanej [modelem hostingu poza procesem.](#out-of-process-hosting-model)

Obsługiwane wersje systemu Windows:

* Windows 7 lub nowsze
* Windows Server 2008 R2 lub nowszy

Podczas hostingu w procesie moduł używa implementacji serwera w trakcie dla usług IIS, o nazwie IIS HTTP Server (`IISHttpServer`).

Podczas hostingu poza procesem moduł działa tylko z Kestrelem. Moduł nie działa z [http.sys](xref:fundamentals/servers/httpsys).

## <a name="hosting-models"></a>Modele hostingu

### <a name="in-process-hosting-model"></a>Model hostingu w trakcie

ASP.NET aplikacje Core domyślnie do modelu hostingu w trakcie.

Podczas hostingu w procesie obowiązują następujące cechy:

* Serwer HTTP usług`IISHttpServer`IIS ( ) jest używany zamiast serwera [Kestrel.](xref:fundamentals/servers/kestrel) W przypadku w toku [createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) wywołuje: <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*>

  * Zarejestruj `IISHttpServer`plik .
  * Skonfiguruj port i ścieżkę podstawową, na którą serwer powinien nasłuchiwany podczas uruchamiania za modułem ASP.NET Core.
  * Skonfiguruj hosta do przechwytywania błędów uruchamiania.

* [Atrybut requestTimeout](#attributes-of-the-aspnetcore-element) nie ma zastosowania do hostingu w trakcie.

* Udostępnianie puli aplikacji między aplikacjami nie jest obsługiwane. Użyj jednej puli aplikacji na aplikację.

* Podczas korzystania z [narzędzia Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) lub ręcznego umieszczania pliku [app_offline.htm we wdrożeniu](xref:host-and-deploy/iis/index#locked-deployment-files)aplikacja może nie zostać natychmiast zamknięta, jeśli istnieje otwarte połączenie. Na przykład połączenie websocket może opóźnić zamknięcie aplikacji.

* Architektura (bitness) aplikacji i zainstalowanego środowiska wykonawczego (x64 lub x86) musi odpowiadać architekturze puli aplikacji.

* Wykryto rozłączenie klienta. Token anulowania [httpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) jest anulowany, gdy klient rozłącza się.

* W ASP.NET Core 2.2.1 lub <xref:System.IO.Directory.GetCurrentDirectory*> starszym zwraca katalog roboczy procesu uruchomionego przez usługi IIS, a nie katalog aplikacji (na przykład *C:\Windows\System32\inetsrv* dla *w3wp.exe*).

  Przykładowy kod, który ustawia bieżący katalog aplikacji, zobacz [CurrentDirectoryHelpers klasy](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs). Wywołanie `SetCurrentDirectory` metody. Kolejne wywołania w celu <xref:System.IO.Directory.GetCurrentDirectory*> zapewnienia katalogu aplikacji.

* Podczas hostingu w <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> procesie, nie jest wywoływana wewnętrznie, aby zainicjować użytkownika. W związku <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> z tym implementacja używana do przekształcania oświadczeń po każdym uwierzytelnianiu nie jest domyślnie aktywowana. Podczas przekształcania oświadczeń <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> za <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> pomocą implementacji, wywołanie dodawania usług uwierzytelniania:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * [Wdrożenia pakietu sieci Web (z jednym plikiem)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) nie są obsługiwane.

### <a name="out-of-process-hosting-model"></a>Model hostingu poza procesem

Aby skonfigurować aplikację do hostingu poza procesem, ustaw `<AspNetCoreHostingModel>` wartość `OutOfProcess` właściwości w pliku projektu (*.csproj*):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Hosting w trakcie jest `InProcess`ustawiony z , który jest wartością domyślną.

Wartość `<AspNetCoreHostingModel>` jest bez uwzględniania wielkości liter, więc `inprocess` i `outofprocess` są prawidłowe wartości.

[Serwer pustułki](xref:fundamentals/servers/kestrel) jest używany zamiast serwera`IISHttpServer`HTTP usług IIS ( ).

W przypadku brakuprocesu [createDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) wywołuje: <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>

* Skonfiguruj port i ścieżkę podstawową, na którą serwer powinien nasłuchiwany podczas uruchamiania za modułem ASP.NET Core.
* Skonfiguruj hosta do przechwytywania błędów uruchamiania.

### <a name="hosting-model-changes"></a>Zmiany modelu hostingu

Jeśli `hostingModel` ustawienie zostanie zmienione w pliku *web.config* (wyjaśnione w [sekcji Konfiguracja z web.config),](#configuration-with-webconfig) moduł odtwarza proces roboczy dla usług IIS.

W przypadku usługi IIS Express moduł nie odtwarza procesu roboczego, ale zamiast tego wyzwala pełne wdzięku zamknięcie bieżącego procesu usługi IIS Express. Następne żądanie do aplikacji tworzy nowy proces IIS Express.

### <a name="process-name"></a>Nazwa procesu

`Process.GetCurrentProcess().ProcessName``w3wp` / `iisexpress` (w toku) lub `dotnet` (poza procesem).

Wiele modułów natywnych, takich jak uwierzytelnianie systemu Windows, pozostaje aktywnych. Aby dowiedzieć się więcej o modułach usług IIS <xref:host-and-deploy/iis/modules>aktywnych za pomocą ASP.NET Core Module, zobacz .

ASP.NET Moduł podstawowy może również:

* Ustawianie zmiennych środowiskowych dla procesu roboczego.
* Dane wyjściowe stdout do magazynu plików w celu rozwiązywania problemów z uruchamianiem.
* Przesyłanie dalej tokenów uwierzytelniania systemu Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Jak zainstalować i korzystać z modułu ASP.NET Core

Aby uzyskać instrukcje dotyczące instalowania modułu ASP.NET Core, zobacz [Instalowanie pakietu hostingowego .NET Core .](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)

## <a name="configuration-with-webconfig"></a>Konfiguracja z web.config

Moduł ASP.NET Core jest skonfigurowany z `aspNetCore` sekcją `system.webServer` węzła w pliku *web.config* witryny.

Następujący plik *web.config* jest publikowany dla [wdrożenia zależnego od struktury](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) i konfiguruje ASP.NET Core Module do obsługi żądań lokacji:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Następujące *web.config* jest publikowany dla [samodzielnego wdrożenia:](/dotnet/articles/core/deploying/#self-contained-deployments-scd)

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Właściwość jest <xref:System.Configuration.SectionInformation.InheritInChildApplications*> `false` ustawiona, aby wskazać, że ustawienia określone w [ \<lokalizacji>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nie są dziedziczone przez aplikacje, które znajdują się w podkatalogu aplikacji.

Gdy aplikacja jest wdrażana w [usłudze Azure App Service,](https://azure.microsoft.com/services/app-service/)ścieżka jest ustawiona `stdoutLogFile` na `\\?\%home%\LogFiles\stdout`. Ścieżka zapisuje dzienniki stdout do folderu *LogFiles,* który jest lokalizacją automatycznie utworzoną przez usługę.

Aby uzyskać informacje na temat konfiguracji <xref:host-and-deploy/iis/index#sub-applications>podpołowieczek usług IIS, zobacz .

### <a name="attributes-of-the-aspnetcore-element"></a>Atrybuty elementu aspNetCore

| Atrybut | Opis | Domyślne |
| --------- | ----------- | :-----: |
| `arguments` | <p>Opcjonalny atrybut ciągu.</p><p>Argumenty do pliku wykonywalnego określonego w **processPath**.</p> | |
| `disableStartUpErrorPage` | <p>Opcjonalny atrybut logiczny.</p><p>Jeśli true, **502.5 - Błąd procesu** strona jest pomijana, a strona kod stanu 502 skonfigurowana w *web.config* ma pierwszeństwo.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Opcjonalny atrybut logiczny.</p><p>Jeśli true, token jest przekazywanie dalej do procesu podrzędnego nasłuchiwania na %ASPNETCORE_PORT% jako nagłówek "MS-ASPNETCORE-WINAUTHTOKEN" na żądanie. Jest odpowiedzialny za tego procesu do wywołania CloseHandle na ten token na żądanie.</p> | `true` |
| `hostingModel` | <p>Opcjonalny atrybut ciągu.</p><p>Określa model hostingu jako w`InProcess`/`inprocess`trakcie ( ) lub`OutOfProcess`/`outofprocess`poza procesem ( ).</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>Opcjonalny atrybut liczby całkowitej.</p><p>Określa liczbę wystąpień procesu określonego w **ustawieniu processPath,** które można obrócić na aplikację.</p><p>&dagger;W przypadku hostingu w trakcie `1`wartość jest ograniczona do .</p><p>Ustawienie `processesPerApplication` jest odradzane. Ten atrybut zostanie usunięty w przyszłej wersji.</p> | Domyślny:`1`<br>Min:`1`<br>Max:`100`&dagger; |
| `processPath` | <p>Wymagany atrybut ciągu.</p><p>Ścieżka do pliku wykonywalnego, który uruchamia proces nasłuchiwania żądań HTTP. Ścieżki względne są obsługiwane. Jeśli ścieżka zaczyna `.`się od , ścieżka jest uważana za względem względem katalogu głównego witryny.</p> | |
| `rapidFailsPerMinute` | <p>Opcjonalny atrybut liczby całkowitej.</p><p>Określa, ile razy proces określony w **processPath** może ulec awarii na minutę. Jeśli ten limit zostanie przekroczony, moduł przestaje uruchamiać proces przez pozostałą część minuty.</p><p>Nie jest obsługiwany w trakcie hostingu.</p> | Domyślny:`10`<br>Min:`0`<br>Max:`100` |
| `requestTimeout` | <p>Opcjonalny atrybut przedziału czasu.</p><p>Określa czas trwania oczekiwania modułu ASP.NET Core module na odpowiedź z procesu nasłuchiwania na %ASPNETCORE_PORT%.</p><p>W wersjach modułu ASP.NET Core, który został dostarczony wraz z wydaniem ASP.NET Core 2.1 lub nowszym, `requestTimeout` jest określony w godzinach, minutach i sekundach.</p><p>Nie dotyczy hostingu w trakcie. W przypadku hostingu w trakcie moduł czeka na przetworzenie żądania przez aplikację.</p><p>Prawidłowe wartości dla minut i sekund segmentów ciągu znajdują się w zakresie 0-59. Użycie **60** w wartości minut lub sekund powoduje *500 - Wewnętrzny błąd serwera*.</p> | Domyślny:`00:02:00`<br>Min:`00:00:00`<br>Max:`360:00:00` |
| `shutdownTimeLimit` | <p>Opcjonalny atrybut liczby całkowitej.</p><p>Czas trwania w sekundach, że moduł czeka na plik wykonywalny bezpiecznie zamknąć po wykryciu *pliku app_offline.htm.*</p> | Domyślny:`10`<br>Min:`0`<br>Max:`600` |
| `startupTimeLimit` | <p>Opcjonalny atrybut liczby całkowitej.</p><p>Czas trwania w sekundach, że moduł czeka na plik wykonywalny, aby rozpocząć proces nasłuchiwania na porcie. Jeśli ten limit czasu zostanie przekroczony, moduł zabija proces. Moduł próbuje ponownie uruchomić proces po otrzymaniu nowego żądania i kontynuuje próbę ponownego uruchomienia procesu na kolejne żądania przychodzące, chyba że aplikacja nie może uruchomić **rapidFailsPerMinute** liczba razy w ostatniej toczenia minut.</p><p>Wartość 0 (zero) **nie** jest uważany za nieskończony limit czasu.</p> | Domyślny:`120`<br>Min:`0`<br>Max:`3600` |
| `stdoutLogEnabled` | <p>Opcjonalny atrybut logiczny.</p><p>Jeśli true, **stdout** i **stderr** dla procesu określonego w **processPath** są przekierowywane do pliku określonego w **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Opcjonalny atrybut ciągu.</p><p>Określa względną lub bezwzględną ścieżkę pliku, dla której są rejestrowane **stdout** i **stderr** z procesu określonego w **processPath.** Ścieżki względne są względem katalogu głównego witryny. Każda ścieżka `.` rozpoczynająca się względem katalogu głównego witryny i wszystkie inne ścieżki są traktowane jako ścieżki bezwzględne. Wszystkie foldery znajdujące się w ścieżce są tworzone przez moduł podczas tworzenia pliku dziennika. Za pomocą ograniczników podkreślenia do ostatniego segmentu ścieżki **pliku stdoutLogFile** dodaje się znacznik czasu, identyfikator procesu i rozszerzenie pliku (*.log).* Jeśli `.\logs\stdout` jest dostarczany jako wartość, przykładowy dziennik stdout jest zapisywany jako *stdout_20180205194132_1934.log* w folderze *dzienników* po zapisaniu w 2/5/2018 o godzinie 19:41:32 z identyfikatorem procesu 1934.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Zmienne środowiskowe można określić `processPath` dla procesu w atrybucie. Określ zmienną `<environmentVariable>` środowiskową z `<environmentVariables>` elementem podrzędnym elementu kolekcji. Zmienne środowiskowe ustawione w tej sekcji mają pierwszeństwo przed zmiennymi środowiska systemowego.

W poniższym przykładzie ustawia się dwie zmienne środowiskowe w *pliku web.config*. `ASPNETCORE_ENVIRONMENT` konfiguruje środowisko aplikacji `Development`na . Deweloper może tymczasowo ustawić tę wartość w pliku *web.config,* aby wymusić na [stronie wyjątku dewelopera](xref:fundamentals/error-handling) załadować podczas debugowania wyjątku aplikacji. `CONFIG_DIR`jest przykładem zmiennej środowiskowej zdefiniowanej przez użytkownika, gdzie deweloper napisał kod, który odczytuje wartość podczas uruchamiania, tworząc ścieżkę do ładowania pliku konfiguracyjnego aplikacji.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Alternatywą dla ustawienia środowiska bezpośrednio w *web.config* jest uwzględnienie `<EnvironmentName>` właściwości w profilu publikowania [(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) lub pliku projektu. Takie podejście ustawia środowisko w *web.config* po opublikowaniu projektu:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Zmienną `ASPNETCORE_ENVIRONMENT` środowiskową `Development` należy ustawić tylko na serwery przejściowe i testujące, które nie są dostępne dla niezaufanych sieci, takich jak Internet.

## <a name="app_offlinehtm"></a>app_offline.htm

Jeśli plik o nazwie *app_offline.htm* zostanie wykryty w katalogu głównym aplikacji, moduł ASP.NET Core próbuje bezpiecznie zamknąć aplikację i zatrzymać przetwarzanie żądań przychodzących. Jeśli aplikacja jest nadal uruchomiona po liczbie `shutdownTimeLimit`sekund zdefiniowanych w , ASP.NET moduł podstawowy zabija uruchomiony proces.

Podczas gdy plik *app_offline.htm* jest obecny, ASP.NET Core Module odpowiada na żądania, wysyłając z powrotem zawartość pliku *app_offline.htm.* Po usunięciu pliku *app_offline.htm* następne żądanie uruchamia aplikację.

Podczas korzystania z modelu hostingu poza procesem aplikacja może nie zostać natychmiast zamknięta, jeśli istnieje otwarte połączenie. Na przykład połączenie websocket może opóźnić zamknięcie aplikacji.

## <a name="start-up-error-page"></a>Strona błędu uruchamiania

Hosting w trakcie, jak i poza procesem powoduje tworzenie niestandardowych stron błędów, gdy nie można uruchomić aplikacji.

Jeśli ASP.NET moduł podstawowy nie może znaleźć programu obsługi żądań w toku lub poza procesem, zostanie wyświetlona strona kodu stanu awarii ładowania w trakcie lub poza *procesem.*

W przypadku hostingu w trakcie, jeśli ASP.NET Moduł podstawowy nie może uruchomić aplikacji, zostanie wyświetlona strona kodu stanu *500.30 — Start Failure.*

W przypadku hostingu poza procesem, jeśli ASP.NET modułu rdzenia nie uruchamia procesu wewnętrznej bazy danych lub rozpocznie się proces wewnętrznej bazy danych, ale nie nasłuchuje na skonfigurowanym porcie, zostanie wyświetlona strona kodowa stanu *awarii procesu 502.5.*

Aby pominąć tę stronę i powrócić do domyślnej strony kodowej `disableStartUpErrorPage` stanu IIS 5xx, użyj atrybutu. Aby uzyskać więcej informacji na temat konfigurowania niestandardowych komunikatów o [błędach, zobacz Błędy \<HTTP httpErrors>](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Tworzenie i przekierowywanie w dzienniku

Moduł ASP.NET Core przekierowuje wyjście konsoli stdout i stderr na dysk, `stdoutLogEnabled` jeśli ustawiono atrybuty i `stdoutLogFile` atrybuty `aspNetCore` elementu. Wszystkie foldery `stdoutLogFile` w ścieżce są tworzone przez moduł podczas tworzenia pliku dziennika. Pula aplikacji musi mieć dostęp do zapisu do lokalizacji, w której są zapisywane dzienniki (służy `IIS AppPool\<app_pool_name>` do udzielania uprawnień do zapisu).

Dzienniki nie są obracane, chyba że proces recyklingu /ponownego uruchamiania występuje. Jest odpowiedzialny za hosta, aby ograniczyć miejsce na dysku dzienniki zużywają.

Używanie dziennika stdout jest zalecane tylko do rozwiązywania problemów z uruchamianiem aplikacji podczas hostingu w serwisie IIS lub podczas korzystania [z obsługi programów IIS w czasie tworzenia w programie Visual Studio,](xref:host-and-deploy/iis/development-time-iis-support)a nie podczas debugowania lokalnie i uruchamiania aplikacji za pomocą usługi IIS Express.

Nie używaj dziennika stdout do ogólnych celów rejestrowania aplikacji. W przypadku rutynowego rejestrowania w aplikacji ASP.NET Core należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).

Sygnatura czasowa i rozszerzenie pliku są dodawane automatycznie podczas tworzenia pliku dziennika. Nazwa pliku dziennika składa się przez dołączenie sygnatury czasowej, identyfikatora procesu i `stdoutLogFile` rozszerzenia pliku *(.log*) do ostatniego segmentu ścieżki (zazwyczaj *stdout)* rozdzielonego przez podkreślenia. Jeśli `stdoutLogFile` ścieżka kończy się *stdout*, dziennik dla aplikacji z PID 1934 utworzony w dniu 2/5/2018 o 19:42:32 ma nazwę pliku *stdout_20180205194132_1934.log*.

Jeśli `stdoutLogEnabled` jest false, błędy, które występują podczas uruchamiania aplikacji są przechwytywane i emitowane do dziennika zdarzeń do 30 KB. Po uruchomieniu wszystkie dodatkowe dzienniki są odrzucane.

Poniższy `aspNetCore` przykładowy element konfiguruje rejestrowanie `.\log\`stdout w ścieżce względnej . Upewnij się, że tożsamość użytkownika AppPool ma uprawnienia do zapisu w podanej ścieżce.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Podczas publikowania aplikacji dla wdrożenia usługi Azure App Service `stdoutLogFile` zestaw `\\?\%home%\LogFiles\stdout`SDK sieci Web ustawia wartość . Zmienna środowiskowa `%home` jest wstępnie zdefiniowana dla aplikacji obsługiwanych przez usługę Azure App Service.

Aby utworzyć reguły filtrowania rejestrowania, zobacz sekcje [filtrowania](xref:fundamentals/logging/index#log-filtering) [konfiguracji](xref:fundamentals/logging/index#log-filtering) i dziennika w dokumentacji rejestrowania ASP.NET Core.

Aby uzyskać więcej informacji na temat formatów ścieżek, zobacz [Formaty ścieżek plików w systemach Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Ulepszone dzienniki diagnostyczne

Moduł ASP.NET Core można skonfigurować w celu zapewnienia rozszerzonych dzienników diagnostycznych. Dodaj `<handlerSettings>` element do `<aspNetCore>` elementu w *web.config*. Ustawienie, `debugLevel` `TRACE` aby uwidacznia wyższą wierność informacji diagnostycznych:

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

Wszystkie foldery w ścieżce *(dzienniki* w poprzednim przykładzie) są tworzone przez moduł podczas tworzenia pliku dziennika. Pula aplikacji musi mieć dostęp do zapisu do lokalizacji, w której są zapisywane dzienniki (służy `IIS AppPool\<app_pool_name>` do udzielania uprawnień do zapisu).

Wartości poziomu`debugLevel`debugowania ( ) mogą obejmować zarówno poziom, jak i lokalizację.

Poziomy (w kolejności od najmniej do większości pełnych):

* BŁĄD
* OSTRZEŻENIE
* Informacji
* TRACE

Lokalizacje (dozwolone jest wiele lokalizacji):

* Konsoli
* Eventlog
* PLIK

Ustawienia obsługi mogą być również dostarczane za pośrednictwem zmiennych środowiskowych:

* `ASPNETCORE_MODULE_DEBUG_FILE`&ndash; Ścieżka do pliku dziennika debugowania. (Domyślnie: *aspnetcore-debug.log)*
* `ASPNETCORE_MODULE_DEBUG`&ndash; Ustawienie poziomu debugowania.

> [!WARNING]
> **Nie** należy pozostawiać rejestrowania debugowania włączone we wdrożeniu dłużej niż wymagane do rozwiązania problemu. Rozmiar dziennika nie jest ograniczony. Pozostawienie włączonego dziennika debugowania może wyczerpyć dostępne miejsce na dysku i upaść serwer lub usługę aplikacji.

Zobacz [Konfiguracja z web.config](#configuration-with-webconfig) `aspNetCore` na przykład elementu w pliku *web.config.*

## <a name="modify-the-stack-size"></a>Modyfikowanie rozmiaru stosu

*Dotyczy tylko podczas korzystania z modelu hostingu w trakcie.*

Skonfiguruj rozmiar `stackSize` zarządzanego stosu przy użyciu ustawienia w bajtach w *pliku web.config*. Domyślny rozmiar `1048576` to bajty (1 MB).

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Konfiguracja serwera proxy używa protokołu HTTP i tokenu parowania

*Dotyczy tylko hostingu poza procesem.*

Serwer proxy utworzony między ASP.NET Core Module a Kestrel używa protokołu HTTP. Nie ma ryzyka podsłuchiwania ruchu między modułem a Pustułką z lokalizacji poza serwerem.

Token parowania jest używany do zagwarantowania, że żądania odebrane przez Kestrel zostały wysłane przez usługi IIS i nie pochodzą z innego źródła. Token parowania jest tworzony i ustawiany`ASPNETCORE_TOKEN`w zmiennej środowiskowej ( ) przez moduł. Token parowania jest również ustawiony`MS-ASPNETCORE-TOKEN`w nagłówku ( ) przy każdym proxied żądania. Oprogramowanie pośredniczące usługi IIS sprawdza każde otrzymane żądanie, aby potwierdzić, że wartość nagłówka tokenu parowania jest zgodna z wartością zmiennej środowiskowej. Jeśli wartości tokenu są niezgodne, żądanie jest rejestrowane i odrzucane. Zmienna środowiska tokenu parowania i ruch między modułem a Kestrel nie są dostępne z lokalizacji poza serwerem. Bez znajomości wartości tokenu parowania osoba atakująca nie może przesłać żądań, które omijają sprawdzanie w oprogramowaniu pośredniczącym usługi IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>ASP.NET modułu rdzenia z konfiguracją współdzieloną usług IIS

Instalator modułu ASP.NET Core module działa z uprawnieniami konta **TrustedInstaller.** Ponieważ lokalne konto systemowe nie ma uprawnień do modyfikowania ścieżki udziału używanej przez konfigurację współużytkową usług IIS, instalator zgłasza błąd odmowy dostępu podczas próby skonfigurowania ustawień modułu w pliku *applicationHost.config* w udziale.

W przypadku korzystania z udostępnionej konfiguracji usług IIS na tym samym komputerze co instalacja `OPT_NO_SHARED_CONFIG_CHECK` usług IIS uruchom instalator ASP.NET Core Hosting Bundle z parametrem ustawionym na: `1`

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Jeśli ścieżka do konfiguracji udostępnionej nie jest na tym samym komputerze co instalacja usług IIS, wykonaj następujące kroki:

1. Wyłącz konfigurację udostępnioną usług IIS.
1. Uruchom instalatora.
1. Wyeksportuj zaktualizowany plik *applicationHost.config* do udziału.
1. Ponownie włącz konfigurację udostępnioną usług IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Dane dotyczące wersji modułu i dzienników instalatora pakietu hostingowego

Aby określić wersję zainstalowanego ASP.NET modułu rdzenia:

1. W systemie hostingowym przejdź do *%windir%\System32\inetsrv*.
1. Znajdź plik *aspnetcore.dll.*
1. Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości** z menu kontekstowego.
1. Wybierz kartę **Szczegóły.** **Wersja pliku** i **wersja produktu** reprezentują zainstalowaną wersję modułu.

Dzienniki instalatora pakietu hostingowego dla modułu znajdują się pod adresem *C:\\\\Users %UserName%\\AppData\\Local\\Temp*. Plik nosi nazwę *dd_DotNetCoreWinSvrHosting__\<sygnatura czasowa>_000_AspNetCoreModule_x64.log*.

## <a name="module-schema-and-configuration-file-locations"></a>Lokalizacje plików modułów, schematów i konfiguracji

### <a name="module"></a>Moduł

**IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

### <a name="schema"></a>Schemat

**IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Konfigurowanie

**IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {APPLICATION\\ROOT} .vs\config\applicationHost.config

* *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Pliki można znaleźć, wyszukując *aspnetcore* w pliku *applicationHost.config.*

::: moniker-end

::: moniker range="= aspnetcore-2.2"

ASP.NET Core Module to natywny moduł IIS, który podłącza się do potoku IIS do:

* Hostuj aplikację ASP.NET Core wewnątrz procesu roboczego`w3wp.exe`usługi IIS ( ), zwanego [modelem hostingu w trakcie.](#in-process-hosting-model)
* Przesyłanie dalej żądań sieci web do wewnętrznej bazy danych ASP.NET aplikacji Core z [systemem serwera Kestrel,](xref:fundamentals/servers/kestrel)zwanej [modelem hostingu poza procesem.](#out-of-process-hosting-model)

Obsługiwane wersje systemu Windows:

* Windows 7 lub nowsze
* Windows Server 2008 R2 lub nowszy

Podczas hostingu w procesie moduł używa implementacji serwera w trakcie dla usług IIS, o nazwie IIS HTTP Server (`IISHttpServer`).

Podczas hostingu poza procesem moduł działa tylko z Kestrelem. Moduł nie działa z [http.sys](xref:fundamentals/servers/httpsys).

## <a name="hosting-models"></a>Modele hostingu

### <a name="in-process-hosting-model"></a>Model hostingu w trakcie

Aby skonfigurować aplikację do hostingu w `<AspNetCoreHostingModel>` trakcie, dodaj właściwość do pliku `InProcess` projektu aplikacji o wartości (hosting poza procesem jest ustawiony z): `OutOfProcess`

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Model hostingu w trakcie nie jest obsługiwany dla aplikacji ASP.NET Core, które są przeznaczone dla platformy .NET Framework.

Wartość `<AspNetCoreHostingModel>` jest bez uwzględniania wielkości liter, więc `inprocess` i `outofprocess` są prawidłowe wartości.

Jeśli `<AspNetCoreHostingModel>` właściwości nie ma w pliku, wartością `OutOfProcess`domyślną jest .

Podczas hostingu w procesie obowiązują następujące cechy:

* Serwer HTTP usług`IISHttpServer`IIS ( ) jest używany zamiast serwera [Kestrel.](xref:fundamentals/servers/kestrel) W przypadku w toku [createdefaultbuilder](xref:fundamentals/host/web-host#set-up-a-host) wywołuje: <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*>

  * Zarejestruj `IISHttpServer`plik .
  * Skonfiguruj port i ścieżkę podstawową, na którą serwer powinien nasłuchiwany podczas uruchamiania za modułem ASP.NET Core.
  * Skonfiguruj hosta do przechwytywania błędów uruchamiania.

* [Atrybut requestTimeout](#attributes-of-the-aspnetcore-element) nie ma zastosowania do hostingu w trakcie.

* Udostępnianie puli aplikacji między aplikacjami nie jest obsługiwane. Użyj jednej puli aplikacji na aplikację.

* Podczas korzystania z [narzędzia Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) lub ręcznego umieszczania pliku [app_offline.htm we wdrożeniu](xref:host-and-deploy/iis/index#locked-deployment-files)aplikacja może nie zostać natychmiast zamknięta, jeśli istnieje otwarte połączenie. Na przykład połączenie websocket może opóźnić zamknięcie aplikacji.

* Architektura (bitness) aplikacji i zainstalowanego środowiska wykonawczego (x64 lub x86) musi odpowiadać architekturze puli aplikacji.

* Wykryto rozłączenie klienta. Token anulowania [httpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) jest anulowany, gdy klient rozłącza się.

* W ASP.NET Core 2.2.1 lub <xref:System.IO.Directory.GetCurrentDirectory*> starszym zwraca katalog roboczy procesu uruchomionego przez usługi IIS, a nie katalog aplikacji (na przykład *C:\Windows\System32\inetsrv* dla *w3wp.exe*).

  Przykładowy kod, który ustawia bieżący katalog aplikacji, zobacz [CurrentDirectoryHelpers klasy](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs). Wywołanie `SetCurrentDirectory` metody. Kolejne wywołania w celu <xref:System.IO.Directory.GetCurrentDirectory*> zapewnienia katalogu aplikacji.

* Podczas hostingu w <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> procesie, nie jest wywoływana wewnętrznie, aby zainicjować użytkownika. W związku <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> z tym implementacja używana do przekształcania oświadczeń po każdym uwierzytelnianiu nie jest domyślnie aktywowana. Podczas przekształcania oświadczeń <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> za <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> pomocą implementacji, wywołanie dodawania usług uwierzytelniania:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a>Model hostingu poza procesem

Aby skonfigurować aplikację do hostingu poza procesem, użyj jednego z następujących metod w pliku projektu:

* Nie określaj `<AspNetCoreHostingModel>` właściwości. Jeśli `<AspNetCoreHostingModel>` właściwości nie ma w pliku, wartością `OutOfProcess`domyślną jest .
* Ustaw wartość `<AspNetCoreHostingModel>` właściwości (hosting `OutOfProcess` w trakcie jest `InProcess`ustawiony z):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Wartość jest bez uwzględniania wielkości `inprocess` liter, więc i `outofprocess` są prawidłowe wartości.

[Serwer pustułki](xref:fundamentals/servers/kestrel) jest używany zamiast serwera`IISHttpServer`HTTP usług IIS ( ).

W przypadku brakuprocesu [createDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) wywołuje: <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>

* Skonfiguruj port i ścieżkę podstawową, na którą serwer powinien nasłuchiwany podczas uruchamiania za modułem ASP.NET Core.
* Skonfiguruj hosta do przechwytywania błędów uruchamiania.

### <a name="hosting-model-changes"></a>Zmiany modelu hostingu

Jeśli `hostingModel` ustawienie zostanie zmienione w pliku *web.config* (wyjaśnione w [sekcji Konfiguracja z web.config),](#configuration-with-webconfig) moduł odtwarza proces roboczy dla usług IIS.

W przypadku usługi IIS Express moduł nie odtwarza procesu roboczego, ale zamiast tego wyzwala pełne wdzięku zamknięcie bieżącego procesu usługi IIS Express. Następne żądanie do aplikacji tworzy nowy proces IIS Express.

### <a name="process-name"></a>Nazwa procesu

`Process.GetCurrentProcess().ProcessName``w3wp` / `iisexpress` (w toku) lub `dotnet` (poza procesem).

Wiele modułów natywnych, takich jak uwierzytelnianie systemu Windows, pozostaje aktywnych. Aby dowiedzieć się więcej o modułach usług IIS <xref:host-and-deploy/iis/modules>aktywnych za pomocą ASP.NET Core Module, zobacz .

ASP.NET Moduł podstawowy może również:

* Ustawianie zmiennych środowiskowych dla procesu roboczego.
* Dane wyjściowe stdout do magazynu plików w celu rozwiązywania problemów z uruchamianiem.
* Przesyłanie dalej tokenów uwierzytelniania systemu Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Jak zainstalować i korzystać z modułu ASP.NET Core

Aby uzyskać instrukcje dotyczące instalowania modułu ASP.NET Core, zobacz [Instalowanie pakietu hostingowego .NET Core .](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)

## <a name="configuration-with-webconfig"></a>Konfiguracja z web.config

Moduł ASP.NET Core jest skonfigurowany z `aspNetCore` sekcją `system.webServer` węzła w pliku *web.config* witryny.

Następujący plik *web.config* jest publikowany dla [wdrożenia zależnego od struktury](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) i konfiguruje ASP.NET Core Module do obsługi żądań lokacji:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Następujące *web.config* jest publikowany dla [samodzielnego wdrożenia:](/dotnet/articles/core/deploying/#self-contained-deployments-scd)

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Właściwość jest <xref:System.Configuration.SectionInformation.InheritInChildApplications*> `false` ustawiona, aby wskazać, że ustawienia określone w [ \<lokalizacji>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nie są dziedziczone przez aplikacje, które znajdują się w podkatalogu aplikacji.

Gdy aplikacja jest wdrażana w [usłudze Azure App Service,](https://azure.microsoft.com/services/app-service/)ścieżka jest ustawiona `stdoutLogFile` na `\\?\%home%\LogFiles\stdout`. Ścieżka zapisuje dzienniki stdout do folderu *LogFiles,* który jest lokalizacją automatycznie utworzoną przez usługę.

Aby uzyskać informacje na temat konfiguracji <xref:host-and-deploy/iis/index#sub-applications>podpołowieczek usług IIS, zobacz .

### <a name="attributes-of-the-aspnetcore-element"></a>Atrybuty elementu aspNetCore

| Atrybut | Opis | Domyślne |
| --------- | ----------- | :-----: |
| `arguments` | <p>Opcjonalny atrybut ciągu.</p><p>Argumenty do pliku wykonywalnego określonego w **processPath**.</p> | |
| `disableStartUpErrorPage` | <p>Opcjonalny atrybut logiczny.</p><p>Jeśli true, **502.5 - Błąd procesu** strona jest pomijana, a strona kod stanu 502 skonfigurowana w *web.config* ma pierwszeństwo.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Opcjonalny atrybut logiczny.</p><p>Jeśli true, token jest przekazywanie dalej do procesu podrzędnego nasłuchiwania na %ASPNETCORE_PORT% jako nagłówek "MS-ASPNETCORE-WINAUTHTOKEN" na żądanie. Jest odpowiedzialny za tego procesu do wywołania CloseHandle na ten token na żądanie.</p> | `true` |
| `hostingModel` | <p>Opcjonalny atrybut ciągu.</p><p>Określa model hostingu jako w`InProcess`/`inprocess`trakcie ( ) lub`OutOfProcess`/`outofprocess`poza procesem ( ).</p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p>Opcjonalny atrybut liczby całkowitej.</p><p>Określa liczbę wystąpień procesu określonego w **ustawieniu processPath,** które można obrócić na aplikację.</p><p>&dagger;W przypadku hostingu w trakcie `1`wartość jest ograniczona do .</p><p>Ustawienie `processesPerApplication` jest odradzane. Ten atrybut zostanie usunięty w przyszłej wersji.</p> | Domyślny:`1`<br>Min:`1`<br>Max:`100`&dagger; |
| `processPath` | <p>Wymagany atrybut ciągu.</p><p>Ścieżka do pliku wykonywalnego, który uruchamia proces nasłuchiwania żądań HTTP. Ścieżki względne są obsługiwane. Jeśli ścieżka zaczyna `.`się od , ścieżka jest uważana za względem względem katalogu głównego witryny.</p> | |
| `rapidFailsPerMinute` | <p>Opcjonalny atrybut liczby całkowitej.</p><p>Określa, ile razy proces określony w **processPath** może ulec awarii na minutę. Jeśli ten limit zostanie przekroczony, moduł przestaje uruchamiać proces przez pozostałą część minuty.</p><p>Nie jest obsługiwany w trakcie hostingu.</p> | Domyślny:`10`<br>Min:`0`<br>Max:`100` |
| `requestTimeout` | <p>Opcjonalny atrybut przedziału czasu.</p><p>Określa czas trwania oczekiwania modułu ASP.NET Core module na odpowiedź z procesu nasłuchiwania na %ASPNETCORE_PORT%.</p><p>W wersjach modułu ASP.NET Core, który został dostarczony wraz z wydaniem ASP.NET Core 2.1 lub nowszym, `requestTimeout` jest określony w godzinach, minutach i sekundach.</p><p>Nie dotyczy hostingu w trakcie. W przypadku hostingu w trakcie moduł czeka na przetworzenie żądania przez aplikację.</p><p>Prawidłowe wartości dla minut i sekund segmentów ciągu znajdują się w zakresie 0-59. Użycie **60** w wartości minut lub sekund powoduje *500 - Wewnętrzny błąd serwera*.</p> | Domyślny:`00:02:00`<br>Min:`00:00:00`<br>Max:`360:00:00` |
| `shutdownTimeLimit` | <p>Opcjonalny atrybut liczby całkowitej.</p><p>Czas trwania w sekundach, że moduł czeka na plik wykonywalny bezpiecznie zamknąć po wykryciu *pliku app_offline.htm.*</p> | Domyślny:`10`<br>Min:`0`<br>Max:`600` |
| `startupTimeLimit` | <p>Opcjonalny atrybut liczby całkowitej.</p><p>Czas trwania w sekundach, że moduł czeka na plik wykonywalny, aby rozpocząć proces nasłuchiwania na porcie. Jeśli ten limit czasu zostanie przekroczony, moduł zabija proces. Moduł próbuje ponownie uruchomić proces po otrzymaniu nowego żądania i kontynuuje próbę ponownego uruchomienia procesu na kolejne żądania przychodzące, chyba że aplikacja nie może uruchomić **rapidFailsPerMinute** liczba razy w ostatniej toczenia minut.</p><p>Wartość 0 (zero) **nie** jest uważany za nieskończony limit czasu.</p> | Domyślny:`120`<br>Min:`0`<br>Max:`3600` |
| `stdoutLogEnabled` | <p>Opcjonalny atrybut logiczny.</p><p>Jeśli true, **stdout** i **stderr** dla procesu określonego w **processPath** są przekierowywane do pliku określonego w **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Opcjonalny atrybut ciągu.</p><p>Określa względną lub bezwzględną ścieżkę pliku, dla której są rejestrowane **stdout** i **stderr** z procesu określonego w **processPath.** Ścieżki względne są względem katalogu głównego witryny. Każda ścieżka `.` rozpoczynająca się względem katalogu głównego witryny i wszystkie inne ścieżki są traktowane jako ścieżki bezwzględne. Wszystkie foldery znajdujące się w ścieżce są tworzone przez moduł podczas tworzenia pliku dziennika. Za pomocą ograniczników podkreślenia do ostatniego segmentu ścieżki **pliku stdoutLogFile** dodaje się znacznik czasu, identyfikator procesu i rozszerzenie pliku (*.log).* Jeśli `.\logs\stdout` jest dostarczany jako wartość, przykładowy dziennik stdout jest zapisywany jako *stdout_20180205194132_1934.log* w folderze *dzienników* po zapisaniu w 2/5/2018 o godzinie 19:41:32 z identyfikatorem procesu 1934.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Ustawianie zmiennych środowiskowych

Zmienne środowiskowe można określić `processPath` dla procesu w atrybucie. Określ zmienną `<environmentVariable>` środowiskową z `<environmentVariables>` elementem podrzędnym elementu kolekcji. Zmienne środowiskowe ustawione w tej sekcji mają pierwszeństwo przed zmiennymi środowiska systemowego.

W poniższym przykładzie ustawia dwie zmienne środowiskowe. `ASPNETCORE_ENVIRONMENT`konfiguruje środowisko aplikacji `Development`na . Deweloper może tymczasowo ustawić tę wartość w pliku *web.config,* aby wymusić na [stronie wyjątku dewelopera](xref:fundamentals/error-handling) załadować podczas debugowania wyjątku aplikacji. `CONFIG_DIR`jest przykładem zmiennej środowiskowej zdefiniowanej przez użytkownika, gdzie deweloper napisał kod, który odczytuje wartość podczas uruchamiania, tworząc ścieżkę do ładowania pliku konfiguracyjnego aplikacji.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Alternatywą dla ustawienia środowiska bezpośrednio w *web.config* jest uwzględnienie `<EnvironmentName>` właściwości w profilu publikowania [(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) lub pliku projektu. Takie podejście ustawia środowisko w *web.config* po opublikowaniu projektu:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Zmienną `ASPNETCORE_ENVIRONMENT` środowiskową `Development` należy ustawić tylko na serwery przejściowe i testujące, które nie są dostępne dla niezaufanych sieci, takich jak Internet.

## <a name="app_offlinehtm"></a>app_offline.htm

Jeśli plik o nazwie *app_offline.htm* zostanie wykryty w katalogu głównym aplikacji, moduł ASP.NET Core próbuje bezpiecznie zamknąć aplikację i zatrzymać przetwarzanie żądań przychodzących. Jeśli aplikacja jest nadal uruchomiona po liczbie `shutdownTimeLimit`sekund zdefiniowanych w , ASP.NET moduł podstawowy zabija uruchomiony proces.

Podczas gdy plik *app_offline.htm* jest obecny, ASP.NET Core Module odpowiada na żądania, wysyłając z powrotem zawartość pliku *app_offline.htm.* Po usunięciu pliku *app_offline.htm* następne żądanie uruchamia aplikację.

Podczas korzystania z modelu hostingu poza procesem aplikacja może nie zostać natychmiast zamknięta, jeśli istnieje otwarte połączenie. Na przykład połączenie websocket może opóźnić zamknięcie aplikacji.

## <a name="start-up-error-page"></a>Strona błędu uruchamiania

Hosting w trakcie, jak i poza procesem powoduje tworzenie niestandardowych stron błędów, gdy nie można uruchomić aplikacji.

Jeśli ASP.NET moduł podstawowy nie może znaleźć programu obsługi żądań w toku lub poza procesem, zostanie wyświetlona strona kodu stanu awarii ładowania w trakcie lub poza *procesem.*

W przypadku hostingu w trakcie, jeśli ASP.NET Moduł podstawowy nie może uruchomić aplikacji, zostanie wyświetlona strona kodu stanu *500.30 — Start Failure.*

W przypadku hostingu poza procesem, jeśli ASP.NET modułu rdzenia nie uruchamia procesu wewnętrznej bazy danych lub rozpocznie się proces wewnętrznej bazy danych, ale nie nasłuchuje na skonfigurowanym porcie, zostanie wyświetlona strona kodowa stanu *awarii procesu 502.5.*

Aby pominąć tę stronę i powrócić do domyślnej strony kodowej `disableStartUpErrorPage` stanu IIS 5xx, użyj atrybutu. Aby uzyskać więcej informacji na temat konfigurowania niestandardowych komunikatów o [błędach, zobacz Błędy \<HTTP httpErrors>](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Tworzenie i przekierowywanie w dzienniku

Moduł ASP.NET Core przekierowuje wyjście konsoli stdout i stderr na dysk, `stdoutLogEnabled` jeśli ustawiono atrybuty i `stdoutLogFile` atrybuty `aspNetCore` elementu. Wszystkie foldery `stdoutLogFile` w ścieżce są tworzone przez moduł podczas tworzenia pliku dziennika. Pula aplikacji musi mieć dostęp do zapisu do lokalizacji, w której są zapisywane dzienniki (służy `IIS AppPool\<app_pool_name>` do udzielania uprawnień do zapisu).

Dzienniki nie są obracane, chyba że proces recyklingu /ponownego uruchamiania występuje. Jest odpowiedzialny za hosta, aby ograniczyć miejsce na dysku dzienniki zużywają.

Używanie dziennika stdout jest zalecane tylko do rozwiązywania problemów z uruchamianiem aplikacji podczas hostingu w serwisie IIS lub podczas korzystania [z obsługi programów IIS w czasie tworzenia w programie Visual Studio,](xref:host-and-deploy/iis/development-time-iis-support)a nie podczas debugowania lokalnie i uruchamiania aplikacji za pomocą usługi IIS Express.

Nie używaj dziennika stdout do ogólnych celów rejestrowania aplikacji. W przypadku rutynowego rejestrowania w aplikacji ASP.NET Core należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).

Sygnatura czasowa i rozszerzenie pliku są dodawane automatycznie podczas tworzenia pliku dziennika. Nazwa pliku dziennika składa się przez dołączenie sygnatury czasowej, identyfikatora procesu i `stdoutLogFile` rozszerzenia pliku *(.log*) do ostatniego segmentu ścieżki (zazwyczaj *stdout)* rozdzielonego przez podkreślenia. Jeśli `stdoutLogFile` ścieżka kończy się *stdout*, dziennik dla aplikacji z PID 1934 utworzony w dniu 2/5/2018 o 19:42:32 ma nazwę pliku *stdout_20180205194132_1934.log*.

Jeśli `stdoutLogEnabled` jest false, błędy, które występują podczas uruchamiania aplikacji są przechwytywane i emitowane do dziennika zdarzeń do 30 KB. Po uruchomieniu wszystkie dodatkowe dzienniki są odrzucane.

Poniższy `aspNetCore` przykładowy element konfiguruje rejestrowanie `.\log\`stdout w ścieżce względnej . Upewnij się, że tożsamość użytkownika AppPool ma uprawnienia do zapisu w podanej ścieżce.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Podczas publikowania aplikacji dla wdrożenia usługi Azure App Service `stdoutLogFile` zestaw `\\?\%home%\LogFiles\stdout`SDK sieci Web ustawia wartość . Zmienna środowiskowa `%home` jest wstępnie zdefiniowana dla aplikacji obsługiwanych przez usługę Azure App Service.

Aby uzyskać więcej informacji na temat formatów ścieżek, zobacz [Formaty ścieżek plików w systemach Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Ulepszone dzienniki diagnostyczne

Moduł ASP.NET Core można skonfigurować w celu zapewnienia rozszerzonych dzienników diagnostycznych. Dodaj `<handlerSettings>` element do `<aspNetCore>` elementu w *web.config*. Ustawienie, `debugLevel` `TRACE` aby uwidacznia wyższą wierność informacji diagnostycznych:

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

Foldery w ścieżce `<handlerSetting>` dostarczonej do wartości *(dzienniki* w poprzednim przykładzie) nie są tworzone przez moduł automatycznie i powinny wcześniej istnieć we wdrożeniu. Pula aplikacji musi mieć dostęp do zapisu do lokalizacji, w której są zapisywane dzienniki (służy `IIS AppPool\<app_pool_name>` do udzielania uprawnień do zapisu).

Wartości poziomu`debugLevel`debugowania ( ) mogą obejmować zarówno poziom, jak i lokalizację.

Poziomy (w kolejności od najmniej do większości pełnych):

* BŁĄD
* OSTRZEŻENIE
* Informacji
* TRACE

Lokalizacje (dozwolone jest wiele lokalizacji):

* Konsoli
* Eventlog
* PLIK

Ustawienia obsługi mogą być również dostarczane za pośrednictwem zmiennych środowiskowych:

* `ASPNETCORE_MODULE_DEBUG_FILE`&ndash; Ścieżka do pliku dziennika debugowania. (Domyślnie: *aspnetcore-debug.log)*
* `ASPNETCORE_MODULE_DEBUG`&ndash; Ustawienie poziomu debugowania.

> [!WARNING]
> **Nie** należy pozostawiać rejestrowania debugowania włączone we wdrożeniu dłużej niż wymagane do rozwiązania problemu. Rozmiar dziennika nie jest ograniczony. Pozostawienie włączonego dziennika debugowania może wyczerpyć dostępne miejsce na dysku i upaść serwer lub usługę aplikacji.

Zobacz [Konfiguracja z web.config](#configuration-with-webconfig) `aspNetCore` na przykład elementu w pliku *web.config.*

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Konfiguracja serwera proxy używa protokołu HTTP i tokenu parowania

*Dotyczy tylko hostingu poza procesem.*

Serwer proxy utworzony między ASP.NET Core Module a Kestrel używa protokołu HTTP. Nie ma ryzyka podsłuchiwania ruchu między modułem a Pustułką z lokalizacji poza serwerem.

Token parowania jest używany do zagwarantowania, że żądania odebrane przez Kestrel zostały wysłane przez usługi IIS i nie pochodzą z innego źródła. Token parowania jest tworzony i ustawiany`ASPNETCORE_TOKEN`w zmiennej środowiskowej ( ) przez moduł. Token parowania jest również ustawiony`MS-ASPNETCORE-TOKEN`w nagłówku ( ) przy każdym proxied żądania. Oprogramowanie pośredniczące usługi IIS sprawdza każde otrzymane żądanie, aby potwierdzić, że wartość nagłówka tokenu parowania jest zgodna z wartością zmiennej środowiskowej. Jeśli wartości tokenu są niezgodne, żądanie jest rejestrowane i odrzucane. Zmienna środowiska tokenu parowania i ruch między modułem a Kestrel nie są dostępne z lokalizacji poza serwerem. Bez znajomości wartości tokenu parowania osoba atakująca nie może przesłać żądań, które omijają sprawdzanie w oprogramowaniu pośredniczącym usługi IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>ASP.NET modułu rdzenia z konfiguracją współdzieloną usług IIS

Instalator modułu ASP.NET Core module działa z uprawnieniami konta **TrustedInstaller.** Ponieważ lokalne konto systemowe nie ma uprawnień do modyfikowania ścieżki udziału używanej przez konfigurację współużytkową usług IIS, instalator zgłasza błąd odmowy dostępu podczas próby skonfigurowania ustawień modułu w pliku *applicationHost.config* w udziale.

W przypadku korzystania z udostępnionej konfiguracji usług IIS na tym samym komputerze co instalacja `OPT_NO_SHARED_CONFIG_CHECK` usług IIS uruchom instalator ASP.NET Core Hosting Bundle z parametrem ustawionym na: `1`

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Jeśli ścieżka do konfiguracji udostępnionej nie jest na tym samym komputerze co instalacja usług IIS, wykonaj następujące kroki:

1. Wyłącz konfigurację udostępnioną usług IIS.
1. Uruchom instalatora.
1. Wyeksportuj zaktualizowany plik *applicationHost.config* do udziału.
1. Ponownie włącz konfigurację udostępnioną usług IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Dane dotyczące wersji modułu i dzienników instalatora pakietu hostingowego

Aby określić wersję zainstalowanego ASP.NET modułu rdzenia:

1. W systemie hostingowym przejdź do *%windir%\System32\inetsrv*.
1. Znajdź plik *aspnetcore.dll.*
1. Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości** z menu kontekstowego.
1. Wybierz kartę **Szczegóły.** **Wersja pliku** i **wersja produktu** reprezentują zainstalowaną wersję modułu.

Dzienniki instalatora pakietu hostingowego dla modułu znajdują się pod adresem *C:\\\\Users %UserName%\\AppData\\Local\\Temp*. Plik nosi nazwę *dd_DotNetCoreWinSvrHosting__\<sygnatura czasowa>_000_AspNetCoreModule_x64.log*.

## <a name="module-schema-and-configuration-file-locations"></a>Lokalizacje plików modułów, schematów i konfiguracji

### <a name="module"></a>Moduł

**IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

### <a name="schema"></a>Schemat

**IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Konfigurowanie

**IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {APPLICATION\\ROOT} .vs\config\applicationHost.config

* *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Pliki można znaleźć, wyszukując *aspnetcore* w pliku *applicationHost.config.*

::: moniker-end

::: moniker range="< aspnetcore-2.2"

ASP.NET Core Module to natywny moduł usług IIS, który podłącza się do potoku usług IIS w celu przekazywania żądań sieci web do wewnętrznej bazy danych ASP.NET aplikacji Core.

Obsługiwane wersje systemu Windows:

* Windows 7 lub nowsze
* Windows Server 2008 R2 lub nowszy

Moduł działa tylko z Kestrel. Moduł jest niezgodny z [http.sys](xref:fundamentals/servers/httpsys).

Ponieważ ASP.NET aplikacje Core są uruchamiane w procesie odrębnym od procesu roboczego usług IIS, moduł obsługuje również zarządzanie procesami. Moduł uruchamia proces aplikacji ASP.NET Core po nadejściu pierwszego żądania i uruchamia ponownie aplikację w przypadku awarii. Jest to zasadniczo to samo zachowanie, jak w ASP.NET aplikacje 4.x, które działają w procesie w usługach IIS, które są zarządzane przez [usługę aktywacji procesów systemu Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Na poniższym diagramie przedstawiono relację między iis, ASP.NET Core Module i aplikacji:

![Moduł ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

Żądania przychodzą z sieci Web do sterownika HTTP.sys w trybie jądra. Sterownik kieruje żądania do iIS w skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS). Moduł przekazuje żądania do Kestrel na losowym porcie dla aplikacji, który nie jest portem 80 lub 443.

Moduł określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania, a [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguruje serwer do nasłuchiwać na `http://localhost:{port}`. Dodatkowe kontrole są wykonywane, a żądania, które nie pochodzą z modułu są odrzucane. Moduł nie obsługuje przekazywania protokołu HTTPS, więc żądania są przekazywane za pośrednictwem protokołu HTTP, nawet jeśli są odbierane przez usługi IIS za pośrednictwem protokołu HTTPS.

Po Kestrel odbiera żądanie z modułu, żądanie jest wypychane do potoku oprogramowania pośredniczącego ASP.NET Core. Potok oprogramowania pośredniczącego obsługuje żądanie i `HttpContext` przekazuje go jako wystąpienie do logiki aplikacji. Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i bazę ścieżek w celu przekazania żądania do Kestrel. Odpowiedź aplikacji jest przekazywana z powrotem do iIS, co wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.

Wiele modułów natywnych, takich jak uwierzytelnianie systemu Windows, pozostaje aktywnych. Aby dowiedzieć się więcej o modułach usług IIS <xref:host-and-deploy/iis/modules>aktywnych za pomocą ASP.NET Core Module, zobacz .

ASP.NET Moduł podstawowy może również:

* Ustawianie zmiennych środowiskowych dla procesu roboczego.
* Dane wyjściowe stdout do magazynu plików w celu rozwiązywania problemów z uruchamianiem.
* Przesyłanie dalej tokenów uwierzytelniania systemu Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Jak zainstalować i korzystać z modułu ASP.NET Core

Aby uzyskać instrukcje dotyczące instalowania modułu ASP.NET Core, zobacz [Instalowanie pakietu hostingowego .NET Core .](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)

## <a name="configuration-with-webconfig"></a>Konfiguracja z web.config

Moduł ASP.NET Core jest skonfigurowany z `aspNetCore` sekcją `system.webServer` węzła w pliku *web.config* witryny.

Następujący plik *web.config* jest publikowany dla [wdrożenia zależnego od struktury](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) i konfiguruje ASP.NET Core Module do obsługi żądań lokacji:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Następujące *web.config* jest publikowany dla [samodzielnego wdrożenia:](/dotnet/articles/core/deploying/#self-contained-deployments-scd)

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Gdy aplikacja jest wdrażana w [usłudze Azure App Service,](https://azure.microsoft.com/services/app-service/)ścieżka jest ustawiona `stdoutLogFile` na `\\?\%home%\LogFiles\stdout`. Ścieżka zapisuje dzienniki stdout do folderu *LogFiles,* który jest lokalizacją automatycznie utworzoną przez usługę.

Aby uzyskać informacje na temat konfiguracji <xref:host-and-deploy/iis/index#sub-applications>podpołowieczek usług IIS, zobacz .

### <a name="attributes-of-the-aspnetcore-element"></a>Atrybuty elementu aspNetCore

| Atrybut | Opis | Domyślne |
| --------- | ----------- | :-----: |
| `arguments` | <p>Opcjonalny atrybut ciągu.</p><p>Argumenty do pliku wykonywalnego określonego w **processPath**.</p>| |
| `disableStartUpErrorPage` | <p>Opcjonalny atrybut logiczny.</p><p>Jeśli true, **502.5 - Błąd procesu** strona jest pomijana, a strona kod stanu 502 skonfigurowana w *web.config* ma pierwszeństwo.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Opcjonalny atrybut logiczny.</p><p>Jeśli true, token jest przekazywanie dalej do procesu podrzędnego nasłuchiwania na %ASPNETCORE_PORT% jako nagłówek "MS-ASPNETCORE-WINAUTHTOKEN" na żądanie. Jest odpowiedzialny za tego procesu do wywołania CloseHandle na ten token na żądanie.</p> | `true` |
| `processesPerApplication` | <p>Opcjonalny atrybut liczby całkowitej.</p><p>Określa liczbę wystąpień procesu określonego w **ustawieniu processPath,** które można obrócić na aplikację.</p><p>Ustawienie `processesPerApplication` jest odradzane. Ten atrybut zostanie usunięty w przyszłej wersji.</p> | Domyślny:`1`<br>Min:`1`<br>Max:`100` |
| `processPath` | <p>Wymagany atrybut ciągu.</p><p>Ścieżka do pliku wykonywalnego, który uruchamia proces nasłuchiwania żądań HTTP. Ścieżki względne są obsługiwane. Jeśli ścieżka zaczyna `.`się od , ścieżka jest uważana za względem względem katalogu głównego witryny.</p> | |
| `rapidFailsPerMinute` | <p>Opcjonalny atrybut liczby całkowitej.</p><p>Określa, ile razy proces określony w **processPath** może ulec awarii na minutę. Jeśli ten limit zostanie przekroczony, moduł przestaje uruchamiać proces przez pozostałą część minuty.</p> | Domyślny:`10`<br>Min:`0`<br>Max:`100` |
| `requestTimeout` | <p>Opcjonalny atrybut przedziału czasu.</p><p>Określa czas trwania oczekiwania modułu ASP.NET Core module na odpowiedź z procesu nasłuchiwania na %ASPNETCORE_PORT%.</p><p>W wersjach modułu ASP.NET Core, który został dostarczony wraz z wydaniem ASP.NET Core 2.1 lub nowszym, `requestTimeout` jest określony w godzinach, minutach i sekundach.</p> | Domyślny:`00:02:00`<br>Min:`00:00:00`<br>Max:`360:00:00` |
| `shutdownTimeLimit` | <p>Opcjonalny atrybut liczby całkowitej.</p><p>Czas trwania w sekundach, że moduł czeka na plik wykonywalny bezpiecznie zamknąć po wykryciu *pliku app_offline.htm.*</p> | Domyślny:`10`<br>Min:`0`<br>Max:`600` |
| `startupTimeLimit` | <p>Opcjonalny atrybut liczby całkowitej.</p><p>Czas trwania w sekundach, że moduł czeka na plik wykonywalny, aby rozpocząć proces nasłuchiwania na porcie. Jeśli ten limit czasu zostanie przekroczony, moduł zabija proces. Moduł próbuje ponownie uruchomić proces po otrzymaniu nowego żądania i kontynuuje próbę ponownego uruchomienia procesu na kolejne żądania przychodzące, chyba że aplikacja nie może uruchomić **rapidFailsPerMinute** liczba razy w ostatniej toczenia minut.</p><p>Wartość 0 (zero) **nie** jest uważany za nieskończony limit czasu.</p> | Domyślny:`120`<br>Min:`0`<br>Max:`3600` |
| `stdoutLogEnabled` | <p>Opcjonalny atrybut logiczny.</p><p>Jeśli true, **stdout** i **stderr** dla procesu określonego w **processPath** są przekierowywane do pliku określonego w **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Opcjonalny atrybut ciągu.</p><p>Określa względną lub bezwzględną ścieżkę pliku, dla której są rejestrowane **stdout** i **stderr** z procesu określonego w **processPath.** Ścieżki względne są względem katalogu głównego witryny. Każda ścieżka `.` rozpoczynająca się względem katalogu głównego witryny i wszystkie inne ścieżki są traktowane jako ścieżki bezwzględne. Wszystkie foldery podane w ścieżce muszą istnieć, aby moduł utworzył plik dziennika. Za pomocą ograniczników podkreślenia do ostatniego segmentu ścieżki **pliku stdoutLogFile** dodaje się znacznik czasu, identyfikator procesu i rozszerzenie pliku (*.log).* Jeśli `.\logs\stdout` jest dostarczany jako wartość, przykładowy dziennik stdout jest zapisywany jako *stdout_20180205194132_1934.log* w folderze *dzienników* po zapisaniu w 2/5/2018 o godzinie 19:41:32 z identyfikatorem procesu 1934.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Ustawianie zmiennych środowiskowych

Zmienne środowiskowe można określić `processPath` dla procesu w atrybucie. Określ zmienną `<environmentVariable>` środowiskową z `<environmentVariables>` elementem podrzędnym elementu kolekcji.

> [!WARNING]
> Zmienne środowiskowe ustawione w tej sekcji są w konflikcie ze zmiennymi środowiska systemowego ustawionymi o tej samej nazwie. Jeśli zmienna środowiskowa jest ustawiona zarówno w pliku *web.config,* jak i na poziomie systemu w systemie Windows, wartość z `ASPNETCORE_ENVIRONMENT: Development;Development`pliku *web.config* zostanie dołączona do wartości zmiennej środowiska systemowego (na przykład ), która uniemożliwia uruchomienie aplikacji.

W poniższym przykładzie ustawia dwie zmienne środowiskowe. `ASPNETCORE_ENVIRONMENT`konfiguruje środowisko aplikacji `Development`na . Deweloper może tymczasowo ustawić tę wartość w pliku *web.config,* aby wymusić na [stronie wyjątku dewelopera](xref:fundamentals/error-handling) załadować podczas debugowania wyjątku aplikacji. `CONFIG_DIR`jest przykładem zmiennej środowiskowej zdefiniowanej przez użytkownika, gdzie deweloper napisał kod, który odczytuje wartość podczas uruchamiania, tworząc ścieżkę do ładowania pliku konfiguracyjnego aplikacji.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> Zmienną `ASPNETCORE_ENVIRONMENT` środowiskową `Development` należy ustawić tylko na serwery przejściowe i testujące, które nie są dostępne dla niezaufanych sieci, takich jak Internet.

## <a name="app_offlinehtm"></a>app_offline.htm

Jeśli plik o nazwie *app_offline.htm* zostanie wykryty w katalogu głównym aplikacji, moduł ASP.NET Core próbuje bezpiecznie zamknąć aplikację i zatrzymać przetwarzanie żądań przychodzących. Jeśli aplikacja jest nadal uruchomiona po liczbie `shutdownTimeLimit`sekund zdefiniowanych w , ASP.NET moduł podstawowy zabija uruchomiony proces.

Podczas gdy plik *app_offline.htm* jest obecny, ASP.NET Core Module odpowiada na żądania, wysyłając z powrotem zawartość pliku *app_offline.htm.* Po usunięciu pliku *app_offline.htm* następne żądanie uruchamia aplikację.

## <a name="start-up-error-page"></a>Strona błędu uruchamiania

Jeśli ASP.NET modułu rdzenia nie może uruchomić procesu wewnętrznej bazy danych lub rozpocznie się proces wewnętrznej bazy danych, ale nie nasłuchuje na skonfigurowanym porcie, zostanie wyświetlona strona kodowa stanu *awarii procesu 502.5.* Aby pominąć tę stronę i przywrócić domyślną stronę kodową stanu `disableStartUpErrorPage` IIS 502, użyj tego atrybutu. Aby uzyskać więcej informacji na temat konfigurowania niestandardowych komunikatów o [błędach, zobacz Błędy \<HTTP httpErrors>](/iis/configuration/system.webServer/httpErrors/).

![502.5 Strona kodowa stanu awarii procesu](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a>Tworzenie i przekierowywanie w dzienniku

Moduł ASP.NET Core przekierowuje wyjście konsoli stdout i stderr na dysk, `stdoutLogEnabled` jeśli ustawiono atrybuty i `stdoutLogFile` atrybuty `aspNetCore` elementu. Wszystkie foldery `stdoutLogFile` w ścieżce są tworzone przez moduł podczas tworzenia pliku dziennika. Pula aplikacji musi mieć dostęp do zapisu do lokalizacji, w której są zapisywane dzienniki (służy `IIS AppPool\<app_pool_name>` do udzielania uprawnień do zapisu).

Dzienniki nie są obracane, chyba że proces recyklingu /ponownego uruchamiania występuje. Jest odpowiedzialny za hosta, aby ograniczyć miejsce na dysku dzienniki zużywają.

Używanie dziennika stdout jest zalecane tylko do rozwiązywania problemów z uruchamianiem aplikacji podczas hostingu w serwisie IIS lub podczas korzystania [z obsługi programów IIS w czasie tworzenia w programie Visual Studio,](xref:host-and-deploy/iis/development-time-iis-support)a nie podczas debugowania lokalnie i uruchamiania aplikacji za pomocą usługi IIS Express.

Nie używaj dziennika stdout do ogólnych celów rejestrowania aplikacji. W przypadku rutynowego rejestrowania w aplikacji ASP.NET Core należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).

Sygnatura czasowa i rozszerzenie pliku są dodawane automatycznie podczas tworzenia pliku dziennika. Nazwa pliku dziennika składa się przez dołączenie sygnatury czasowej, identyfikatora procesu i `stdoutLogFile` rozszerzenia pliku *(.log*) do ostatniego segmentu ścieżki (zazwyczaj *stdout)* rozdzielonego przez podkreślenia. Jeśli `stdoutLogFile` ścieżka kończy się *stdout*, dziennik dla aplikacji z PID 1934 utworzony w dniu 2/5/2018 o 19:42:32 ma nazwę pliku *stdout_20180205194132_1934.log*.

Poniższy `aspNetCore` przykładowy element konfiguruje rejestrowanie `.\log\`stdout w ścieżce względnej . Upewnij się, że tożsamość użytkownika AppPool ma uprawnienia do zapisu w podanej ścieżce.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

Podczas publikowania aplikacji dla wdrożenia usługi Azure App Service `stdoutLogFile` zestaw `\\?\%home%\LogFiles\stdout`SDK sieci Web ustawia wartość . Zmienna środowiskowa `%home` jest wstępnie zdefiniowana dla aplikacji obsługiwanych przez usługę Azure App Service.

Aby utworzyć reguły filtrowania rejestrowania, zobacz sekcje [filtrowania](xref:fundamentals/logging/index#log-filtering) [konfiguracji](xref:fundamentals/logging/index#log-filtering) i dziennika w dokumentacji rejestrowania ASP.NET Core.

Aby uzyskać więcej informacji na temat formatów ścieżek, zobacz [Formaty ścieżek plików w systemach Windows](/dotnet/standard/io/file-path-formats).

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Konfiguracja serwera proxy używa protokołu HTTP i tokenu parowania

Serwer proxy utworzony między ASP.NET Core Module a Kestrel używa protokołu HTTP. Nie ma ryzyka podsłuchiwania ruchu między modułem a Pustułką z lokalizacji poza serwerem.

Token parowania jest używany do zagwarantowania, że żądania odebrane przez Kestrel zostały wysłane przez usługi IIS i nie pochodzą z innego źródła. Token parowania jest tworzony i ustawiany`ASPNETCORE_TOKEN`w zmiennej środowiskowej ( ) przez moduł. Token parowania jest również ustawiony`MS-ASPNETCORE-TOKEN`w nagłówku ( ) przy każdym proxied żądania. Oprogramowanie pośredniczące usługi IIS sprawdza każde otrzymane żądanie, aby potwierdzić, że wartość nagłówka tokenu parowania jest zgodna z wartością zmiennej środowiskowej. Jeśli wartości tokenu są niezgodne, żądanie jest rejestrowane i odrzucane. Zmienna środowiska tokenu parowania i ruch między modułem a Kestrel nie są dostępne z lokalizacji poza serwerem. Bez znajomości wartości tokenu parowania osoba atakująca nie może przesłać żądań, które omijają sprawdzanie w oprogramowaniu pośredniczącym usługi IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>ASP.NET modułu rdzenia z konfiguracją współdzieloną usług IIS

Instalator modułu ASP.NET Core module działa z uprawnieniami konta **TrustedInstaller.** Ponieważ lokalne konto systemowe nie ma uprawnień do modyfikowania ścieżki udziału używanej przez konfigurację współużytkową usług IIS, instalator zgłasza błąd odmowy dostępu podczas próby skonfigurowania ustawień modułu w pliku *applicationHost.config* w udziale.

Korzystając z konfiguracji udostępnionej usług IIS, wykonaj następujące czynności:

1. Wyłącz konfigurację udostępnioną usług IIS.
1. Uruchom instalatora.
1. Wyeksportuj zaktualizowany plik *applicationHost.config* do udziału.
1. Ponownie włącz konfigurację udostępnioną usług IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Dane dotyczące wersji modułu i dzienników instalatora pakietu hostingowego

Aby określić wersję zainstalowanego ASP.NET modułu rdzenia:

1. W systemie hostingowym przejdź do *%windir%\System32\inetsrv*.
1. Znajdź plik *aspnetcore.dll.*
1. Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Właściwości** z menu kontekstowego.
1. Wybierz kartę **Szczegóły.** **Wersja pliku** i **wersja produktu** reprezentują zainstalowaną wersję modułu.

Dzienniki instalatora pakietu hostingowego dla modułu znajdują się pod adresem *C:\\\\Users %UserName%\\AppData\\Local\\Temp*. Plik nosi nazwę *dd_DotNetCoreWinSvrHosting__\<sygnatura czasowa>_000_AspNetCoreModule_x64.log*.

## <a name="module-schema-and-configuration-file-locations"></a>Lokalizacje plików modułów, schematów i konfiguracji

### <a name="module"></a>Moduł

**IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

### <a name="schema"></a>Schemat

**IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

### <a name="configuration"></a>Konfigurowanie

**IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {APPLICATION\\ROOT} .vs\config\applicationHost.config

* *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Pliki można znaleźć, wyszukując *aspnetcore* w pliku *applicationHost.config.*

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* [ASP.NET źródło referencyjne modułu rdzenia (gałąź główna)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; Użyj listy rozwijanej **Gałąź,** `release/3.1`aby wybrać określoną wersję (na przykład ).
* <xref:host-and-deploy/iis/modules>
