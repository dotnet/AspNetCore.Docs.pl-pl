---
title: Rozwiązywanie problemów ASP.NET Core na Azure App Service i usługach IIS
author: rick-anderson
description: Dowiedz się, jak zdiagnozować problemy z wdrożeniami Azure App Service i Internet Information Services (IIS) ASP.NET Core aplikacji.
monikerRange: '>= aspnetcore-2.1'
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
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 3f6b74ee9621529943d9f685d803837e7df10cfc
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589572"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a>Rozwiązywanie problemów ASP.NET Core na Azure App Service i usługach IIS

Autor [Justin Kotalik](https://github.com/jkotalik)

::: moniker range=">= aspnetcore-3.0"

Ten artykuł zawiera informacje dotyczące typowych błędów uruchamiania aplikacji oraz instrukcje dotyczące sposobu diagnozowania błędów podczas wdrażania aplikacji w usłudze Azure App Service lub IIS:

[Błędy uruchamiania aplikacji](#app-startup-errors)  
Objaśnia typowe scenariusze uruchamiania kodu stanu HTTP.

[Rozwiązywanie problemów dotyczących Azure App Service](#troubleshoot-on-azure-app-service)  
Zawiera porady dotyczące rozwiązywania problemów z aplikacjami wdrożonymi w celu Azure App Service.

[Rozwiązywanie problemów w usługach IIS](#troubleshoot-on-iis)  
Zawiera porady dotyczące rozwiązywania problemów z aplikacjami wdrożonymi w usługach IIS lub lokalnie uruchomionymi IIS Express. Wskazówki dotyczą zarówno wdrożeń systemu Windows Server, jak i pulpitu systemu Windows.

[Wyczyść pamięć podręczną pakietów](#clear-package-caches)  
Wyjaśnia, co należy zrobić, gdy niespójne pakiety przerywają działanie aplikacji podczas przeprowadzania uaktualnień głównych lub zmiany wersji pakietu.

[Dodatkowe zasoby](#additional-resources)  
Wyświetla listę dodatkowych tematów dotyczących rozwiązywania problemów.

## <a name="app-startup-errors"></a>Błędy uruchamiania aplikacji

W programie Visual Studio ASP.NET Core Project domyślnie [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting podczas debugowania. *502,5 — błąd procesu* lub *błąd uruchomienia 500,30* , który występuje, gdy debugowanie lokalne można zdiagnozować przy użyciu porady w tym temacie.

### <a name="40314-forbidden"></a>403,14 zabronione

Nie można uruchomić aplikacji. Rejestrowany jest następujący błąd:

```
The Web server is configured to not list the contents of this directory.
```

Ten błąd jest zwykle spowodowany przez uszkodzone wdrożenie w systemie hostingu, który obejmuje następujące scenariusze:

* Aplikacja jest wdrażana w niewłaściwym folderze w systemie hostingu.
* W procesie wdrażania nie powiodło się przeniesienie wszystkich plików i folderów aplikacji do folderu wdrożenia w systemie hostingu.
* W wdrożeniu brakuje pliku *web.config* lub zawartość pliku *web.config* jest źle sformułowana.

Wykonaj następujące czynności:

1. Usuń wszystkie pliki i foldery z folderu wdrożenia w systemie hostingu.
1. Wdróż ponownie zawartość folderu *publikowania* aplikacji w systemie hostingu przy użyciu zwykłej metody wdrażania, takiej jak Visual Studio, PowerShell lub wdrażanie ręczne:
   * Upewnij się, że plik *web.config* jest obecny we wdrożeniu i że jego zawartość jest poprawna.
   * Podczas hostowania w Azure App Service upewnij się, że aplikacja została wdrożona w `D:\home\site\wwwroot` folderze.
   * Jeśli aplikacja jest hostowana przez usługi IIS, upewnij się, że aplikacja jest wdrożona w **ścieżce fizycznej** usług IIS pokazanej w **ustawieniach podstawowych** w **Menedżerze usług IIS**.
1. Upewnij się, że wszystkie pliki i foldery aplikacji zostały wdrożone, porównując wdrożenie w systemie hostingu z zawartością folderu *publikowania* projektu.

Aby uzyskać więcej informacji na temat układu opublikowanej aplikacji ASP.NET Core, zobacz <xref:host-and-deploy/directory-structure> . Aby uzyskać więcej informacji na temat pliku *web.config* , zobacz <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> .

### <a name="500-internal-server-error"></a>500 Wewnętrzny błąd serwera

Aplikacja zostanie uruchomiona, ale błąd uniemożliwia serwerowi spełnienie żądania.

Ten błąd występuje w kodzie aplikacji podczas uruchamiania lub podczas tworzenia odpowiedzi. Odpowiedź może nie zawierać żadnej zawartości lub odpowiedź może pojawić się w przeglądarce jako *błąd wewnętrzny serwera 500* . Dziennik zdarzeń aplikacji zwykle stwierdza, że aplikacja została uruchomiona normalnie. Z perspektywy serwera jest to poprawne. Aplikacja została uruchomiona, ale nie może wygenerować prawidłowej odpowiedzi. Uruchom aplikację w wierszu polecenia na serwerze lub Włącz dziennik stdout modułu ASP.NET Core, aby rozwiązać problem.

### <a name="5000-in-process-handler-load-failure"></a>500,0 błąd ładowania procedury obsługi In-Process

Proces roboczy kończy się niepowodzeniem. Aplikacja nie zostanie uruchomiona.

Wystąpił nieznany błąd podczas ładowania składników [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) . Wykonaj jedno z następujących działań:

* [Pomoc techniczna firmy Microsoft](https://support.microsoft.com/oas/default.aspx?prid=15832) kontaktu (wybierz **Narzędzia deweloperskie** następnie **ASP.NET Core**).
* Zadawaj pytanie na Stack Overflow.
* Zajrzyj do problemu w naszym [repozytorium GitHub](https://github.com/dotnet/AspNetCore).

### <a name="50030-in-process-startup-failure"></a>500,30 In-Process błąd uruchamiania

Proces roboczy kończy się niepowodzeniem. Aplikacja nie zostanie uruchomiona.

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) próbuje uruchomić program .NET Core CLR w procesie, ale nie można go uruchomić. Przyczyna niepowodzenia uruchomienia procesu zwykle można ustalić na podstawie wpisów w dzienniku zdarzeń aplikacji i dzienniku modułu ASP.NET Core stdout.

Typowe warunki awarii:

* Aplikacja jest nieprawidłowo skonfigurowana z powodu docelowej wersji ASP.NET Core udostępnionej platformy, która nie istnieje. Sprawdź, które wersje ASP.NET Core udostępnionej platformy są zainstalowane na maszynie docelowej.
* Za pomocą Azure Key Vault, brak uprawnień do Key Vault. Sprawdź zasady dostępu w Key Vault celem, aby upewnić się, że udzielono odpowiednich uprawnień.

### <a name="50031-ancm-failed-to-find-native-dependencies"></a>500,31 ANCM nie może odnaleźć natywnych zależności

Proces roboczy kończy się niepowodzeniem. Aplikacja nie zostanie uruchomiona.

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) próbuje uruchomić środowisko uruchomieniowe programu .NET Core w procesie, ale nie może go uruchomić. Najczęstszym powodem tego błędu uruchomienia jest to, że `Microsoft.NETCore.App` `Microsoft.AspNetCore.App` środowisko uruchomieniowe lub nie jest zainstalowane. Jeśli aplikacja jest wdrożona w programie docelowym ASP.NET Core 3,0 i ta wersja nie istnieje na maszynie, wystąpi błąd. Poniżej znajduje się przykładowy komunikat o błędzie:

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

W komunikacie o błędzie są wyświetlane wszystkie zainstalowane wersje programu .NET Core i wersja żądana przez aplikację. Aby naprawić ten błąd, należy:

* Zainstaluj na komputerze odpowiednią wersję programu .NET Core.
* Zmień aplikację na wersję docelową platformy .NET Core, która jest obecna na maszynie.
* Publikowanie aplikacji jako [samodzielnego wdrożenia](/dotnet/core/deploying/#self-contained-deployments-scd).

Podczas pracy w środowisku deweloperskim ( `ASPNETCORE_ENVIRONMENT` zmienna środowiskowa jest ustawiona na `Development` ) określony błąd jest zapisywana w odpowiedzi HTTP. Przyczyna niepowodzenia uruchomienia procesu znajduje się również w dzienniku zdarzeń aplikacji.

### <a name="50032-ancm-failed-to-load-dll"></a>500,32 ANCM nie może załadować biblioteki DLL

Proces roboczy kończy się niepowodzeniem. Aplikacja nie zostanie uruchomiona.

Najbardziej typową przyczyną tego błędu jest to, że aplikacja jest publikowana dla niezgodnej architektury procesora. Jeśli proces roboczy jest uruchomiony jako aplikacja 32-bitowa, a aplikacja została opublikowana w docelowym 64-bitowym, ten błąd wystąpi.

Aby naprawić ten błąd, należy:

* Opublikuj ponownie aplikację dla tej samej architektury procesora co proces roboczy.
* Opublikuj aplikację jako [wdrożenie zależne od platformy](/dotnet/core/deploying/#framework-dependent-executables-fde).

### <a name="50033-ancm-request-handler-load-failure"></a>500,33 błąd ładowania obsługi żądania ANCM

Proces roboczy kończy się niepowodzeniem. Aplikacja nie zostanie uruchomiona.

Aplikacja nie odwołuje się do `Microsoft.AspNetCore.App` struktury. `Microsoft.AspNetCore.App` [Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module)może obsługiwać tylko aplikacje ukierunkowane na platformę.

Aby naprawić ten błąd, upewnij się, że aplikacja jest ukierunkowana na `Microsoft.AspNetCore.App` platformę. Sprawdź, `.runtimeconfig.json` czy w celu zweryfikowania struktury wskazywanej przez aplikację.

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a>500,34 ANCM mieszane modele hostingu nie są obsługiwane

Proces roboczy nie może uruchomić zarówno aplikacji w procesie, jak i aplikacji pozaprocesowej w tym samym procesie.

Aby naprawić ten błąd, uruchom aplikacje w osobnych pulach aplikacji usług IIS.

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a>500,35 ANCM wiele aplikacji In-Process w tym samym procesie

Proces roboczy nie może uruchamiać wielu aplikacji w procesie w tym samym procesie.

Aby naprawić ten błąd, uruchom aplikacje w osobnych pulach aplikacji usług IIS.

### <a name="50036-ancm-out-of-process-handler-load-failure"></a>500,36 ANCM błąd ładowania procedury obsługi poza procesem

Procedura obsługi żądań poza procesem, *aspnetcorev2_outofprocess.dll*, nie jest obok pliku *aspnetcorev2.dll* . Oznacza to uszkodzenie instalacji [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

Aby naprawić ten błąd, napraw instalację [pakietu hostingu platformy .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (dla usług IIS) lub programu Visual Studio (w przypadku IIS Express).

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a>Nie można uruchomić 500,37 ANCM w ramach limitu czasu uruchamiania

Nie można uruchomić ANCM w określonym limicie czasu uruchamiania. Domyślnie limit czasu wynosi 120 sekund.

Ten błąd może wystąpić podczas uruchamiania dużej liczby aplikacji na tym samym komputerze. Podczas uruchamiania Sprawdź, czy na serwerze są naskoki użycie procesora CPU/pamięci. Może być konieczne rozłożenie procesu uruchamiania wielu aplikacji.

### <a name="50038-ancm-application-dll-not-found"></a>500,38 nie znaleziono biblioteki DLL aplikacji ANCM

ANCM nie może odnaleźć biblioteki DLL aplikacji, która powinna znajdować się obok pliku wykonywalnego.

Ten błąd występuje podczas hostowania aplikacji jako [pliku wykonywalnego jednoplikowego](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) przy użyciu modelu hostingu w procesie. Model w procesie wymaga, aby ANCM ładował aplikację .NET Core do istniejącego procesu usług IIS. Ten scenariusz nie jest obsługiwany przez model wdrażania pojedynczego pliku. Aby naprawić ten błąd, użyj **jednego** z następujących metod w pliku projektu aplikacji:

1. Wyłącz publikowanie pojedynczych plików przez ustawienie `PublishSingleFile` właściwości MSBuild na `false` .
1. Przejdź do modelu hostingu poza procesem, ustawiając `AspNetCoreHostingModel` Właściwość programu MSBuild na wartość `OutOfProcess` .

### <a name="5025-process-failure"></a>502.5 Błąd procesu

Proces roboczy kończy się niepowodzeniem. Aplikacja nie zostanie uruchomiona.

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) próbuje uruchomić proces roboczy, ale jego uruchomienie nie powiedzie się. Przyczyna niepowodzenia uruchomienia procesu zwykle można ustalić na podstawie wpisów w dzienniku zdarzeń aplikacji i dzienniku modułu ASP.NET Core stdout.

Typowym warunkiem niepowodzenia jest to, że aplikacja jest nieprawidłowo skonfigurowana z powodu docelowej wersji ASP.NET Core udostępnionej platformy, która nie istnieje. Sprawdź, które wersje ASP.NET Core udostępnionej platformy są zainstalowane na maszynie docelowej. *Platforma udostępniona* jest zestawem zestawów (plików *dll* ), które są zainstalowane na maszynie i do których odwołuje się pakiet `Microsoft.AspNetCore.App` . Odwołanie do pakietu nie może określać minimalnej wymaganej wersji. Aby uzyskać więcej informacji, zobacz [udostępnioną strukturę](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Strona błędu *niepowodzenia procesu 502,5* jest zwracana w przypadku niepowodzenia konfiguracji hostingu lub aplikacji. powoduje to niepowodzenie procesu roboczego:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Nie można uruchomić aplikacji (ErrorCode "0x800700C1")

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Nie można uruchomić aplikacji, ponieważ nie można załadować zestawu aplikacji (*dll*).

Ten błąd występuje, gdy wystąpiła niezgodność między opublikowaną aplikacją a procesem w3wp/iisexpress.

Upewnij się, że ustawienie 32-bitowe puli aplikacji jest poprawne:

1. Wybierz pulę aplikacji w **pulach aplikacji** Menedżera usług IIS.
1. Wybierz pozycję **Ustawienia zaawansowane** w obszarze **Edytuj pulę aplikacji** w panelu **Akcje** .
1. Ustaw **opcję Włącz aplikacje 32-bitowe**:
   * W przypadku wdrażania aplikacji 32-bitowej (x86) ustaw wartość na `True` .
   * W przypadku wdrażania aplikacji 64-bitowej (x64) ustaw wartość na `False` .

Upewnij się, że nie występuje konflikt między `<Platform>` właściwością programu MSBuild w pliku projektu a opublikowaną bitową w aplikacji.

### <a name="connection-reset"></a>Resetowanie połączenia

Jeśli wystąpi błąd po wysłaniu nagłówków, jest zbyt późno, aby serwer mógł wysłać **błąd wewnętrzny serwera 500** w przypadku wystąpienia błędu. Zdarza się to często w przypadku wystąpienia błędu podczas serializacji obiektów złożonych dla odpowiedzi. Ten typ błędu pojawia się jako błąd *resetowania połączenia* na kliencie. [Rejestrowanie aplikacji](xref:fundamentals/logging/index) może pomóc w rozwiązywaniu tego typu błędów.

### <a name="default-startup-limits"></a>Domyślne limity uruchamiania

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) jest skonfigurowany z domyślną *startupTimeLimitą* 120 sekund. Po lewej stronie wartości domyślnej, uruchomienie aplikacji może trwać do dwóch minut przed zarejestrowaniem awarii procesu przez moduł. Aby uzyskać informacje na temat konfigurowania modułu, zobacz [atrybuty elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Rozwiązywanie problemów dotyczących Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Dziennik zdarzeń aplikacji (Azure App Service)

Aby uzyskać dostęp do dziennika zdarzeń aplikacji, użyj bloku **diagnozowanie i rozwiązywanie problemów** w Azure Portal:

1. W Azure Portal Otwórz aplikację w **App Services**.
1. Kliknij pozycję **Diagnozowanie i rozwiązywanie problemów**.
1. Wybierz nagłówek **Narzędzia diagnostyczne** .
1. W obszarze **Narzędzia obsługi** wybierz przycisk **zdarzenia aplikacji** .
1. Zapoznaj się z najnowszym błędem podanym w pozycji *AspNetCoreModule IIS* lub *IIS AspNetCoreModule v2* w kolumnie **Źródło** .

Alternatywą dla korzystania z bloku **diagnozowanie i rozwiązywanie problemów** jest przetestowanie pliku dziennika zdarzeń aplikacji bezpośrednio przy użyciu [kudu](https://github.com/projectkudu/kudu/wiki):

1. Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** . Wybierz przycisk **Przejdź &rarr;** . Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.
1. Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.
1. Otwórz folder **LogFiles** .
1. Wybierz ikonę ołówka obok pliku *eventlog.xml* .
1. Przejrzyj dziennik. Przewiń w dół dziennika, aby zobaczyć najnowsze zdarzenia.

### <a name="run-the-app-in-the-kudu-console"></a>Uruchamianie aplikacji w konsoli kudu

Wiele błędów uruchamiania nie tworzy użytecznych informacji w dzienniku zdarzeń aplikacji. Możesz uruchomić aplikację w konsoli zdalnego wykonywania [kudu](https://github.com/projectkudu/kudu/wiki) , aby wykryć błąd:

1. Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** . Wybierz przycisk **Przejdź &rarr;** . Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.
1. Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.

#### <a name="test-a-32-bit-x86-app"></a>Testowanie aplikacji 32-bitowej (x86)

**Bieżąca wersja**

1. `cd d:\home\site\wwwroot`
1. Uruchom aplikację:
   * Jeśli aplikacja jest [wdrożeniem zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:

     ```console
     {ASSEMBLY NAME}.exe
     ```

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

**Wdrożenie zależne od platformy uruchomione w wersji zapoznawczej**

*Wymaga zainstalowania rozszerzenia witryny środowiska uruchomieniowego ASP.NET Core {VERSION} (x86).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` ( `{X.Y}` to wersja środowiska uruchomieniowego)
1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Testowanie aplikacji 64-bitowej (x64)

**Bieżąca wersja**

* Jeśli aplikacja jest wdrożeniem 64-bitowym (x64), [zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):
  1. `cd D:\Program Files\dotnet`
  1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:
  1. `cd D:\home\site\wwwroot`
  1. Uruchom aplikację: `{ASSEMBLY NAME}.exe`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

**Wdrożenie zależne od platformy uruchomione w wersji zapoznawczej**

*Wymaga zainstalowania rozszerzenia witryny środowiska uruchomieniowego ASP.NET Core {VERSION} (x64).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` ( `{X.Y}` to wersja środowiska uruchomieniowego)
1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>Dziennik stdout modułu ASP.NET Core (Azure App Service)

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika. Rejestrowania stdout można używać tylko w celu rozwiązywania problemów z uruchamianiem aplikacji.
>
> Aby uzyskać ogólne rejestrowanie w aplikacji ASP.NET Core po uruchomieniu, należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.

Dziennik modułu ASP.NET Core stdout często rejestruje przydatne komunikaty o błędach, które nie są dostępne w dzienniku zdarzeń aplikacji. Aby włączyć i wyświetlić dzienniki stdout:

1. W witrynie Azure Portal przejdź do aplikacji sieci Web.
1. W bloku **App Service** wprowadź **kudu** w polu wyszukiwania.
1. Wybierz pozycję **Narzędzia zaawansowane** > **Przejdź**.
1. Wybierz  **konsolę debugowania > cmd**.
1. Przejdź do *witryny/wwwroot*
1. Wybierz ikonę ołówka, aby edytować plik *web.config* .
1. W `<aspNetCore />` elemencie Ustaw `stdoutLogEnabled="true"` i wybierz pozycję **Zapisz**.

Wyłącz rejestrowanie stdout, gdy Rozwiązywanie problemów zostanie zakończone przez ustawienie `stdoutLogEnabled="false"` .

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

<a name="enhanced-diagnostic-logs"></a>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>Dziennik debugowania modułu ASP.NET Core (Azure App Service)

Dziennik debugowania modułu ASP.NET Core zapewnia dodatkowe, dokładniejsze rejestrowanie z modułu ASP.NET Core. Aby włączyć i wyświetlić dzienniki stdout:

1. Aby włączyć Rozszerzony Dziennik diagnostyczny, wykonaj jedną z następujących czynności:
   * Postępuj zgodnie z instrukcjami w temacie [udoskonalone dzienniki diagnostyczne](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) , aby skonfigurować aplikację do rozszerzonego rejestrowania diagnostycznego. Wdróż ponownie aplikację.
   * Dodaj `<handlerSettings>` pokazany w [ulepszonych dziennikach diagnostycznych](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do pliku *web.config* aplikacji na żywo za pomocą konsoli kudu:
     1. Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** . Wybierz przycisk **Przejdź &rarr;** . Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.
     1. Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.
     1. Otwórz foldery w **witrynie** Path  >  **wwwroot**. Edytuj plik *web.config* , wybierając przycisk ołówka. Dodaj `<handlerSettings>` sekcję, jak pokazano w [udoskonalonych dziennikach diagnostycznych](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Wybierz ikonę **Zapisz**.
1. Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** . Wybierz przycisk **Przejdź &rarr;** . Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.
1. Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.
1. Otwórz foldery w **witrynie** Path  >  **wwwroot**. Jeśli nie podano ścieżki do pliku *aspnetcore-Debug. log* , plik zostanie wyświetlony na liście. Jeśli podano ścieżkę, przejdź do lokalizacji pliku dziennika.
1. Otwórz plik dziennika z przyciskiem ołówek obok nazwy pliku.

Wyłącz rejestrowanie debugowania po zakończeniu rozwiązywania problemów:

Aby wyłączyć rozszerzony Dziennik debugowania, wykonaj jedną z następujących czynności:

* Usuń `<handlerSettings>` plik z pliku *web.config* lokalnie i Wdróż ponownie aplikację.
* Za pomocą konsoli kudu Edytuj plik *web.config* i Usuń `<handlerSettings>` sekcję. Zapisz plik.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> Niepowodzenie wyłączenia dziennika debugowania może prowadzić do awarii aplikacji lub serwera. Nie ma limitu rozmiaru pliku dziennika. Funkcja rejestrowania debugowania służy tylko do rozwiązywania problemów z uruchamianiem aplikacji.
>
> Aby uzyskać ogólne rejestrowanie w aplikacji ASP.NET Core po uruchomieniu, należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.

### <a name="slow-or-hanging-app-azure-app-service"></a>Aplikacja wolna lub wysunięta (Azure App Service)

Gdy aplikacja reaguje powoli lub zawiesza się na żądanie, zobacz następujące artykuły:

* [Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Użyj rozszerzenia witryny diagnostyki awarii, aby przechwycić zrzut dla sporadycznych problemów z wyjątkami lub problemów z wydajnością w usłudze Azure Web App](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>Bloki monitorowania

Bloki monitorowania zapewniają alternatywne środowisko rozwiązywania problemów z metodami opisanymi wcześniej w temacie. Te bloki mogą służyć do diagnozowania błędów serii 500.

Upewnij się, że rozszerzenia ASP.NET Core są zainstalowane. Jeśli rozszerzenia nie są zainstalowane, zainstaluj je ręcznie:

1. W sekcji blok **narzędzi programistycznych** wybierz blok **rozszerzenia** .
1. **Rozszerzenia ASP.NET Core** powinny znajdować się na liście.
1. Jeśli rozszerzenia nie są zainstalowane, wybierz przycisk **Dodaj** .
1. Wybierz z listy **rozszerzenia ASP.NET Core** .
1. Wybierz **przycisk OK** , aby zaakceptować postanowienia prawne.
1. W bloku **Dodaj rozszerzenie** wybierz pozycję **OK** .
1. Komunikat podręczny informujący o pomyślnym zainstalowaniu rozszerzeń.

Jeśli rejestrowanie stdout nie jest włączone, wykonaj następujące kroki:

1. W Azure Portal wybierz blok **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** . Wybierz przycisk **Przejdź &rarr;** . Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.
1. Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.
1. Otwórz foldery w **witrynie** Path > **wwwroot** i przewiń w dół, aby odsłonić *web.config* plik w dolnej części listy.
1. Kliknij ikonę ołówka obok pliku *web.config* .
1. Ustaw wartość **stdoutLogEnabled** na `true` i zmień ścieżkę **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .
1. Wybierz pozycję **Zapisz** , aby zapisać zaktualizowany plik *web.config* .

Wykonaj aktywację rejestrowania diagnostycznego:

1. W Azure Portal wybierz blok **dzienników diagnostycznych** .
1. Wybierz pozycję **Włącz** , aby włączyć **Rejestrowanie aplikacji (system plików)** i **szczegółowe komunikaty o błędach**. Wybierz przycisk **Zapisz** znajdujący się u góry bloku.
1. Aby uwzględnić śledzenie nieudanych żądań, znane także jako rejestrowanie nieudanych żądań buforowania zdarzeń (FREB), wybierz **przełącznik dla** **śledzenia nieudanych żądań**.
1. Wybierz blok **strumień dziennika** , który jest wyświetlany bezpośrednio w bloku **dzienników diagnostycznych** w portalu.
1. Utwórz żądanie do aplikacji.
1. W danych strumienia dziennika jest wskazywana Przyczyna błędu.

Należy pamiętać o wyłączeniu rejestrowania stdout po zakończeniu rozwiązywania problemów.

Aby wyświetlić dzienniki śledzenia niepomyślnych żądań (dzienniki FREB):

1. Przejdź do bloku **diagnozowanie i rozwiązywanie problemów** w Azure Portal.
1. Wybierz pozycję **dzienniki śledzenia niepomyślnych żądań** z obszaru **Narzędzia obsługi** na pasku bocznym.

Zobacz [sekcję śledzenie niepomyślnych żądań w temacie Włączanie rejestrowania diagnostyki dla aplikacji sieci Web w programie Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) i informacje [o wydajności aplikacji dotyczące Web Apps na platformie Azure: Jak mogę włączyć śledzenie nieudanych żądań?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Aby uzyskać więcej informacji.

Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania diagnostycznego dla aplikacji sieci Web w Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika.
>
> Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.

## <a name="troubleshoot-on-iis"></a>Rozwiązywanie problemów w usługach IIS

### <a name="application-event-log-iis"></a>Dziennik zdarzeń aplikacji (IIS)

Dostęp do dziennika zdarzeń aplikacji:

1. Otwórz menu Start, wyszukaj ciąg *Podgląd zdarzeń* i wybierz aplikację **Podgląd zdarzeń** .
1. W **Podgląd zdarzeń** Otwórz węzeł **Dzienniki systemu Windows** .
1. Wybierz pozycję **aplikacja** , aby otworzyć dziennik zdarzeń aplikacji.
1. Wyszukaj błędy związane z niepowodzeniem aplikacji. Błędy mają wartość *modułu AspNetCore IIS* lub *moduł IIS Express AspNetCore* w kolumnie *Źródło* .

### <a name="run-the-app-at-a-command-prompt"></a>Uruchamianie aplikacji w wierszu polecenia

Wiele błędów uruchamiania nie tworzy użytecznych informacji w dzienniku zdarzeń aplikacji. Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingu.

#### <a name="framework-dependent-deployment"></a>Wdrożenie zależne od platformy

Jeśli aplikacja jest [wdrożeniem zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

1. W wierszu polecenia przejdź do folderu wdrożenia i uruchom aplikację, wykonując zestaw aplikacji za pomocą *dotnet.exe*. W poniższym poleceniu Zastąp nazwę zestawu aplikacji dla \<assembly_name> : `dotnet .\<assembly_name>.dll` .
1. Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.
1. Jeśli wystąpią błędy podczas wysyłania żądania do aplikacji, należy wysłać żądanie do hosta i portu, gdzie Kestrel nasłuchuje. Przy użyciu domyślnego hosta i wpisu Wyślij żądanie do `http://localhost:5000/` . Jeśli aplikacja będzie odpowiadać zwykle na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobnie związany z konfiguracją hostingu i mniej korzystnie w aplikacji.

#### <a name="self-contained-deployment"></a>Wdrażanie samodzielne

Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:

1. W wierszu polecenia przejdź do folderu wdrożenia i uruchom plik wykonywalny aplikacji. W poniższym poleceniu Zastąp nazwę zestawu aplikacji dla \<assembly_name> : `<assembly_name>.exe` .
1. Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.
1. Jeśli wystąpią błędy podczas wysyłania żądania do aplikacji, należy wysłać żądanie do hosta i portu, gdzie Kestrel nasłuchuje. Przy użyciu domyślnego hosta i wpisu Wyślij żądanie do `http://localhost:5000/` . Jeśli aplikacja będzie odpowiadać zwykle na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobnie związany z konfiguracją hostingu i mniej korzystnie w aplikacji.

### <a name="aspnet-core-module-stdout-log-iis"></a>Dziennik stdout modułu ASP.NET Core (IIS)

Aby włączyć i wyświetlić dzienniki stdout:

1. Przejdź do folderu wdrożenia lokacji w systemie hostingu.
1. Jeśli folder *Logs* nie istnieje, utwórz go. Aby uzyskać instrukcje dotyczące włączania programu MSBuild do automatycznego tworzenia folderu *Logs* w ramach wdrożenia, zobacz temat [Struktura katalogów](xref:host-and-deploy/directory-structure) .
1. Edytuj plik *web.config* . Ustaw wartość **stdoutLogEnabled** na `true` i zmień ścieżkę **stdoutLogFile** , tak aby wskazywała folder *Logs* (na przykład `.\logs\stdout` ). `stdout` ścieżka jest prefiksem nazwy pliku dziennika. Sygnatura czasowa, identyfikator procesu i rozszerzenie pliku są dodawane automatycznie podczas tworzenia dziennika. Używając `stdout` jako prefiksu nazwy pliku, typowy plik dziennika ma nazwę *stdout_20180205184032_5412. log*.
1. Upewnij się, że tożsamość puli aplikacji ma uprawnienia do zapisu w folderze *Logs* .
1. Zapisz zaktualizowany plik *web.config* .
1. Utwórz żądanie do aplikacji.
1. Przejdź do folderu *Logs* . Znajdź i Otwórz najnowszy dziennik stdout.
1. Zbadaj dziennik pod kątem błędów.

Wyłącz rejestrowanie stdout po zakończeniu rozwiązywania problemów:

1. Edytuj plik *web.config* .
1. Ustaw wartość **stdoutLogEnabled** na `false` .
1. Zapisz plik.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika.
>
> Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.

### <a name="aspnet-core-module-debug-log-iis"></a>Dziennik debugowania modułu ASP.NET Core (IIS)

Dodaj następujące ustawienia programu obsługi do pliku *web.config* aplikacji, aby włączyć Dziennik debugowania modułu ASP.NET Core:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Upewnij się, że ścieżka określona dla dziennika istnieje i że tożsamość puli aplikacji ma uprawnienia do zapisu w tej lokalizacji.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

### <a name="enable-the-developer-exception-page"></a>Włącz stronę wyjątków dla deweloperów

`ASPNETCORE_ENVIRONMENT` [Zmienną środowiskową można dodać do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , aby uruchomić aplikację w środowisku deweloperskim. O ile środowisko nie zostanie zastąpione podczas uruchamiania aplikacji przez `UseEnvironment` konstruktora hosta, ustawienie zmiennej środowiskowej umożliwia wyświetlenie [strony wyjątku dewelopera](xref:fundamentals/error-handling) po uruchomieniu aplikacji.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

Ustawienie zmiennej środowiskowej dla programu `ASPNETCORE_ENVIRONMENT` jest zalecane wyłącznie do użytku na przejściowych i testowych serwerach, które nie są dostępne w Internecie. Usuń zmienną środowiskową z pliku *web.config* po rozwiązaniu problemu. Aby uzyskać informacje na temat ustawiania zmiennych środowiskowych w *web.config*, zobacz [EnvironmentVariables podrzędny element aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Uzyskiwanie danych z aplikacji

Jeśli aplikacja może odpowiadać na żądania, uzyskiwać żądania, połączenia i dodatkowe dane z aplikacji przy użyciu wbudowanego oprogramowania terminala. Aby uzyskać więcej informacji i przykładowy kod, zobacz <xref:test/troubleshoot#obtain-data-from-an-app> .

### <a name="slow-or-hanging-app-iis"></a>Aplikacja wolna lub wysunięta (IIS)

*Zrzut awaryjny* to migawka pamięci systemu, która może pomóc w ustaleniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.

#### <a name="app-crashes-or-encounters-an-exception"></a>Awaria aplikacji lub napotka wyjątek

Uzyskaj i Analizuj Zrzut z [raportowanie błędów systemu Windows (raportowanie błędów systemu Windows)](/windows/desktop/wer/windows-error-reporting):

1. Utwórz folder do przechowywania plików zrzutu awaryjnego w `c:\dumps` . Pula aplikacji musi mieć dostęp do zapisu w folderze.
1. Uruchom [skrypt programu PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)w programie EnableDumps:
   * Jeśli aplikacja korzysta z [modelu hostingu w procesie](xref:host-and-deploy/iis/index#in-process-hosting-model), uruchom skrypt dla *w3wp.exe*:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Jeśli aplikacja korzysta z [modelu hostingu poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), uruchom skrypt dla *dotnet.exe*:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Uruchom aplikację w warunkach, które powodują awarię.
1. Po wystąpieniu awarii Uruchom [skrypt programu DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):
   * Jeśli aplikacja korzysta z [modelu hostingu w procesie](xref:host-and-deploy/iis/index#in-process-hosting-model), uruchom skrypt dla *w3wp.exe*:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Jeśli aplikacja korzysta z [modelu hostingu poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), uruchom skrypt dla *dotnet.exe*:

     ```console
     .\DisableDumps dotnet.exe
     ```

Po awarii aplikacji i zakończeniu zbierania zrzutów aplikacja może zakończyć normalne działanie. Skrypt programu PowerShell konfiguruje raportowanie błędów systemu Windows w celu zebrania do pięciu zrzutów na aplikację.

> [!WARNING]
> Zrzuty awaryjne mogą wymagać dużej ilości miejsca na dysku (do kilku gigabajtów).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie

Gdy aplikacja *zawiesza* się (bez awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [pliki zrzutu w trybie użytkownika: wybór najlepszego narzędzia](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) do wygenerowania zrzutu.

#### <a name="analyze-the-dump"></a>Analizowanie zrzutu

Zrzut można analizować przy użyciu kilku metod. Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu User-Mode](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="clear-package-caches"></a>Wyczyść pamięć podręczną pakietów

Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji. W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych. Większość tych problemów można naprawić, wykonując następujące instrukcje:

1. Usuń foldery *bin* i *obj* .
1. Wyczyść pamięć podręczną pakietów, wykonując [wszystkie elementy lokalne usługi NuGet programu dotnet--Wyczyść](/dotnet/core/tools/dotnet-nuget-locals) z poziomu powłoki poleceń.

   Czyszczenie pamięci podręcznych pakietów można także wykonać przy użyciu narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonując polecenie `nuget locals all -clear` . *nuget.exe* nie jest instalacją w pakiecie z systemem operacyjnym Windows dla komputerów stacjonarnych i należy ją pobrać niezależnie od [witryny sieci Web programu NuGet](https://www.nuget.org/downloads).

1. Przywróć i skompiluj projekt.
1. Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Dokumentacja platformy Azure

* [Usługa Application Insights dla platformy ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Sekcja zdalne debugowanie aplikacji sieci Web Rozwiązywanie problemów z aplikacją sieci Web w Azure App Service przy użyciu programu Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Omówienie diagnostyki Azure App Service](/azure/app-service/app-service-diagnostics)
* [Jak monitorować aplikacje w usłudze Azure App Service](/azure/app-service/web-sites-monitor)
* [Rozwiązywanie problemów z aplikacją sieci Web w Azure App Service przy użyciu programu Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Rozwiązywanie problemów z błędami HTTP "502 złej Gateway" i "503 Usługa niedostępna" w usłudze Azure Web Apps](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Często zadawane pytania dotyczące wydajności aplikacji dla Web Apps na platformie Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Piaskownica usługi Azure Web App (ograniczenia wykonywania App Service Runtime)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Piątek Azure: Azure App Service środowisko diagnostyczne i rozwiązywania problemów (wideo 12-minutowy)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Dokumentacja programu Visual Studio

* [ASP.NET Core debugowania zdalnego na serwerze IIS na platformie Azure w programie Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure)
* [Zdalne debugowanie ASP.NET Core na zdalnym komputerze IIS w programie Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Nauka debugowania przy użyciu programu Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Dokumentacja Visual Studio Code

* [Debugowanie za pomocą Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Ten artykuł zawiera informacje dotyczące typowych błędów uruchamiania aplikacji oraz instrukcje dotyczące sposobu diagnozowania błędów podczas wdrażania aplikacji w usłudze Azure App Service lub IIS:

[Błędy uruchamiania aplikacji](#app-startup-errors)  
Objaśnia typowe scenariusze uruchamiania kodu stanu HTTP.

[Rozwiązywanie problemów dotyczących Azure App Service](#troubleshoot-on-azure-app-service)  
Zawiera porady dotyczące rozwiązywania problemów z aplikacjami wdrożonymi w celu Azure App Service.

[Rozwiązywanie problemów w usługach IIS](#troubleshoot-on-iis)  
Zawiera porady dotyczące rozwiązywania problemów z aplikacjami wdrożonymi w usługach IIS lub lokalnie uruchomionymi IIS Express. Wskazówki dotyczą zarówno wdrożeń systemu Windows Server, jak i pulpitu systemu Windows.

[Wyczyść pamięć podręczną pakietów](#clear-package-caches)  
Wyjaśnia, co należy zrobić, gdy niespójne pakiety przerywają działanie aplikacji podczas przeprowadzania uaktualnień głównych lub zmiany wersji pakietu.

[Dodatkowe zasoby](#additional-resources)  
Wyświetla listę dodatkowych tematów dotyczących rozwiązywania problemów.

## <a name="app-startup-errors"></a>Błędy uruchamiania aplikacji

W programie Visual Studio ASP.NET Core Project domyślnie [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting podczas debugowania. *502,5 — błąd procesu* lub *błąd uruchomienia 500,30* , który występuje, gdy debugowanie lokalne można zdiagnozować przy użyciu porady w tym temacie.

### <a name="40314-forbidden"></a>403,14 zabronione

Nie można uruchomić aplikacji. Rejestrowany jest następujący błąd:

```
The Web server is configured to not list the contents of this directory.
```

Ten błąd jest zwykle spowodowany przez uszkodzone wdrożenie w systemie hostingu, który obejmuje następujące scenariusze:

* Aplikacja jest wdrażana w niewłaściwym folderze w systemie hostingu.
* W procesie wdrażania nie powiodło się przeniesienie wszystkich plików i folderów aplikacji do folderu wdrożenia w systemie hostingu.
* W wdrożeniu brakuje pliku *web.config* lub zawartość pliku *web.config* jest źle sformułowana.

Wykonaj następujące czynności:

1. Usuń wszystkie pliki i foldery z folderu wdrożenia w systemie hostingu.
1. Wdróż ponownie zawartość folderu *publikowania* aplikacji w systemie hostingu przy użyciu zwykłej metody wdrażania, takiej jak Visual Studio, PowerShell lub wdrażanie ręczne:
   * Upewnij się, że plik *web.config* jest obecny we wdrożeniu i że jego zawartość jest poprawna.
   * Podczas hostowania w Azure App Service upewnij się, że aplikacja została wdrożona w `D:\home\site\wwwroot` folderze.
   * Jeśli aplikacja jest hostowana przez usługi IIS, upewnij się, że aplikacja jest wdrożona w **ścieżce fizycznej** usług IIS pokazanej w **ustawieniach podstawowych** w **Menedżerze usług IIS**.
1. Upewnij się, że wszystkie pliki i foldery aplikacji zostały wdrożone, porównując wdrożenie w systemie hostingu z zawartością folderu *publikowania* projektu.

Aby uzyskać więcej informacji na temat układu opublikowanej aplikacji ASP.NET Core, zobacz <xref:host-and-deploy/directory-structure> . Aby uzyskać więcej informacji na temat pliku *web.config* , zobacz <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> .

### <a name="500-internal-server-error"></a>500 Wewnętrzny błąd serwera

Aplikacja zostanie uruchomiona, ale błąd uniemożliwia serwerowi spełnienie żądania.

Ten błąd występuje w kodzie aplikacji podczas uruchamiania lub podczas tworzenia odpowiedzi. Odpowiedź może nie zawierać żadnej zawartości lub odpowiedź może pojawić się w przeglądarce jako *błąd wewnętrzny serwera 500* . Dziennik zdarzeń aplikacji zwykle stwierdza, że aplikacja została uruchomiona normalnie. Z perspektywy serwera jest to poprawne. Aplikacja została uruchomiona, ale nie może wygenerować prawidłowej odpowiedzi. Uruchom aplikację w wierszu polecenia na serwerze lub Włącz dziennik stdout modułu ASP.NET Core, aby rozwiązać problem.

### <a name="5000-in-process-handler-load-failure"></a>500,0 błąd ładowania procedury obsługi In-Process

Proces roboczy kończy się niepowodzeniem. Aplikacja nie zostanie uruchomiona.

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) nie może znaleźć platformy .NET Core CLR i znaleźć procedury obsługi żądań w procesie (*aspnetcorev2_inprocess.dll*). Sprawdź, czy:

* Aplikacja jest przeznaczona dla pakietu NuGet [Microsoft. AspNetCore. Server. IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) lub [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).
* Wersja ASP.NET Core udostępnionej platformy, do której aplikacja jest zainstalowana na maszynie docelowej.

### <a name="5000-out-of-process-handler-load-failure"></a>500,0 błąd ładowania procedury obsługi poza procesem

Proces roboczy kończy się niepowodzeniem. Aplikacja nie zostanie uruchomiona.

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) nie może odnaleźć procedury obsługi żądania hostingu poza procesem. Upewnij się, że *aspnetcorev2_outofprocess.dll* znajduje się w podfolderze obok *aspnetcorev2.dll*.

### <a name="5025-process-failure"></a>502.5 Błąd procesu

Proces roboczy kończy się niepowodzeniem. Aplikacja nie zostanie uruchomiona.

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) próbuje uruchomić proces roboczy, ale jego uruchomienie nie powiedzie się. Przyczyna niepowodzenia uruchomienia procesu zwykle można ustalić na podstawie wpisów w dzienniku zdarzeń aplikacji i dzienniku modułu ASP.NET Core stdout.

Typowym warunkiem niepowodzenia jest to, że aplikacja jest nieprawidłowo skonfigurowana z powodu docelowej wersji ASP.NET Core udostępnionej platformy, która nie istnieje. Sprawdź, które wersje ASP.NET Core udostępnionej platformy są zainstalowane na maszynie docelowej. *Platforma udostępniona* jest zestawem zestawów (plików *dll* ), które są zainstalowane na maszynie i do których odwołuje się pakiet `Microsoft.AspNetCore.App` . Odwołanie do pakietu nie może określać minimalnej wymaganej wersji. Aby uzyskać więcej informacji, zobacz [udostępnioną strukturę](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Strona błędu *niepowodzenia procesu 502,5* jest zwracana w przypadku niepowodzenia konfiguracji hostingu lub aplikacji. powoduje to niepowodzenie procesu roboczego:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Nie można uruchomić aplikacji (ErrorCode "0x800700C1")

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Nie można uruchomić aplikacji, ponieważ nie można załadować zestawu aplikacji (*dll*).

Ten błąd występuje, gdy wystąpiła niezgodność między opublikowaną aplikacją a procesem w3wp/iisexpress.

Upewnij się, że ustawienie 32-bitowe puli aplikacji jest poprawne:

1. Wybierz pulę aplikacji w **pulach aplikacji** Menedżera usług IIS.
1. Wybierz pozycję **Ustawienia zaawansowane** w obszarze **Edytuj pulę aplikacji** w panelu **Akcje** .
1. Ustaw **opcję Włącz aplikacje 32-bitowe**:
   * W przypadku wdrażania aplikacji 32-bitowej (x86) ustaw wartość na `True` .
   * W przypadku wdrażania aplikacji 64-bitowej (x64) ustaw wartość na `False` .

Upewnij się, że nie występuje konflikt między `<Platform>` właściwością programu MSBuild w pliku projektu a opublikowaną bitową w aplikacji.

### <a name="connection-reset"></a>Resetowanie połączenia

Jeśli wystąpi błąd po wysłaniu nagłówków, jest zbyt późno, aby serwer mógł wysłać **błąd wewnętrzny serwera 500** w przypadku wystąpienia błędu. Zdarza się to często w przypadku wystąpienia błędu podczas serializacji obiektów złożonych dla odpowiedzi. Ten typ błędu pojawia się jako błąd *resetowania połączenia* na kliencie. [Rejestrowanie aplikacji](xref:fundamentals/logging/index) może pomóc w rozwiązywaniu tego typu błędów.

### <a name="default-startup-limits"></a>Domyślne limity uruchamiania

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) jest skonfigurowany z domyślną *startupTimeLimitą* 120 sekund. Po lewej stronie wartości domyślnej, uruchomienie aplikacji może trwać do dwóch minut przed zarejestrowaniem awarii procesu przez moduł. Aby uzyskać informacje na temat konfigurowania modułu, zobacz [atrybuty elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Rozwiązywanie problemów dotyczących Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Dziennik zdarzeń aplikacji (Azure App Service)

Aby uzyskać dostęp do dziennika zdarzeń aplikacji, użyj bloku **diagnozowanie i rozwiązywanie problemów** w Azure Portal:

1. W Azure Portal Otwórz aplikację w **App Services**.
1. Kliknij pozycję **Diagnozowanie i rozwiązywanie problemów**.
1. Wybierz nagłówek **Narzędzia diagnostyczne** .
1. W obszarze **Narzędzia obsługi** wybierz przycisk **zdarzenia aplikacji** .
1. Zapoznaj się z najnowszym błędem podanym w pozycji *AspNetCoreModule IIS* lub *IIS AspNetCoreModule v2* w kolumnie **Źródło** .

Alternatywą dla korzystania z bloku **diagnozowanie i rozwiązywanie problemów** jest przetestowanie pliku dziennika zdarzeń aplikacji bezpośrednio przy użyciu [kudu](https://github.com/projectkudu/kudu/wiki):

1. Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** . Wybierz przycisk **Przejdź &rarr;** . Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.
1. Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.
1. Otwórz folder **LogFiles** .
1. Wybierz ikonę ołówka obok pliku *eventlog.xml* .
1. Przejrzyj dziennik. Przewiń w dół dziennika, aby zobaczyć najnowsze zdarzenia.

### <a name="run-the-app-in-the-kudu-console"></a>Uruchamianie aplikacji w konsoli kudu

Wiele błędów uruchamiania nie tworzy użytecznych informacji w dzienniku zdarzeń aplikacji. Możesz uruchomić aplikację w konsoli zdalnego wykonywania [kudu](https://github.com/projectkudu/kudu/wiki) , aby wykryć błąd:

1. Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** . Wybierz przycisk **Przejdź &rarr;** . Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.
1. Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.

#### <a name="test-a-32-bit-x86-app"></a>Testowanie aplikacji 32-bitowej (x86)

**Bieżąca wersja**

1. `cd d:\home\site\wwwroot`
1. Uruchom aplikację:
   * Jeśli aplikacja jest [wdrożeniem zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:

     ```console
     {ASSEMBLY NAME}.exe
     ```

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

**Wdrożenie zależne od platformy uruchomione w wersji zapoznawczej**

*Wymaga zainstalowania rozszerzenia witryny środowiska uruchomieniowego ASP.NET Core {VERSION} (x86).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` ( `{X.Y}` to wersja środowiska uruchomieniowego)
1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Testowanie aplikacji 64-bitowej (x64)

**Bieżąca wersja**

* Jeśli aplikacja jest wdrożeniem 64-bitowym (x64), [zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):
  1. `cd D:\Program Files\dotnet`
  1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:
  1. `cd D:\home\site\wwwroot`
  1. Uruchom aplikację: `{ASSEMBLY NAME}.exe`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

**Wdrożenie zależne od platformy uruchomione w wersji zapoznawczej**

*Wymaga zainstalowania rozszerzenia witryny środowiska uruchomieniowego ASP.NET Core {VERSION} (x64).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` ( `{X.Y}` to wersja środowiska uruchomieniowego)
1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>Dziennik stdout modułu ASP.NET Core (Azure App Service)

Dziennik modułu ASP.NET Core stdout często rejestruje przydatne komunikaty o błędach, które nie są dostępne w dzienniku zdarzeń aplikacji. Aby włączyć i wyświetlić dzienniki stdout:

1. Przejdź do bloku **diagnozowanie i rozwiązywanie problemów** w Azure Portal.
1. W obszarze **Wybierz kategorię problemu** wybierz przycisk **aplikacji sieci Web w dół** .
1. W obszarze **sugerowane rozwiązania** > **Włącz przekierowywanie dziennika stdout**, wybierz przycisk, aby **otworzyć konsolę kudu, aby edytować Web.Config**.
1. W **konsoli diagnostyki** kudu Otwórz foldery w **witrynie** Path  >  **wwwroot**. Przewiń w dół, aby odsłonić *web.config* plik w dolnej części listy.
1. Kliknij ikonę ołówka obok pliku *web.config* .
1. Ustaw wartość **stdoutLogEnabled** na `true` i zmień ścieżkę **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .
1. Wybierz pozycję **Zapisz** , aby zapisać zaktualizowany plik *web.config* .
1. Utwórz żądanie do aplikacji.
1. Wróć do witryny Azure Portal. Wybierz blok **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** . Wybierz przycisk **Przejdź &rarr;** . Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.
1. Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.
1. Wybierz folder **LogFiles** .
1. Sprawdź **zmodyfikowaną** kolumnę i wybierz ikonę ołówka, aby edytować dziennik stdout z datą ostatniej modyfikacji.
1. Po otwarciu pliku dziennika zostanie wyświetlony komunikat o błędzie.

Wyłącz rejestrowanie stdout po zakończeniu rozwiązywania problemów:

1. W **konsoli diagnostyki** kudu Wróć do **witryny** ścieżki  >  **wwwroot** , aby odsłonić plik *web.config* . Otwórz plik **web.config** ponownie, wybierając ikonę ołówka.
1. Ustaw wartość **stdoutLogEnabled** na `false` .
1. Wybierz pozycję **Zapisz** , aby zapisać plik.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika. Rejestrowania stdout można używać tylko w celu rozwiązywania problemów z uruchamianiem aplikacji.
>
> Aby uzyskać ogólne rejestrowanie w aplikacji ASP.NET Core po uruchomieniu, należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>Dziennik debugowania modułu ASP.NET Core (Azure App Service)

Dziennik debugowania modułu ASP.NET Core zapewnia dodatkowe, dokładniejsze rejestrowanie z modułu ASP.NET Core. Aby włączyć i wyświetlić dzienniki stdout:

1. Aby włączyć Rozszerzony Dziennik diagnostyczny, wykonaj jedną z następujących czynności:
   * Postępuj zgodnie z instrukcjami w temacie [udoskonalone dzienniki diagnostyczne](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) , aby skonfigurować aplikację do rozszerzonego rejestrowania diagnostycznego. Wdróż ponownie aplikację.
   * Dodaj `<handlerSettings>` pokazany w [ulepszonych dziennikach diagnostycznych](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do pliku *web.config* aplikacji na żywo za pomocą konsoli kudu:
     1. Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** . Wybierz przycisk **Przejdź &rarr;** . Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.
     1. Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.
     1. Otwórz foldery w **witrynie** Path  >  **wwwroot**. Edytuj plik *web.config* , wybierając przycisk ołówka. Dodaj `<handlerSettings>` sekcję, jak pokazano w [udoskonalonych dziennikach diagnostycznych](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Wybierz ikonę **Zapisz**.
1. Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** . Wybierz przycisk **Przejdź &rarr;** . Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.
1. Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.
1. Otwórz foldery w **witrynie** Path  >  **wwwroot**. Jeśli nie podano ścieżki do pliku *aspnetcore-Debug. log* , plik zostanie wyświetlony na liście. Jeśli podano ścieżkę, przejdź do lokalizacji pliku dziennika.
1. Otwórz plik dziennika z przyciskiem ołówek obok nazwy pliku.

Wyłącz rejestrowanie debugowania po zakończeniu rozwiązywania problemów:

Aby wyłączyć rozszerzony Dziennik debugowania, wykonaj jedną z następujących czynności:

* Usuń `<handlerSettings>` plik z pliku *web.config* lokalnie i Wdróż ponownie aplikację.
* Za pomocą konsoli kudu Edytuj plik *web.config* i Usuń `<handlerSettings>` sekcję. Zapisz plik.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> Niepowodzenie wyłączenia dziennika debugowania może prowadzić do awarii aplikacji lub serwera. Nie ma limitu rozmiaru pliku dziennika. Funkcja rejestrowania debugowania służy tylko do rozwiązywania problemów z uruchamianiem aplikacji.
>
> Aby uzyskać ogólne rejestrowanie w aplikacji ASP.NET Core po uruchomieniu, należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.

### <a name="slow-or-hanging-app-azure-app-service"></a>Aplikacja wolna lub wysunięta (Azure App Service)

Gdy aplikacja reaguje powoli lub zawiesza się na żądanie, zobacz następujące artykuły:

* [Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Użyj rozszerzenia witryny diagnostyki awarii, aby przechwycić zrzut dla sporadycznych problemów z wyjątkami lub problemów z wydajnością w usłudze Azure Web App](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>Bloki monitorowania

Bloki monitorowania zapewniają alternatywne środowisko rozwiązywania problemów z metodami opisanymi wcześniej w temacie. Te bloki mogą służyć do diagnozowania błędów serii 500.

Upewnij się, że rozszerzenia ASP.NET Core są zainstalowane. Jeśli rozszerzenia nie są zainstalowane, zainstaluj je ręcznie:

1. W sekcji blok **narzędzi programistycznych** wybierz blok **rozszerzenia** .
1. **Rozszerzenia ASP.NET Core** powinny znajdować się na liście.
1. Jeśli rozszerzenia nie są zainstalowane, wybierz przycisk **Dodaj** .
1. Wybierz z listy **rozszerzenia ASP.NET Core** .
1. Wybierz **przycisk OK** , aby zaakceptować postanowienia prawne.
1. W bloku **Dodaj rozszerzenie** wybierz pozycję **OK** .
1. Komunikat podręczny informujący o pomyślnym zainstalowaniu rozszerzeń.

Jeśli rejestrowanie stdout nie jest włączone, wykonaj następujące kroki:

1. W Azure Portal wybierz blok **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** . Wybierz przycisk **Przejdź &rarr;** . Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.
1. Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.
1. Otwórz foldery w **witrynie** Path > **wwwroot** i przewiń w dół, aby odsłonić *web.config* plik w dolnej części listy.
1. Kliknij ikonę ołówka obok pliku *web.config* .
1. Ustaw wartość **stdoutLogEnabled** na `true` i zmień ścieżkę **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .
1. Wybierz pozycję **Zapisz** , aby zapisać zaktualizowany plik *web.config* .

Wykonaj aktywację rejestrowania diagnostycznego:

1. W Azure Portal wybierz blok **dzienników diagnostycznych** .
1. Wybierz pozycję **Włącz** , aby włączyć **Rejestrowanie aplikacji (system plików)** i **szczegółowe komunikaty o błędach**. Wybierz przycisk **Zapisz** znajdujący się u góry bloku.
1. Aby uwzględnić śledzenie nieudanych żądań, znane także jako rejestrowanie nieudanych żądań buforowania zdarzeń (FREB), wybierz **przełącznik dla** **śledzenia nieudanych żądań**.
1. Wybierz blok **strumień dziennika** , który jest wyświetlany bezpośrednio w bloku **dzienników diagnostycznych** w portalu.
1. Utwórz żądanie do aplikacji.
1. W danych strumienia dziennika jest wskazywana Przyczyna błędu.

Należy pamiętać o wyłączeniu rejestrowania stdout po zakończeniu rozwiązywania problemów.

Aby wyświetlić dzienniki śledzenia niepomyślnych żądań (dzienniki FREB):

1. Przejdź do bloku **diagnozowanie i rozwiązywanie problemów** w Azure Portal.
1. Wybierz pozycję **dzienniki śledzenia niepomyślnych żądań** z obszaru **Narzędzia obsługi** na pasku bocznym.

Zobacz [sekcję śledzenie niepomyślnych żądań w temacie Włączanie rejestrowania diagnostyki dla aplikacji sieci Web w programie Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) i informacje [o wydajności aplikacji dotyczące Web Apps na platformie Azure: Jak mogę włączyć śledzenie nieudanych żądań?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Aby uzyskać więcej informacji.

Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania diagnostycznego dla aplikacji sieci Web w Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika.
>
> Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.

## <a name="troubleshoot-on-iis"></a>Rozwiązywanie problemów w usługach IIS

### <a name="application-event-log-iis"></a>Dziennik zdarzeń aplikacji (IIS)

Dostęp do dziennika zdarzeń aplikacji:

1. Otwórz menu Start, wyszukaj ciąg *Podgląd zdarzeń* i wybierz aplikację **Podgląd zdarzeń** .
1. W **Podgląd zdarzeń** Otwórz węzeł **Dzienniki systemu Windows** .
1. Wybierz pozycję **aplikacja** , aby otworzyć dziennik zdarzeń aplikacji.
1. Wyszukaj błędy związane z niepowodzeniem aplikacji. Błędy mają wartość *modułu AspNetCore IIS* lub *moduł IIS Express AspNetCore* w kolumnie *Źródło* .

### <a name="run-the-app-at-a-command-prompt"></a>Uruchamianie aplikacji w wierszu polecenia

Wiele błędów uruchamiania nie tworzy użytecznych informacji w dzienniku zdarzeń aplikacji. Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingu.

#### <a name="framework-dependent-deployment"></a>Wdrożenie zależne od platformy

Jeśli aplikacja jest [wdrożeniem zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

1. W wierszu polecenia przejdź do folderu wdrożenia i uruchom aplikację, wykonując zestaw aplikacji za pomocą *dotnet.exe*. W poniższym poleceniu Zastąp nazwę zestawu aplikacji dla \<assembly_name> : `dotnet .\<assembly_name>.dll` .
1. Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.
1. Jeśli wystąpią błędy podczas wysyłania żądania do aplikacji, należy wysłać żądanie do hosta i portu, gdzie Kestrel nasłuchuje. Przy użyciu domyślnego hosta i wpisu Wyślij żądanie do `http://localhost:5000/` . Jeśli aplikacja będzie odpowiadać zwykle na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobnie związany z konfiguracją hostingu i mniej korzystnie w aplikacji.

#### <a name="self-contained-deployment"></a>Wdrażanie samodzielne

Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:

1. W wierszu polecenia przejdź do folderu wdrożenia i uruchom plik wykonywalny aplikacji. W poniższym poleceniu Zastąp nazwę zestawu aplikacji dla \<assembly_name> : `<assembly_name>.exe` .
1. Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.
1. Jeśli wystąpią błędy podczas wysyłania żądania do aplikacji, należy wysłać żądanie do hosta i portu, gdzie Kestrel nasłuchuje. Przy użyciu domyślnego hosta i wpisu Wyślij żądanie do `http://localhost:5000/` . Jeśli aplikacja będzie odpowiadać zwykle na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobnie związany z konfiguracją hostingu i mniej korzystnie w aplikacji.

### <a name="aspnet-core-module-stdout-log-iis"></a>Dziennik stdout modułu ASP.NET Core (IIS)

Aby włączyć i wyświetlić dzienniki stdout:

1. Przejdź do folderu wdrożenia lokacji w systemie hostingu.
1. Jeśli folder *Logs* nie istnieje, utwórz go. Aby uzyskać instrukcje dotyczące włączania programu MSBuild do automatycznego tworzenia folderu *Logs* w ramach wdrożenia, zobacz temat [Struktura katalogów](xref:host-and-deploy/directory-structure) .
1. Edytuj plik *web.config* . Ustaw wartość **stdoutLogEnabled** na `true` i zmień ścieżkę **stdoutLogFile** , tak aby wskazywała folder *Logs* (na przykład `.\logs\stdout` ). `stdout` ścieżka jest prefiksem nazwy pliku dziennika. Sygnatura czasowa, identyfikator procesu i rozszerzenie pliku są dodawane automatycznie podczas tworzenia dziennika. Używając `stdout` jako prefiksu nazwy pliku, typowy plik dziennika ma nazwę *stdout_20180205184032_5412. log*.
1. Upewnij się, że tożsamość puli aplikacji ma uprawnienia do zapisu w folderze *Logs* .
1. Zapisz zaktualizowany plik *web.config* .
1. Utwórz żądanie do aplikacji.
1. Przejdź do folderu *Logs* . Znajdź i Otwórz najnowszy dziennik stdout.
1. Zbadaj dziennik pod kątem błędów.

Wyłącz rejestrowanie stdout po zakończeniu rozwiązywania problemów:

1. Edytuj plik *web.config* .
1. Ustaw wartość **stdoutLogEnabled** na `false` .
1. Zapisz plik.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika.
>
> Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.

### <a name="aspnet-core-module-debug-log-iis"></a>Dziennik debugowania modułu ASP.NET Core (IIS)

Dodaj następujące ustawienia programu obsługi do pliku *web.config* aplikacji, aby włączyć Dziennik debugowania modułu ASP.NET Core:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Upewnij się, że ścieżka określona dla dziennika istnieje i że tożsamość puli aplikacji ma uprawnienia do zapisu w tej lokalizacji.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

### <a name="enable-the-developer-exception-page"></a>Włącz stronę wyjątków dla deweloperów

`ASPNETCORE_ENVIRONMENT` [Zmienną środowiskową można dodać do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , aby uruchomić aplikację w środowisku deweloperskim. O ile środowisko nie zostanie zastąpione podczas uruchamiania aplikacji przez `UseEnvironment` konstruktora hosta, ustawienie zmiennej środowiskowej umożliwia wyświetlenie [strony wyjątku dewelopera](xref:fundamentals/error-handling) po uruchomieniu aplikacji.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

Ustawienie zmiennej środowiskowej dla programu `ASPNETCORE_ENVIRONMENT` jest zalecane wyłącznie do użytku na przejściowych i testowych serwerach, które nie są dostępne w Internecie. Usuń zmienną środowiskową z pliku *web.config* po rozwiązaniu problemu. Aby uzyskać informacje na temat ustawiania zmiennych środowiskowych w *web.config*, zobacz [EnvironmentVariables podrzędny element aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Uzyskiwanie danych z aplikacji

Jeśli aplikacja może odpowiadać na żądania, uzyskiwać żądania, połączenia i dodatkowe dane z aplikacji przy użyciu wbudowanego oprogramowania terminala. Aby uzyskać więcej informacji i przykładowy kod, zobacz <xref:test/troubleshoot#obtain-data-from-an-app> .

### <a name="slow-or-hanging-app-iis"></a>Aplikacja wolna lub wysunięta (IIS)

*Zrzut awaryjny* to migawka pamięci systemu, która może pomóc w ustaleniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.

#### <a name="app-crashes-or-encounters-an-exception"></a>Awaria aplikacji lub napotka wyjątek

Uzyskaj i Analizuj Zrzut z [raportowanie błędów systemu Windows (raportowanie błędów systemu Windows)](/windows/desktop/wer/windows-error-reporting):

1. Utwórz folder do przechowywania plików zrzutu awaryjnego w `c:\dumps` . Pula aplikacji musi mieć dostęp do zapisu w folderze.
1. Uruchom [skrypt programu PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)w programie EnableDumps:
   * Jeśli aplikacja korzysta z [modelu hostingu w procesie](xref:host-and-deploy/iis/index#in-process-hosting-model), uruchom skrypt dla *w3wp.exe*:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Jeśli aplikacja korzysta z [modelu hostingu poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), uruchom skrypt dla *dotnet.exe*:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Uruchom aplikację w warunkach, które powodują awarię.
1. Po wystąpieniu awarii Uruchom [skrypt programu DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):
   * Jeśli aplikacja korzysta z [modelu hostingu w procesie](xref:host-and-deploy/iis/index#in-process-hosting-model), uruchom skrypt dla *w3wp.exe*:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Jeśli aplikacja korzysta z [modelu hostingu poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), uruchom skrypt dla *dotnet.exe*:

     ```console
     .\DisableDumps dotnet.exe
     ```

Po awarii aplikacji i zakończeniu zbierania zrzutów aplikacja może zakończyć normalne działanie. Skrypt programu PowerShell konfiguruje raportowanie błędów systemu Windows w celu zebrania do pięciu zrzutów na aplikację.

> [!WARNING]
> Zrzuty awaryjne mogą wymagać dużej ilości miejsca na dysku (do kilku gigabajtów).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie

Gdy aplikacja *zawiesza* się (bez awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [pliki zrzutu w trybie użytkownika: wybór najlepszego narzędzia](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) do wygenerowania zrzutu.

#### <a name="analyze-the-dump"></a>Analizowanie zrzutu

Zrzut można analizować przy użyciu kilku metod. Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu User-Mode](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="clear-package-caches"></a>Wyczyść pamięć podręczną pakietów

Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji. W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych. Większość tych problemów można naprawić, wykonując następujące instrukcje:

1. Usuń foldery *bin* i *obj* .
1. Wyczyść pamięć podręczną pakietów, wykonując [wszystkie elementy lokalne usługi NuGet programu dotnet--Wyczyść](/dotnet/core/tools/dotnet-nuget-locals) z poziomu powłoki poleceń.

   Czyszczenie pamięci podręcznych pakietów można także wykonać przy użyciu narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonując polecenie `nuget locals all -clear` . *nuget.exe* nie jest instalacją w pakiecie z systemem operacyjnym Windows dla komputerów stacjonarnych i należy ją pobrać niezależnie od [witryny sieci Web programu NuGet](https://www.nuget.org/downloads).

1. Przywróć i skompiluj projekt.
1. Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Dokumentacja platformy Azure

* [Usługa Application Insights dla platformy ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Sekcja zdalne debugowanie aplikacji sieci Web Rozwiązywanie problemów z aplikacją sieci Web w Azure App Service przy użyciu programu Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Omówienie diagnostyki Azure App Service](/azure/app-service/app-service-diagnostics)
* [Jak monitorować aplikacje w usłudze Azure App Service](/azure/app-service/web-sites-monitor)
* [Rozwiązywanie problemów z aplikacją sieci Web w Azure App Service przy użyciu programu Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Rozwiązywanie problemów z błędami HTTP "502 złej Gateway" i "503 Usługa niedostępna" w usłudze Azure Web Apps](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Często zadawane pytania dotyczące wydajności aplikacji dla Web Apps na platformie Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Piaskownica usługi Azure Web App (ograniczenia wykonywania App Service Runtime)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Piątek Azure: Azure App Service środowisko diagnostyczne i rozwiązywania problemów (wideo 12-minutowy)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Dokumentacja programu Visual Studio

* [ASP.NET Core debugowania zdalnego na serwerze IIS na platformie Azure w programie Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure)
* [Zdalne debugowanie ASP.NET Core na zdalnym komputerze IIS w programie Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Nauka debugowania przy użyciu programu Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Dokumentacja Visual Studio Code

* [Debugowanie za pomocą Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Ten artykuł zawiera informacje dotyczące typowych błędów uruchamiania aplikacji oraz instrukcje dotyczące sposobu diagnozowania błędów podczas wdrażania aplikacji w usłudze Azure App Service lub IIS:

[Błędy uruchamiania aplikacji](#app-startup-errors)  
Objaśnia typowe scenariusze uruchamiania kodu stanu HTTP.

[Rozwiązywanie problemów dotyczących Azure App Service](#troubleshoot-on-azure-app-service)  
Zawiera porady dotyczące rozwiązywania problemów z aplikacjami wdrożonymi w celu Azure App Service.

[Rozwiązywanie problemów w usługach IIS](#troubleshoot-on-iis)  
Zawiera porady dotyczące rozwiązywania problemów z aplikacjami wdrożonymi w usługach IIS lub lokalnie uruchomionymi IIS Express. Wskazówki dotyczą zarówno wdrożeń systemu Windows Server, jak i pulpitu systemu Windows.

[Wyczyść pamięć podręczną pakietów](#clear-package-caches)  
Wyjaśnia, co należy zrobić, gdy niespójne pakiety przerywają działanie aplikacji podczas przeprowadzania uaktualnień głównych lub zmiany wersji pakietu.

[Dodatkowe zasoby](#additional-resources)  
Wyświetla listę dodatkowych tematów dotyczących rozwiązywania problemów.

## <a name="app-startup-errors"></a>Błędy uruchamiania aplikacji

W programie Visual Studio ASP.NET Core Project domyślnie [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting podczas debugowania. *Błąd procesu 502,5* , który występuje, gdy debugowanie lokalne można zdiagnozować przy użyciu porady w tym temacie.

### <a name="40314-forbidden"></a>403,14 zabronione

Nie można uruchomić aplikacji. Rejestrowany jest następujący błąd:

```
The Web server is configured to not list the contents of this directory.
```

Ten błąd jest zwykle spowodowany przez uszkodzone wdrożenie w systemie hostingu, który obejmuje następujące scenariusze:

* Aplikacja jest wdrażana w niewłaściwym folderze w systemie hostingu.
* W procesie wdrażania nie powiodło się przeniesienie wszystkich plików i folderów aplikacji do folderu wdrożenia w systemie hostingu.
* W wdrożeniu brakuje pliku *web.config* lub zawartość pliku *web.config* jest źle sformułowana.

Wykonaj następujące czynności:

1. Usuń wszystkie pliki i foldery z folderu wdrożenia w systemie hostingu.
1. Wdróż ponownie zawartość folderu *publikowania* aplikacji w systemie hostingu przy użyciu zwykłej metody wdrażania, takiej jak Visual Studio, PowerShell lub wdrażanie ręczne:
   * Upewnij się, że plik *web.config* jest obecny we wdrożeniu i że jego zawartość jest poprawna.
   * Podczas hostowania w Azure App Service upewnij się, że aplikacja została wdrożona w `D:\home\site\wwwroot` folderze.
   * Jeśli aplikacja jest hostowana przez usługi IIS, upewnij się, że aplikacja jest wdrożona w **ścieżce fizycznej** usług IIS pokazanej w **ustawieniach podstawowych** w **Menedżerze usług IIS**.
1. Upewnij się, że wszystkie pliki i foldery aplikacji zostały wdrożone, porównując wdrożenie w systemie hostingu z zawartością folderu *publikowania* projektu.

Aby uzyskać więcej informacji na temat układu opublikowanej aplikacji ASP.NET Core, zobacz <xref:host-and-deploy/directory-structure> . Aby uzyskać więcej informacji na temat pliku *web.config* , zobacz <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> .

### <a name="500-internal-server-error"></a>500 Wewnętrzny błąd serwera

Aplikacja zostanie uruchomiona, ale błąd uniemożliwia serwerowi spełnienie żądania.

Ten błąd występuje w kodzie aplikacji podczas uruchamiania lub podczas tworzenia odpowiedzi. Odpowiedź może nie zawierać żadnej zawartości lub odpowiedź może pojawić się w przeglądarce jako *błąd wewnętrzny serwera 500* . Dziennik zdarzeń aplikacji zwykle stwierdza, że aplikacja została uruchomiona normalnie. Z perspektywy serwera jest to poprawne. Aplikacja została uruchomiona, ale nie może wygenerować prawidłowej odpowiedzi. Uruchom aplikację w wierszu polecenia na serwerze lub Włącz dziennik stdout modułu ASP.NET Core, aby rozwiązać problem.

### <a name="5025-process-failure"></a>502.5 Błąd procesu

Proces roboczy kończy się niepowodzeniem. Aplikacja nie zostanie uruchomiona.

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) próbuje uruchomić proces roboczy, ale jego uruchomienie nie powiedzie się. Przyczyna niepowodzenia uruchomienia procesu zwykle można ustalić na podstawie wpisów w dzienniku zdarzeń aplikacji i dzienniku modułu ASP.NET Core stdout.

Typowym warunkiem niepowodzenia jest to, że aplikacja jest nieprawidłowo skonfigurowana z powodu docelowej wersji ASP.NET Core udostępnionej platformy, która nie istnieje. Sprawdź, które wersje ASP.NET Core udostępnionej platformy są zainstalowane na maszynie docelowej. *Platforma udostępniona* jest zestawem zestawów (plików *dll* ), które są zainstalowane na maszynie i do których odwołuje się pakiet `Microsoft.AspNetCore.App` . Odwołanie do pakietu nie może określać minimalnej wymaganej wersji. Aby uzyskać więcej informacji, zobacz [udostępnioną strukturę](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Strona błędu *niepowodzenia procesu 502,5* jest zwracana w przypadku niepowodzenia konfiguracji hostingu lub aplikacji. powoduje to niepowodzenie procesu roboczego:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Nie można uruchomić aplikacji (ErrorCode "0x800700C1")

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Nie można uruchomić aplikacji, ponieważ nie można załadować zestawu aplikacji (*dll*).

Ten błąd występuje, gdy wystąpiła niezgodność między opublikowaną aplikacją a procesem w3wp/iisexpress.

Upewnij się, że ustawienie 32-bitowe puli aplikacji jest poprawne:

1. Wybierz pulę aplikacji w **pulach aplikacji** Menedżera usług IIS.
1. Wybierz pozycję **Ustawienia zaawansowane** w obszarze **Edytuj pulę aplikacji** w panelu **Akcje** .
1. Ustaw **opcję Włącz aplikacje 32-bitowe**:
   * W przypadku wdrażania aplikacji 32-bitowej (x86) ustaw wartość na `True` .
   * W przypadku wdrażania aplikacji 64-bitowej (x64) ustaw wartość na `False` .

Upewnij się, że nie występuje konflikt między `<Platform>` właściwością programu MSBuild w pliku projektu a opublikowaną bitową w aplikacji.

### <a name="connection-reset"></a>Resetowanie połączenia

Jeśli wystąpi błąd po wysłaniu nagłówków, jest zbyt późno, aby serwer mógł wysłać **błąd wewnętrzny serwera 500** w przypadku wystąpienia błędu. Zdarza się to często w przypadku wystąpienia błędu podczas serializacji obiektów złożonych dla odpowiedzi. Ten typ błędu pojawia się jako błąd *resetowania połączenia* na kliencie. [Rejestrowanie aplikacji](xref:fundamentals/logging/index) może pomóc w rozwiązywaniu tego typu błędów.

### <a name="default-startup-limits"></a>Domyślne limity uruchamiania

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) jest skonfigurowany z domyślną *startupTimeLimitą* 120 sekund. Po lewej stronie wartości domyślnej, uruchomienie aplikacji może trwać do dwóch minut przed zarejestrowaniem awarii procesu przez moduł. Aby uzyskać informacje na temat konfigurowania modułu, zobacz [atrybuty elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Rozwiązywanie problemów dotyczących Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Dziennik zdarzeń aplikacji (Azure App Service)

Aby uzyskać dostęp do dziennika zdarzeń aplikacji, użyj bloku **diagnozowanie i rozwiązywanie problemów** w Azure Portal:

1. W Azure Portal Otwórz aplikację w **App Services**.
1. Kliknij pozycję **Diagnozowanie i rozwiązywanie problemów**.
1. Wybierz nagłówek **Narzędzia diagnostyczne** .
1. W obszarze **Narzędzia obsługi** wybierz przycisk **zdarzenia aplikacji** .
1. Zapoznaj się z najnowszym błędem podanym w pozycji *AspNetCoreModule IIS* lub *IIS AspNetCoreModule v2* w kolumnie **Źródło** .

Alternatywą dla korzystania z bloku **diagnozowanie i rozwiązywanie problemów** jest przetestowanie pliku dziennika zdarzeń aplikacji bezpośrednio przy użyciu [kudu](https://github.com/projectkudu/kudu/wiki):

1. Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** . Wybierz przycisk **Przejdź &rarr;** . Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.
1. Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.
1. Otwórz folder **LogFiles** .
1. Wybierz ikonę ołówka obok pliku *eventlog.xml* .
1. Przejrzyj dziennik. Przewiń w dół dziennika, aby zobaczyć najnowsze zdarzenia.

### <a name="run-the-app-in-the-kudu-console"></a>Uruchamianie aplikacji w konsoli kudu

Wiele błędów uruchamiania nie tworzy użytecznych informacji w dzienniku zdarzeń aplikacji. Możesz uruchomić aplikację w konsoli zdalnego wykonywania [kudu](https://github.com/projectkudu/kudu/wiki) , aby wykryć błąd:

1. Otwórz **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** . Wybierz przycisk **Przejdź &rarr;** . Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.
1. Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.

#### <a name="test-a-32-bit-x86-app"></a>Testowanie aplikacji 32-bitowej (x86)

**Bieżąca wersja**

1. `cd d:\home\site\wwwroot`
1. Uruchom aplikację:
   * Jeśli aplikacja jest [wdrożeniem zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:

     ```console
     {ASSEMBLY NAME}.exe
     ```

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

**Wdrożenie zależne od platformy uruchomione w wersji zapoznawczej**

*Wymaga zainstalowania rozszerzenia witryny środowiska uruchomieniowego ASP.NET Core {VERSION} (x86).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` ( `{X.Y}` to wersja środowiska uruchomieniowego)
1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Testowanie aplikacji 64-bitowej (x64)

**Bieżąca wersja**

* Jeśli aplikacja jest wdrożeniem 64-bitowym (x64), [zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):
  1. `cd D:\Program Files\dotnet`
  1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:
  1. `cd D:\home\site\wwwroot`
  1. Uruchom aplikację: `{ASSEMBLY NAME}.exe`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

**Wdrożenie zależne od platformy uruchomione w wersji zapoznawczej**

*Wymaga zainstalowania rozszerzenia witryny środowiska uruchomieniowego ASP.NET Core {VERSION} (x64).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` ( `{X.Y}` to wersja środowiska uruchomieniowego)
1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>Dziennik stdout modułu ASP.NET Core (Azure App Service)

Dziennik modułu ASP.NET Core stdout często rejestruje przydatne komunikaty o błędach, które nie są dostępne w dzienniku zdarzeń aplikacji. Aby włączyć i wyświetlić dzienniki stdout:

1. Przejdź do bloku **diagnozowanie i rozwiązywanie problemów** w Azure Portal.
1. W obszarze **Wybierz kategorię problemu** wybierz przycisk **aplikacji sieci Web w dół** .
1. W obszarze **sugerowane rozwiązania** > **Włącz przekierowywanie dziennika stdout**, wybierz przycisk, aby **otworzyć konsolę kudu, aby edytować Web.Config**.
1. W **konsoli diagnostyki** kudu Otwórz foldery w **witrynie** Path  >  **wwwroot**. Przewiń w dół, aby odsłonić *web.config* plik w dolnej części listy.
1. Kliknij ikonę ołówka obok pliku *web.config* .
1. Ustaw wartość **stdoutLogEnabled** na `true` i zmień ścieżkę **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .
1. Wybierz pozycję **Zapisz** , aby zapisać zaktualizowany plik *web.config* .
1. Utwórz żądanie do aplikacji.
1. Wróć do witryny Azure Portal. Wybierz blok **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** . Wybierz przycisk **Przejdź &rarr;** . Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.
1. Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.
1. Wybierz folder **LogFiles** .
1. Sprawdź **zmodyfikowaną** kolumnę i wybierz ikonę ołówka, aby edytować dziennik stdout z datą ostatniej modyfikacji.
1. Po otwarciu pliku dziennika zostanie wyświetlony komunikat o błędzie.

Wyłącz rejestrowanie stdout po zakończeniu rozwiązywania problemów:

1. W **konsoli diagnostyki** kudu Wróć do **witryny** ścieżki  >  **wwwroot** , aby odsłonić plik *web.config* . Otwórz plik **web.config** ponownie, wybierając ikonę ołówka.
1. Ustaw wartość **stdoutLogEnabled** na `false` .
1. Wybierz pozycję **Zapisz** , aby zapisać plik.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika. Rejestrowania stdout można używać tylko w celu rozwiązywania problemów z uruchamianiem aplikacji.
>
> Aby uzyskać ogólne rejestrowanie w aplikacji ASP.NET Core po uruchomieniu, należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.

### <a name="slow-or-hanging-app-azure-app-service"></a>Aplikacja wolna lub wysunięta (Azure App Service)

Gdy aplikacja reaguje powoli lub zawiesza się na żądanie, zobacz następujące artykuły:

* [Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Użyj rozszerzenia witryny diagnostyki awarii, aby przechwycić zrzut dla sporadycznych problemów z wyjątkami lub problemów z wydajnością w usłudze Azure Web App](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>Bloki monitorowania

Bloki monitorowania zapewniają alternatywne środowisko rozwiązywania problemów z metodami opisanymi wcześniej w temacie. Te bloki mogą służyć do diagnozowania błędów serii 500.

Upewnij się, że rozszerzenia ASP.NET Core są zainstalowane. Jeśli rozszerzenia nie są zainstalowane, zainstaluj je ręcznie:

1. W sekcji blok **narzędzi programistycznych** wybierz blok **rozszerzenia** .
1. **Rozszerzenia ASP.NET Core** powinny znajdować się na liście.
1. Jeśli rozszerzenia nie są zainstalowane, wybierz przycisk **Dodaj** .
1. Wybierz z listy **rozszerzenia ASP.NET Core** .
1. Wybierz **przycisk OK** , aby zaakceptować postanowienia prawne.
1. W bloku **Dodaj rozszerzenie** wybierz pozycję **OK** .
1. Komunikat podręczny informujący o pomyślnym zainstalowaniu rozszerzeń.

Jeśli rejestrowanie stdout nie jest włączone, wykonaj następujące kroki:

1. W Azure Portal wybierz blok **Narzędzia zaawansowane** w obszarze **Narzędzia programistyczne** . Wybierz przycisk **Przejdź &rarr;** . Konsola kudu otwiera się w nowej karcie lub oknie przeglądarki.
1. Korzystając z paska nawigacyjnego w górnej części strony, Otwórz **konsolę debugowanie** i wybierz polecenie **cmd**.
1. Otwórz foldery w **witrynie** Path > **wwwroot** i przewiń w dół, aby odsłonić *web.config* plik w dolnej części listy.
1. Kliknij ikonę ołówka obok pliku *web.config* .
1. Ustaw wartość **stdoutLogEnabled** na `true` i zmień ścieżkę **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout` .
1. Wybierz pozycję **Zapisz** , aby zapisać zaktualizowany plik *web.config* .

Wykonaj aktywację rejestrowania diagnostycznego:

1. W Azure Portal wybierz blok **dzienników diagnostycznych** .
1. Wybierz pozycję **Włącz** , aby włączyć **Rejestrowanie aplikacji (system plików)** i **szczegółowe komunikaty o błędach**. Wybierz przycisk **Zapisz** znajdujący się u góry bloku.
1. Aby uwzględnić śledzenie nieudanych żądań, znane także jako rejestrowanie nieudanych żądań buforowania zdarzeń (FREB), wybierz **przełącznik dla** **śledzenia nieudanych żądań**.
1. Wybierz blok **strumień dziennika** , który jest wyświetlany bezpośrednio w bloku **dzienników diagnostycznych** w portalu.
1. Utwórz żądanie do aplikacji.
1. W danych strumienia dziennika jest wskazywana Przyczyna błędu.

Należy pamiętać o wyłączeniu rejestrowania stdout po zakończeniu rozwiązywania problemów.

Aby wyświetlić dzienniki śledzenia niepomyślnych żądań (dzienniki FREB):

1. Przejdź do bloku **diagnozowanie i rozwiązywanie problemów** w Azure Portal.
1. Wybierz pozycję **dzienniki śledzenia niepomyślnych żądań** z obszaru **Narzędzia obsługi** na pasku bocznym.

Zobacz [sekcję śledzenie niepomyślnych żądań w temacie Włączanie rejestrowania diagnostyki dla aplikacji sieci Web w programie Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) i informacje [o wydajności aplikacji dotyczące Web Apps na platformie Azure: Jak mogę włączyć śledzenie nieudanych żądań?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) Aby uzyskać więcej informacji.

Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania diagnostycznego dla aplikacji sieci Web w Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika.
>
> Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.

## <a name="troubleshoot-on-iis"></a>Rozwiązywanie problemów w usługach IIS

### <a name="application-event-log-iis"></a>Dziennik zdarzeń aplikacji (IIS)

Dostęp do dziennika zdarzeń aplikacji:

1. Otwórz menu Start, wyszukaj ciąg *Podgląd zdarzeń* i wybierz aplikację **Podgląd zdarzeń** .
1. W **Podgląd zdarzeń** Otwórz węzeł **Dzienniki systemu Windows** .
1. Wybierz pozycję **aplikacja** , aby otworzyć dziennik zdarzeń aplikacji.
1. Wyszukaj błędy związane z niepowodzeniem aplikacji. Błędy mają wartość *modułu AspNetCore IIS* lub *moduł IIS Express AspNetCore* w kolumnie *Źródło* .

### <a name="run-the-app-at-a-command-prompt"></a>Uruchamianie aplikacji w wierszu polecenia

Wiele błędów uruchamiania nie tworzy użytecznych informacji w dzienniku zdarzeń aplikacji. Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingu.

#### <a name="framework-dependent-deployment"></a>Wdrożenie zależne od platformy

Jeśli aplikacja jest [wdrożeniem zależnym od platformy](/dotnet/core/deploying/#framework-dependent-deployments-fdd):

1. W wierszu polecenia przejdź do folderu wdrożenia i uruchom aplikację, wykonując zestaw aplikacji za pomocą *dotnet.exe*. W poniższym poleceniu Zastąp nazwę zestawu aplikacji dla \<assembly_name> : `dotnet .\<assembly_name>.dll` .
1. Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.
1. Jeśli wystąpią błędy podczas wysyłania żądania do aplikacji, należy wysłać żądanie do hosta i portu, gdzie Kestrel nasłuchuje. Przy użyciu domyślnego hosta i wpisu Wyślij żądanie do `http://localhost:5000/` . Jeśli aplikacja będzie odpowiadać zwykle na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobnie związany z konfiguracją hostingu i mniej korzystnie w aplikacji.

#### <a name="self-contained-deployment"></a>Wdrażanie samodzielne

Jeśli aplikacja jest [wdrożeniem](/dotnet/core/deploying/#self-contained-deployments-scd)niezależnym:

1. W wierszu polecenia przejdź do folderu wdrożenia i uruchom plik wykonywalny aplikacji. W poniższym poleceniu Zastąp nazwę zestawu aplikacji dla \<assembly_name> : `<assembly_name>.exe` .
1. Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.
1. Jeśli wystąpią błędy podczas wysyłania żądania do aplikacji, należy wysłać żądanie do hosta i portu, gdzie Kestrel nasłuchuje. Przy użyciu domyślnego hosta i wpisu Wyślij żądanie do `http://localhost:5000/` . Jeśli aplikacja będzie odpowiadać zwykle na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobnie związany z konfiguracją hostingu i mniej korzystnie w aplikacji.

### <a name="aspnet-core-module-stdout-log-iis"></a>Dziennik stdout modułu ASP.NET Core (IIS)

Aby włączyć i wyświetlić dzienniki stdout:

1. Przejdź do folderu wdrożenia lokacji w systemie hostingu.
1. Jeśli folder *Logs* nie istnieje, utwórz go. Aby uzyskać instrukcje dotyczące włączania programu MSBuild do automatycznego tworzenia folderu *Logs* w ramach wdrożenia, zobacz temat [Struktura katalogów](xref:host-and-deploy/directory-structure) .
1. Edytuj plik *web.config* . Ustaw wartość **stdoutLogEnabled** na `true` i zmień ścieżkę **stdoutLogFile** , tak aby wskazywała folder *Logs* (na przykład `.\logs\stdout` ). `stdout` ścieżka jest prefiksem nazwy pliku dziennika. Sygnatura czasowa, identyfikator procesu i rozszerzenie pliku są dodawane automatycznie podczas tworzenia dziennika. Używając `stdout` jako prefiksu nazwy pliku, typowy plik dziennika ma nazwę *stdout_20180205184032_5412. log*.
1. Upewnij się, że tożsamość puli aplikacji ma uprawnienia do zapisu w folderze *Logs* .
1. Zapisz zaktualizowany plik *web.config* .
1. Utwórz żądanie do aplikacji.
1. Przejdź do folderu *Logs* . Znajdź i Otwórz najnowszy dziennik stdout.
1. Zbadaj dziennik pod kątem błędów.

Wyłącz rejestrowanie stdout po zakończeniu rozwiązywania problemów:

1. Edytuj plik *web.config* .
1. Ustaw wartość **stdoutLogEnabled** na `false` .
1. Zapisz plik.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma żadnego limitu rozmiaru pliku dziennika lub liczby utworzonych plików dziennika.
>
> Aby rejestrować procedury w aplikacji ASP.NET Core, użyj biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych](xref:fundamentals/logging/index#third-party-logging-providers)firm.

### <a name="enable-the-developer-exception-page"></a>Włącz stronę wyjątków dla deweloperów

`ASPNETCORE_ENVIRONMENT` [Zmienną środowiskową można dodać do web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) , aby uruchomić aplikację w środowisku deweloperskim. O ile środowisko nie zostanie zastąpione podczas uruchamiania aplikacji przez `UseEnvironment` konstruktora hosta, ustawienie zmiennej środowiskowej umożliwia wyświetlenie [strony wyjątku dewelopera](xref:fundamentals/error-handling) po uruchomieniu aplikacji.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

Ustawienie zmiennej środowiskowej dla programu `ASPNETCORE_ENVIRONMENT` jest zalecane wyłącznie do użytku na przejściowych i testowych serwerach, które nie są dostępne w Internecie. Usuń zmienną środowiskową z pliku *web.config* po rozwiązaniu problemu. Aby uzyskać informacje na temat ustawiania zmiennych środowiskowych w *web.config*, zobacz [EnvironmentVariables podrzędny element aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Uzyskiwanie danych z aplikacji

Jeśli aplikacja może odpowiadać na żądania, uzyskiwać żądania, połączenia i dodatkowe dane z aplikacji przy użyciu wbudowanego oprogramowania terminala. Aby uzyskać więcej informacji i przykładowy kod, zobacz <xref:test/troubleshoot#obtain-data-from-an-app> .

### <a name="slow-or-hanging-app-iis"></a>Aplikacja wolna lub wysunięta (IIS)

*Zrzut awaryjny* to migawka pamięci systemu, która może pomóc w ustaleniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.

#### <a name="app-crashes-or-encounters-an-exception"></a>Awaria aplikacji lub napotka wyjątek

Uzyskaj i Analizuj Zrzut z [raportowanie błędów systemu Windows (raportowanie błędów systemu Windows)](/windows/desktop/wer/windows-error-reporting):

1. Utwórz folder do przechowywania plików zrzutu awaryjnego w `c:\dumps` . Pula aplikacji musi mieć dostęp do zapisu w folderze.
1. Uruchom [skrypt programu PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)w programie EnableDumps:
   * Jeśli aplikacja korzysta z [modelu hostingu w procesie](xref:host-and-deploy/iis/index#in-process-hosting-model), uruchom skrypt dla *w3wp.exe*:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Jeśli aplikacja korzysta z [modelu hostingu poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), uruchom skrypt dla *dotnet.exe*:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Uruchom aplikację w warunkach, które powodują awarię.
1. Po wystąpieniu awarii Uruchom [skrypt programu DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):
   * Jeśli aplikacja korzysta z [modelu hostingu w procesie](xref:host-and-deploy/iis/index#in-process-hosting-model), uruchom skrypt dla *w3wp.exe*:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Jeśli aplikacja korzysta z [modelu hostingu poza procesem](xref:host-and-deploy/iis/index#out-of-process-hosting-model), uruchom skrypt dla *dotnet.exe*:

     ```console
     .\DisableDumps dotnet.exe
     ```

Po awarii aplikacji i zakończeniu zbierania zrzutów aplikacja może zakończyć normalne działanie. Skrypt programu PowerShell konfiguruje raportowanie błędów systemu Windows w celu zebrania do pięciu zrzutów na aplikację.

> [!WARNING]
> Zrzuty awaryjne mogą wymagać dużej ilości miejsca na dysku (do kilku gigabajtów).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie

Gdy aplikacja *zawiesza* się (bez awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [pliki zrzutu w trybie użytkownika: wybór najlepszego narzędzia](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) do wygenerowania zrzutu.

#### <a name="analyze-the-dump"></a>Analizowanie zrzutu

Zrzut można analizować przy użyciu kilku metod. Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu User-Mode](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="clear-package-caches"></a>Wyczyść pamięć podręczną pakietów

Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji. W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych. Większość tych problemów można naprawić, wykonując następujące instrukcje:

1. Usuń foldery *bin* i *obj* .
1. Wyczyść pamięć podręczną pakietów, wykonując [wszystkie elementy lokalne usługi NuGet programu dotnet--Wyczyść](/dotnet/core/tools/dotnet-nuget-locals) z poziomu powłoki poleceń.

   Czyszczenie pamięci podręcznych pakietów można także wykonać przy użyciu narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonując polecenie `nuget locals all -clear` . *nuget.exe* nie jest instalacją w pakiecie z systemem operacyjnym Windows dla komputerów stacjonarnych i należy ją pobrać niezależnie od [witryny sieci Web programu NuGet](https://www.nuget.org/downloads).

1. Przywróć i skompiluj projekt.
1. Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Dokumentacja platformy Azure

* [Usługa Application Insights dla platformy ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Sekcja zdalne debugowanie aplikacji sieci Web Rozwiązywanie problemów z aplikacją sieci Web w Azure App Service przy użyciu programu Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Omówienie diagnostyki Azure App Service](/azure/app-service/app-service-diagnostics)
* [Jak monitorować aplikacje w usłudze Azure App Service](/azure/app-service/web-sites-monitor)
* [Rozwiązywanie problemów z aplikacją sieci Web w Azure App Service przy użyciu programu Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Rozwiązywanie problemów z błędami HTTP "502 złej Gateway" i "503 Usługa niedostępna" w usłudze Azure Web Apps](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Często zadawane pytania dotyczące wydajności aplikacji dla Web Apps na platformie Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Piaskownica usługi Azure Web App (ograniczenia wykonywania App Service Runtime)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Piątek Azure: Azure App Service środowisko diagnostyczne i rozwiązywania problemów (wideo 12-minutowy)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Dokumentacja programu Visual Studio

* [ASP.NET Core debugowania zdalnego na serwerze IIS na platformie Azure w programie Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure)
* [Zdalne debugowanie ASP.NET Core na zdalnym komputerze IIS w programie Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Nauka debugowania przy użyciu programu Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Dokumentacja Visual Studio Code

* [Debugowanie za pomocą Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
