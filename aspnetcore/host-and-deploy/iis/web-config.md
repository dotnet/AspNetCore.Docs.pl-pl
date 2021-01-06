---
title: Plik web.config
author: rick-anderson
description: Odkryj, co znajduje się w pliku web.config i jak skonfigurować różne opcje modułu ASP.NET Core.
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
uid: host-and-deploy/iis/web-config
ms.openlocfilehash: edeef31042547db79fcec98f1236787f78e187a5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057287"
---
# <a name="webconfig-file"></a>`web.config` rozszerzeniem

`web.config`To plik, który jest odczytywany przez usługi IIS i [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) w celu skonfigurowania aplikacji hostowanej za pomocą usług IIS.

## <a name="webconfig-file-location"></a>`web.config` Lokalizacja pliku

Aby prawidłowo skonfigurować [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , `web.config` plik musi być obecny w ścieżce [katalogu głównego zawartości](xref:fundamentals/index#content-root) (zazwyczaj ścieżka podstawowa aplikacji) wdrożonej aplikacji. Ta sama lokalizacja jest taka sama jak ścieżka fizyczna witryny sieci Web dostarczana do usług IIS. `web.config`Plik jest wymagany w katalogu głównym aplikacji, aby umożliwić Publikowanie wielu aplikacji przy użyciu Web Deploy.

Poufne pliki znajdują się w ścieżce fizycznej aplikacji, takiej jak `{ASSEMBLY}.runtimeconfig.json` , `{ASSEMBLY}.xml` (Komentarze dokumentacji XML) i `{ASSEMBLY}.deps.json` , gdzie symbol zastępczy `{ASSEMBLY}` jest nazwą zestawu. Gdy `web.config` plik jest obecny, a lokacja jest uruchamiana normalnie, usługi IIS nie będą obsługiwały tych poufnych plików, jeśli są żądane. Jeśli `web.config` brakuje pliku lub nie można skonfigurować lokacji do normalnego uruchamiania, usługi IIS mogą publicznie obsłużyć poufne pliki.

**`web.config`Plik musi być obecny we wdrożeniu przez cały czas, prawidłowo nazwany i można skonfigurować lokację pod kątem normalnego uruchamiania. Nigdy nie należy usuwać `web.config` pliku z wdrożenia produkcyjnego.**

Jeśli `web.config` plik nie jest obecny w projekcie, plik zostanie utworzony z prawidłowym `processPath` i `arguments` w celu skonfigurowania modułu ASP.NET Core i przeniesiona do [publikowanych danych wyjściowych](xref:host-and-deploy/directory-structure).

Jeśli `web.config` plik jest obecny w projekcie, plik zostanie przekształcony z prawidłowym `processPath` i `arguments` skonfigurowanym modułem ASP.NET Core i przeniesiony do publikowanych danych wyjściowych. Transformacja nie modyfikuje ustawień konfiguracji usług IIS w pliku.

`web.config`Plik może zapewnić dodatkowe ustawienia konfiguracji usług IIS kontrolujące aktywne moduły usług IIS. Aby uzyskać informacje na temat modułów usług IIS, które mogą przetwarzać żądania z aplikacjami ASP.NET Core, zobacz temat [moduły usług IIS](xref:host-and-deploy/iis/modules) .

Tworzenie, przekształcanie i publikowanie `web.config` pliku jest obsługiwane przez obiekt docelowy programu MSBuild (), `_TransformWebConfig` gdy projekt jest publikowany. Ten element docelowy znajduje się w obiektach docelowych zestawu SDK sieci Web ( `Microsoft.NET.Sdk.Web` ). Zestaw SDK jest ustawiany u góry pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Aby uniemożliwić przekształcanie plików przez zestaw SDK sieci Web `web.config` , należy użyć `<IsTransformWebConfigDisabled>` właściwości w pliku projektu:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Podczas wyłączania pliku zestawu SDK sieci Web przy użyciu programu `processPath` i `arguments` powinien zostać ręcznie ustawiony przez dewelopera. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-aspnet-core-module-with-webconfig"></a>Konfiguracja modułu ASP.NET Core za pomocą `web.config`

Moduł ASP.NET Core jest skonfigurowany z `aspNetCore` sekcją `system.webServer` węzła w `web.config` pliku witryny.

Następujący `web.config` plik jest publikowany dla [wdrożenia zależnego od platformy](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) i konfiguruje moduł ASP.NET Core do obsługi żądań lokacji:

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

Następujące elementy `web.config` zostały opublikowane w ramach [wdrożenia samodzielnego](/dotnet/articles/core/deploying/#self-contained-deployments-scd):

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

<xref:System.Configuration.SectionInformation.InheritInChildApplications%2A>Właściwość jest ustawiona na `false` , aby wskazać, że ustawienia określone w [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elemencie nie są dziedziczone przez aplikacje, które znajdują się w podkatalogu aplikacji.

Gdy aplikacja zostanie wdrożona do [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` ścieżka jest ustawiona na `\\?\%home%\LogFiles\stdout` . Ścieżka zapisuje dzienniki stdout do `LogFiles` folderu, który jest lokalizacją automatycznie utworzoną przez usługę.

Aby uzyskać informacje na temat konfiguracji aplikacji podrzędnych usług IIS, zobacz <xref:host-and-deploy/iis/index#sub-applications> .

### <a name="attributes-of-the-aspnetcore-element"></a>Atrybuty `aspNetCore` elementu

| Atrybut | Opis | Domyślne |
| --------- | ----------- | :-----: |
| `arguments` | <p>Opcjonalny atrybut ciągu.</p><p>Argumenty do pliku wykonywalnego określonego w `processPath` .</p> | |
| `disableStartUpErrorPage` | <p>Opcjonalny atrybut Boolean.</p><p>W przypadku wartości true strona **niepowodzenie procesu 502,5** jest pomijana, a strona kodowa stanu 502 jest skonfigurowana w ramach `web.config` pierwszeństwa.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Opcjonalny atrybut Boolean.</p><p>Jeśli wartość jest równa true, token jest przekazywany do procesu podrzędnego, który nasłuchuje w `%ASPNETCORE_PORT%` formie nagłówka "MS-ASPNETCORE-WINAUTHTOKEN" na żądanie. Jest odpowiedzialny za ten proces, aby wywołać metodę CloseHandle na tym tokenie na żądanie.</p> | `true` |
| `hostingModel` | <p>Opcjonalny atrybut ciągu.</p><p>Określa model hostingu jako proces ( `InProcess` / `inprocess` ) lub out-of-Process ( `OutOfProcess` / `outofprocess` ).</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>Opcjonalny atrybut Integer.</p><p>Określa liczbę wystąpień procesu określonego w `processPath` ustawieniu, które może być przypadające na aplikację.</p><p>&dagger;W przypadku hostingu w procesie wartość jest ograniczona do `1` .</p><p>Ustawienie `processesPerApplication` jest niezalecane. Ten atrybut zostanie usunięty w przyszłych wydaniach.</p> | Wartooć `1`<br>Długości `1`<br>Maksymalny `100`&dagger; |
| `processPath` | <p>Wymagany atrybut ciągu.</p><p>Ścieżka do pliku wykonywalnego, który uruchamia proces nasłuchiwanie żądań HTTP. Obsługiwane są ścieżki względne. Jeśli ścieżka zaczyna się od `.` , ścieżka jest uznawana za względną względem katalogu głównego witryny.</p> | |
| `rapidFailsPerMinute` | <p>Opcjonalny atrybut Integer.</p><p>Określa, ile razy proces określony w programie `processPath` może ulec awarii na minutę. W przypadku przekroczenia tego limitu moduł przestaje uruchomić proces przez pozostałą część minuty.</p><p>Nieobsługiwane w przypadku hostingu w procesie.</p> | Wartooć `10`<br>Długości `0`<br>Maksymalny `100` |
| `requestTimeout` | <p>Opcjonalny atrybut TimeSpan.</p><p>Określa czas, przez który moduł ASP.NET Core czeka na odpowiedź z procesu nasłuchiwania na% ASPNETCORE_PORT%.</p><p>W wersjach modułu ASP.NET Core, który został dostarczony z wersją ASP.NET Core 2,1 lub nowszą, wartość `requestTimeout` jest określona w godzinach, minutach i sekundach.</p><p>Nie dotyczy hostingu w procesie. W przypadku hostingu w procesie moduł czeka na aplikację w celu przetworzenia żądania.</p><p>Prawidłowe wartości segmentów minut i sekund ciągu mieszczą się w zakresie 0-59. Użycie `60` wartości w minutach lub sekundach powoduje *błąd 500-wewnętrzny serwera*.</p> | Wartooć `00:02:00`<br>Długości `00:00:00`<br>Maksymalny `360:00:00` |
| `shutdownTimeLimit` | <p>Opcjonalny atrybut Integer.</p><p>Czas w sekundach, przez który moduł czeka na łagodne zamknięcie pliku wykonywalnego, gdy `app_offline.htm` zostanie wykryty.</p> | Wartooć `10`<br>Długości `0`<br>Maksymalny `600` |
| `startupTimeLimit` | <p>Opcjonalny atrybut Integer.</p><p>Czas w sekundach, przez który moduł czeka, aż plik wykonywalny uruchomi proces nasłuchujący na porcie. Jeśli ten limit czasu zostanie przekroczony, moduł zakasuje proces.</p><p>Podczas hostingu *w procesie*: proces **nie** jest ponownie uruchamiany i **nie używa tego** `rapidFailsPerMinute` Ustawienia.</p><p>Podczas hostingu *poza procesem*: moduł próbuje ponownie uruchomić proces, gdy odbierze nowe żądanie, i kontynuuje próbę ponownego uruchomienia procesu na kolejnych żądaniach przychodzących, chyba że aplikacja nie powiedzie się `rapidFailsPerMinute` w ciągu ostatniej minuty.</p><p>Wartość 0 (zero) **nie** jest uważana za nieskończony limit czasu.</p> | Wartooć `120`<br>Długości `0`<br>Maksymalny `3600` |
| `stdoutLogEnabled` | <p>Opcjonalny atrybut Boolean.</p><p>Jeśli wartość jest równa true, `stdout` a `stderr` dla procesu określonego w programie `processPath` są przekierowywane do pliku określonego w `stdoutLogFile` .</p> | `false` |
| `stdoutLogFile` | <p>Opcjonalny atrybut ciągu.</p><p>Określa względną lub bezwzględną ścieżkę do pliku, dla którego `stdout` `stderr` proces określony w `processPath` jest rejestrowany. Ścieżki względne są względne względem katalogu głównego witryny. Każda ścieżka rozpoczynająca `.` się od jest określana względem katalogu głównego witryny, a wszystkie inne ścieżki są traktowane jako ścieżki bezwzględne. Wszystkie foldery podane w ścieżce są tworzone przez moduł po utworzeniu pliku dziennika. Przy użyciu ograniczników podkreślenia, sygnatury czasowej, identyfikatora procesu i rozszerzenia pliku ( `.log` ) są dodawane do ostatniego segmentu `stdoutLogFile` ścieżki. Jeśli `.\logs\stdout` jest podana jako wartość, przykładowy dziennik stdout jest zapisywany jako `stdout_20180205194132_1934.log` w folderze, `logs` gdy zapisywany w dniu 2/5/2018 o godzinie 19:41:32 z identyfikatorem procesu 1934.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Zmienne środowiskowe można określić dla procesu w `processPath` atrybucie. Określ zmienną środowiskową z `<environmentVariable>` elementem podrzędnym `<environmentVariables>` elementu kolekcji. Zmienne środowiskowe ustawione w tej sekcji mają pierwszeństwo przed zmiennymi środowiskowymi systemowymi.

Poniższy przykład ustawia dwie zmienne środowiskowe w `web.config` . `ASPNETCORE_ENVIRONMENT` konfiguruje środowisko aplikacji do programu `Development` . Deweloper może tymczasowo ustawić tę wartość w pliku, `web.config` Aby wymusić ładowanie [strony wyjątku dewelopera](xref:fundamentals/error-handling) podczas debugowania wyjątku aplikacji. `CONFIG_DIR` to przykład zmiennej środowiskowej zdefiniowanej przez użytkownika, w której deweloper ma napisać kod, który odczytuje wartość przy uruchamianiu, aby utworzyć ścieżkę do ładowania pliku konfiguracji aplikacji.

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
> Alternatywą dla ustawienia środowiska bezpośrednio w programie `web.config` jest uwzględnienie `<EnvironmentName>` właściwości w [profilu publikacji ( `.pubxml` )](xref:host-and-deploy/visual-studio-publish-profiles) lub w pliku projektu. To podejście ustawia środowisko w `web.config` momencie opublikowania projektu:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Dla `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej należy ustawić tylko `Development` serwery przejściowe i testowe, które nie są dostępne dla niezaufanych sieci, takich jak Internet.

## <a name="configuration-of-iis-with-webconfig"></a>Konfiguracja usług IIS z `web.config`

Na konfigurację usług IIS wpływa `<system.webServer>` sekcja programu `web.config` dla scenariuszy usług IIS, które są funkcjonalne dla ASP.NET Core aplikacji z modułem ASP.NET Core. Na przykład konfiguracja usług IIS jest funkcjonalna dla kompresji dynamicznej. Jeśli usługi IIS są skonfigurowane na poziomie serwera do korzystania z kompresji dynamicznej, `<urlCompression>` element w `web.config` pliku aplikacji może go wyłączyć dla aplikacji ASP.NET Core.

Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Dokumentacja konfiguracyjna programu `<system.webServer>`](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Aby ustawić zmienne środowiskowe dla poszczególnych aplikacji działających w pulach izolowanych aplikacji (obsługiwane dla usług IIS 10,0 lub nowszych), zobacz sekcję *`AppCmd.exe` Command* w temacie [ `<environmentVariables>` zmienne środowiskowe](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) w dokumentacji dotyczącej programu IIS.

## <a name="configuration-sections-of-webconfig"></a>Sekcje konfiguracji programu `web.config`

Sekcje konfiguracyjne aplikacji ASP.NET 4. x w programie `web.config` nie są używane przez aplikacje ASP.NET Core na potrzeby konfiguracji:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Aplikacje ASP.NET Core są konfigurowane przy użyciu innych dostawców konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index).

## <a name="transform-webconfig"></a>Przekształcanie pliku web.config

Jeśli musisz przekształcić `web.config` przy publikowaniu, zobacz <xref:host-and-deploy/iis/transform-webconfig> . Może być konieczne przekształcenie `web.config` przy publikowaniu w celu ustawienia zmiennych środowiskowych na podstawie konfiguracji, profilu lub środowiska.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [SERWERZE \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/iis/modules>
* <xref:host-and-deploy/iis/transform-webconfig>
