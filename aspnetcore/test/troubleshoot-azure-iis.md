---
title: Rozwiązywanie problemów z ASP.NET Core w usłudze Azure App Service i usługach IIS
author: rick-anderson
description: Dowiedz się, jak zdiagnozować problemy z usługą Azure App Service i wdrożeniami internetowych usług informacyjnych (IIS) aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: f994cd1274bda9082a7cd8b637968b2769db1671
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661710"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a>Rozwiązywanie problemów z ASP.NET Core w usłudze Azure App Service i usługach IIS

Przez [Justin Kotalik](https://github.com/jkotalik)

::: moniker range=">= aspnetcore-3.0"

Ten artykuł zawiera informacje na temat typowych błędów uruchamiania aplikacji i instrukcje dotyczące diagnozowania błędów, gdy aplikacja jest wdrażana w usłudze Azure App Service lub usługach IIS:

[Błędy uruchamiania aplikacji](#app-startup-errors)  
W tym artykule wyjaśniono typowe scenariusze kodu stanu HTTP podczas uruchamiania.

[Rozwiązywanie problemów w usłudze Azure App Service](#troubleshoot-on-azure-app-service)  
Zawiera porady dotyczące rozwiązywania problemów dla aplikacji wdrożonych w usłudze Azure App Service.

[Rozwiązywanie problemów w usługach IIS](#troubleshoot-on-iis)  
Zawiera porady dotyczące rozwiązywania problemów dla aplikacji wdrożonych w serwisach IIS lub działających na usługach IIS Express lokalnie. Wskazówki dotyczą zarówno wdrożeń pulpitu w systemie Windows Server, jak i windows.

[Wyczyść pamięć podręczną pakietów](#clear-package-caches)  
Wyjaśniono, co zrobić, gdy niespójne pakiety przerwać aplikację podczas wykonywania głównych uaktualnień lub zmiany wersji pakietu.

[Dodatkowe zasoby](#additional-resources)  
Wyświetla listę dodatkowych tematów rozwiązywania problemów.

## <a name="app-startup-errors"></a>Błędy uruchamiania aplikacji

W programie Visual Studio projekt ASP.NET Core domyślnie ma hosting [usługi IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) podczas debugowania. A *502.5 - Błąd procesu* lub *500.30 - Uruchom błąd,* który występuje podczas debugowania lokalnie można zdiagnozować za pomocą porady w tym temacie.

### <a name="40314-forbidden"></a>403.14 Zabronione

Uruchomienie aplikacji nie powiedzie się. Rejestrowany jest następujący błąd:

```
The Web server is configured to not list the contents of this directory.
```

Błąd jest zwykle spowodowany przez przerwane wdrożenie w systemie hostingowym, który zawiera dowolny z następujących scenariuszy:

* Aplikacja jest wdrażana w niewłaściwym folderze w systemie hostingowym.
* Proces wdrażania nie może przenieść wszystkich plików i folderów aplikacji do folderu wdrażania w systemie hostingowym.
* Brak pliku *web.config* we wdrożeniu lub zawartość pliku *web.config* jest zniekształcona.

Wykonaj poniższe czynności:

1. Usuń wszystkie pliki i foldery z folderu wdrażania w systemie hostingowym.
1. Ponownie rozmieszczaj zawartość folderu *publikowania* aplikacji do systemu hostingowego przy użyciu normalnej metody wdrażania, takiej jak Visual Studio, PowerShell lub wdrożenie ręczne:
   * Upewnij się, że plik *web.config* jest obecny we wdrożeniu i że jego zawartość jest poprawna.
   * Podczas hostingu w usłudze Azure App Service `D:\home\site\wwwroot` upewnij się, że aplikacja została wdrożona w folderze.
   * Gdy aplikacja jest obsługiwana przez usługe IIS, upewnij się, że aplikacja została wdrożona na **ścieżce fizycznej** usług IIS wyświetlanej w **podstawowych ustawieniach menedżera** **usług IIS**.
1. Upewnij się, że wszystkie pliki i foldery aplikacji są wdrażane, porównując wdrożenie w systemie hostingowym z zawartością folderu *publikowania* projektu.

Aby uzyskać więcej informacji na temat układu opublikowanej aplikacji <xref:host-and-deploy/directory-structure>ASP.NET Core, zobacz . Aby uzyskać więcej informacji na temat <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>pliku *web.config,* zobacz .

### <a name="500-internal-server-error"></a>500 — wewnętrzny błąd serwera

Aplikacja uruchamia się, ale błąd uniemożliwia serwerowi spełnienie żądania.

Ten błąd występuje w kodzie aplikacji podczas uruchamiania lub podczas tworzenia odpowiedzi. Odpowiedź może nie zawierać zawartości lub odpowiedź może pojawić się jako *wewnętrzny błąd serwera 500* w przeglądarce. Dziennik zdarzeń aplikacji zwykle stwierdza, że aplikacja została uruchomiona normalnie. Z punktu widzenia serwera jest to poprawne. Aplikacja została uruchomiony, ale nie można wygenerować prawidłową odpowiedź. Uruchom aplikację w wierszu polecenia na serwerze lub włącz ASP.NET dziennik stdout modułu rdzenia, aby rozwiązać problem.

### <a name="5000-in-process-handler-load-failure"></a>500.0 Awaria obciążenia obsługi w trakcie

Proces roboczy kończy się niepowodzeniem. Aplikacja nie uruchamia się.

Wystąpił nieznany błąd podczas ładowania ASP.NET składników [modułu rdzenia.](xref:host-and-deploy/aspnet-core-module) Wykonaj jedno z następujących działań:

* Skontaktuj się z [pomocą techniczną firmy Microsoft](https://support.microsoft.com/oas/default.aspx?prid=15832) (wybierz pozycję Narzędzia **deweloperskie,** a następnie **ASP.NET Core).**
* Zadaj pytanie w sprawie Przepełnienie stosu.
* Zgładzenie problemu w naszym [repozytorium GitHub](https://github.com/dotnet/AspNetCore).

### <a name="50030-in-process-startup-failure"></a>500.30 Niepowodzenie uruchamiania w trakcie

Proces roboczy kończy się niepowodzeniem. Aplikacja nie uruchamia się.

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) próbuje uruchomić program .NET Core CLR w procesie, ale nie można go uruchomić. Przyczynę niepowodzenia uruchamiania procesu można zwykle określić na podstawie wpisów w dzienniku zdarzeń aplikacji i ASP.NET dzienniku stdout modułu rdzenia.

Typowe warunki awarii:

* Aplikacja jest nieprawidłowo skonfigurowana ze względu na kierowanie na wersję udostępnionej struktury ASP.NET Core, która nie jest obecna. Sprawdź, które wersje udostępnionej struktury ASP.NET Core są zainstalowane na komputerze docelowym.
* Korzystanie z usługi Azure Key Vault, brak uprawnień do magazynu kluczy. Sprawdź zasady dostępu w docelowym magazynie kluczy, aby upewnić się, że są przyznawane poprawne uprawnienia.

### <a name="50031-ancm-failed-to-find-native-dependencies"></a>500.31 ANCM nie można znaleźć natywnych zależności

Proces roboczy kończy się niepowodzeniem. Aplikacja nie uruchamia się.

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) próbuje uruchomić środowisko uruchomieniowe .NET Core w procesie, ale nie można go uruchomić. Najczęstszą przyczyną tego błędu uruchamiania `Microsoft.NETCore.App` `Microsoft.AspNetCore.App` jest, gdy lub środowiska wykonawczego nie jest zainstalowany. Jeśli aplikacja jest wdrażana w celu docelowego ASP.NET Core 3.0 i tej wersji nie istnieje na komputerze, ten błąd występuje. Przykładowy komunikat o błędzie jest następujący:

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

Komunikat o błędzie zawiera listę wszystkich zainstalowanych wersji .NET Core i wersji żądanej przez aplikację. Aby naprawić ten błąd,:

* Zainstaluj odpowiednią wersję programu .NET Core na komputerze.
* Zmień aplikację, aby kierować na wersję .NET Core, która jest obecna na komputerze.
* Opublikuj aplikację jako [samodzielne wdrożenie](/dotnet/core/deploying/#self-contained-deployments-scd).

Podczas pracy w `ASPNETCORE_ENVIRONMENT` rozwoju (zmienna `Development`środowiskowa jest ustawiona na), określony błąd jest zapisywany do odpowiedzi HTTP. Przyczynę niepowodzenia uruchamiania procesu znajduje się również w dzienniku zdarzeń aplikacji.

### <a name="50032-ancm-failed-to-load-dll"></a>500.32 ANCM Nie można załadować biblioteki DLL

Proces roboczy kończy się niepowodzeniem. Aplikacja nie uruchamia się.

Najczęstszą przyczyną tego błędu jest to, że aplikacja jest publikowana dla niezgodnej architektury procesora. Jeśli proces roboczy jest uruchomiony jako aplikacja 32-bitowa, a aplikacja została opublikowana w celu docelowego 64-bitowego, ten błąd występuje.

Aby naprawić ten błąd,:

* Ponownie opublikuj aplikację dla tej samej architektury procesora co proces roboczy.
* Opublikuj aplikację jako [wdrożenie zależne od struktury](/dotnet/core/deploying/#framework-dependent-executables-fde).

### <a name="50033-ancm-request-handler-load-failure"></a>500.33 Błąd obsługi obsługi żądań ANCM

Proces roboczy kończy się niepowodzeniem. Aplikacja nie uruchamia się.

Aplikacja nie odwoływała `Microsoft.AspNetCore.App` się do struktury. Tylko aplikacje `Microsoft.AspNetCore.App` przeznaczone dla tej struktury mogą być hostowane przez [moduł ASP.NET Core.](xref:host-and-deploy/aspnet-core-module)

Aby naprawić ten błąd, upewnij się, `Microsoft.AspNetCore.App` że aplikacja jest kierowana na platformę. Sprawdź, `.runtimeconfig.json` aby zweryfikować strukturę, do którego dąży aplikacja.

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a>500.34 AnCM Mieszane modele hostingowe nie są obsługiwane

Proces roboczy nie może uruchomić zarówno aplikacji w toku, jak i aplikacji poza procesem w tym samym procesie.

Aby naprawić ten błąd, uruchom aplikacje w oddzielnych pulach aplikacji usług IIS.

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a>500.35 ANCM wiele aplikacji w trakcie procesu w tym samym procesie

Proces roboczy nie może uruchomić wielu aplikacji w procesie w tym samym procesie.

Aby naprawić ten błąd, uruchom aplikacje w oddzielnych pulach aplikacji usług IIS.

### <a name="50036-ancm-out-of-process-handler-load-failure"></a>500.36 ANCM Awaria obciążenia obsługi poza procesem

Program obsługi żądań poza procesem, *aspnetcorev2_outofprocess.dll*, nie znajduje się obok pliku *aspnetcorev2.dll.* Oznacza to uszkodzoną instalację [modułu ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).

Aby naprawić ten błąd, napraw instalację [pakietu hostingowego .NET Core (dla](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) usług IIS) lub programu Visual Studio (dla usług IIS Express).

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a>500.37 ANCM nie można uruchomić w terminie uruchamiania

Nie można uruchomić w provied termin uruchamiania. Domyślnie limit czasu wynosi 120 sekund.

Ten błąd może wystąpić podczas uruchamiania dużej liczby aplikacji na tym samym komputerze. Sprawdź, czy użycie procesora CPU/pamięci wzrasta na serwerze podczas uruchamiania. Może być konieczne rozłożenie procesu uruchamiania wielu aplikacji.

### <a name="50038-ancm-application-dll-not-found"></a>Nie znaleziono biblioteki DLL aplikacji 500.38 ANCM

Ancm nie można zlokalizować biblioteki DLL aplikacji, która powinna być obok pliku wykonywalnego.

Ten błąd występuje podczas hostowania aplikacji spakowanej jako [plik wykonywalny z jednym plikiem](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) przy użyciu modelu hostingu w trakcie. Model w procesie wymaga, aby ancm załadować aplikację .NET Core do istniejącego procesu IIS. Ten scenariusz nie jest obsługiwany przez model wdrażania z jednym plikiem. Aby naprawić ten błąd, użyj **jednego** z następujących podejść w pliku projektu aplikacji:

1. Wyłącz publikowanie pojedynczego `PublishSingleFile` pliku, ustawiając `false`właściwość MSBuild na .
1. Przełącz się do modelu hostingu poza `AspNetCoreHostingModel` procesem, ustawiając `OutOfProcess`właściwość MSBuild na .

### <a name="5025-process-failure"></a>502.5 Błąd procesu

Proces roboczy kończy się niepowodzeniem. Aplikacja nie uruchamia się.

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) próbuje uruchomić proces roboczy, ale nie można go uruchomić. Przyczynę niepowodzenia uruchamiania procesu można zwykle określić na podstawie wpisów w dzienniku zdarzeń aplikacji i ASP.NET dzienniku stdout modułu rdzenia.

Typowym warunkiem awarii jest aplikacja jest nieprawidłowo skonfigurowany ze względu na kierowanie na wersję ASP.NET Core udostępnionej struktury, która nie jest obecny. Sprawdź, które wersje udostępnionej struktury ASP.NET Core są zainstalowane na komputerze docelowym. *Współużytkowana struktura* to zestaw zestawów (plików*dll),* które są zainstalowane na komputerze `Microsoft.AspNetCore.App`i odwołują się do metapakiety, takie jak . Odwołanie do metapakiety można określić minimalną wymaganą wersję. Aby uzyskać więcej informacji, zobacz [Współużytkowane struktury](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Strona błędu *procesu 502.5* jest zwracana, gdy nieprawidłowa konfiguracja hostingu lub aplikacji powoduje niepowodzenie procesu roboczego:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Nie można uruchomić aplikacji (Kod błędu '0x800700c1')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Nie można uruchomić aplikacji, ponieważ nie można załadować zestawu aplikacji (*dll).*

Ten błąd występuje, gdy istnieje niezgodność bitness między opublikowanej aplikacji i w3wp/iisexpress procesu.

Upewnij się, że 32-bitowe ustawienie puli aplikacji jest poprawne:

1. Wybierz pulę aplikacji w **pulach aplikacji**menedżera usług IIS .
1. Wybierz pozycję **Ustawienia zaawansowane** w obszarze **Edytowanie puli aplikacji** w panelu **Akcje.**
1. Ustawianie **włączania aplikacji 32-bitowych:**
   * Jeśli wdrażasz aplikację 32-bitową (x86), `True`ustaw wartość .
   * Jeśli wdrażasz 64-bitową (x64) aplikację, `False`ustaw wartość .

Upewnij się, że nie ma `<Platform>` konfliktu między właściwością MSBuild w pliku projektu a opublikowaną bitowością aplikacji.

### <a name="connection-reset"></a>Resetowanie połączenia

Jeśli po wysłaniu nagłówków wystąpi błąd, jest za późno, aby serwer wysłał **błąd serwera wewnętrznego 500,** gdy wystąpi błąd. Dzieje się tak często, gdy wystąpi błąd podczas serializacji złożonych obiektów dla odpowiedzi. Ten typ błędu pojawia się jako błąd *resetowania połączenia* na kliencie. [Rejestrowanie aplikacji](xref:fundamentals/logging/index) może pomóc w rozwiązywaniu problemów z tego typu błędami.

### <a name="default-startup-limits"></a>Domyślne limity uruchamiania

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) jest skonfigurowany z domyślnym *startupTimeLimit* 120 sekund. Po lewej stronie na wartość domyślną, aplikacja może potrwać do dwóch minut, aby rozpocząć przed moduł rejestruje błąd procesu. Aby uzyskać informacje na temat konfigurowania modułu, zobacz [Atrybuty elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Rozwiązywanie problemów w usłudze Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Dziennik zdarzeń aplikacji (usługa Azure App Service)

Aby uzyskać dostęp do dziennika zdarzeń aplikacji, należy użyć **diagnozowania i rozwiązywania problemów** bloku w witrynie Azure portal:

1. W witrynie Azure portal otwórz aplikację w **usługach App Services**.
1. Kliknij pozycję **Diagnozowanie i rozwiązywanie problemów**.
1. Wybierz nagłówek **Narzędzia diagnostyczne.**
1. W obszarze **Narzędzia pomocnicze**wybierz przycisk **Zdarzenia aplikacji.**
1. Sprawdź ostatni błąd dostarczony przez *wpis IIS AspNetCoreModule* lub *IIS AspNetCoreModule V2* w kolumnie **Źródło.**

Alternatywą dla korzystania **z diagnozowania i rozwiązywania problemów** bloku jest sprawdzenie pliku dziennika zdarzeń aplikacji bezpośrednio za pomocą [Kudu:](https://github.com/projectkudu/kudu/wiki)

1. Otwórz **zaawansowane narzędzia** w obszarze Narzędzia **programistyczne.** Wybierz przycisk **Przejdź.&rarr; ** Konsola Kudu otwiera się w nowej karcie przeglądarki lub oknie.
1. Korzystając z paska nawigacyjnego u góry strony, otwórz **konsolę debugowania** i wybierz **cmd**.
1. Otwórz folder **LogFiles.**
1. Wybierz ikonę ołówka obok pliku *eventlog.xml.*
1. Sprawdź dziennik. Przewiń do dołu dziennika, aby zobaczyć najnowsze zdarzenia.

### <a name="run-the-app-in-the-kudu-console"></a>Uruchamianie aplikacji w konsoli Kudu

Wiele błędów uruchamiania nie generuje przydatnych informacji w dzienniku zdarzeń aplikacji. Aplikację można uruchomić w konsoli zdalnego wykonania [Kudu,](https://github.com/projectkudu/kudu/wiki) aby wykryć błąd:

1. Otwórz **zaawansowane narzędzia** w obszarze Narzędzia **programistyczne.** Wybierz przycisk **Przejdź.&rarr; ** Konsola Kudu otwiera się w nowej karcie przeglądarki lub oknie.
1. Korzystając z paska nawigacyjnego u góry strony, otwórz **konsolę debugowania** i wybierz **cmd**.

#### <a name="test-a-32-bit-x86-app"></a>Testowanie aplikacji 32-bitowej (x86)

**Bieżące wydanie**

1. `cd d:\home\site\wwwroot`
1. Uruchom aplikację:
   * Jeśli aplikacja jest [wdrożeniem zależnym od struktury:](/dotnet/core/deploying/#framework-dependent-deployments-fdd)

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Jeśli aplikacja jest [samodzielnym wdrożeniem:](/dotnet/core/deploying/#self-contained-deployments-scd)

     ```console
     {ASSEMBLY NAME}.exe
     ```

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

**Wdrożenie zależne od struktury uruchomione w wersji zapoznawczej**

*Wymaga zainstalowania rozszerzenia witryny ASP.NET Core {VERSION} (x86).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(jest`{X.Y}` wersją środowiska wykonawczego)
1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Testowanie aplikacji 64-bitowej (x64)

**Bieżące wydanie**

* Jeśli aplikacja jest 64-bitowym (x64) [wdrożeniem zależnym od struktury:](/dotnet/core/deploying/#framework-dependent-deployments-fdd)
  1. `cd D:\Program Files\dotnet`
  1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Jeśli aplikacja jest [samodzielnym wdrożeniem:](/dotnet/core/deploying/#self-contained-deployments-scd)
  1. `cd D:\home\site\wwwroot`
  1. Uruchom aplikację: `{ASSEMBLY NAME}.exe`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

**Wdrożenie zależne od struktury uruchomione w wersji zapoznawczej**

*Wymaga zainstalowania rozszerzenia witryny ASP.NET Core {VERSION} (x64).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(jest`{X.Y}` wersją środowiska wykonawczego)
1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>ASP.NET dziennik stdout modułu rdzenia (usługa Azure App Service)

Dziennik stdout modułu ASP.NET core często rejestruje przydatne komunikaty o błędach, które nie zostały znalezione w dzienniku zdarzeń aplikacji. Aby włączyć i wyświetlić dzienniki stdout:

1. Przejdź do **bloku Diagnozowanie i rozwiązywanie problemów** w witrynie Azure portal.
1. W **obszarze WYBIERZ KATEGORIĘ PROBLEM**wybierz przycisk W dół aplikacji sieci **Web.**
1. W obszarze **Sugerowane** > rozwiązania **Włącz przekierowanie dziennika stdout**wybierz przycisk **Otwórz konsolę Kudu, aby edytować web.config**.
1. W **Konsoli diagnostycznej**Kudu otwórz foldery do **strony** > ścieżki**wwwroot**. Przewiń w dół, aby wyświetlić plik *web.config* u dołu listy.
1. Kliknij ikonę ołówka obok pliku *web.config.*
1. Ustaw **stdoutLogEnabled** i `true` zmień ścieżkę **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.
1. Wybierz **pozycję Zapisz,** aby zapisać zaktualizowany plik *web.config.*
1. Złożyć wniosek do aplikacji.
1. Wróć do witryny Azure Portal. Wybierz ostrze **Narzędzia zaawansowane** w obszarze **NARZĘDZIA PROGRAMISTYCZNE.** Wybierz przycisk **Przejdź.&rarr; ** Konsola Kudu otwiera się w nowej karcie przeglądarki lub oknie.
1. Korzystając z paska nawigacyjnego u góry strony, otwórz **konsolę debugowania** i wybierz **cmd**.
1. Wybierz folder **LogFiles.**
1. Sprawdź kolumnę **Zmodyfikowane** i wybierz ikonę ołówka, aby edytować dziennik stdout z najpóźniejszą datą modyfikacji.
1. Po otwarciu pliku dziennika zostanie wyświetlony błąd.

Wyłącz rejestrowanie stdout po zakończeniu rozwiązywania problemów:

1. W **Konsoli Diagnostycznej**Kudu wróć do **strony** > ścieżki**wwwroot,** aby odsłonić plik *web.config.* Ponownie otwórz plik **web.config,** wybierając ikonę ołówka.
1. Ustaw **stdoutLogEnabled** na `false`.
1. Wybierz **pozycję Zapisz,** aby zapisać plik.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma limitu rozmiaru pliku dziennika ani liczby utworzonych plików dziennika. Rejestrowanie stdout umożliwia rozwiązywanie problemów z uruchamianiem aplikacji.
>
> Aby ogólne rejestrowanie w aplikacji core ASP.NET po uruchomieniu, należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>dziennik debugowania ASP.NET core module (usługa Azure App Service)

Dziennik debugowania modułu ASP.NET core zapewnia dodatkowe, głębsze rejestrowanie z modułu ASP.NET Core. Aby włączyć i wyświetlić dzienniki stdout:

1. Aby włączyć rozszerzony dziennik diagnostyczny, wykonaj jedną z następujących czynności:
   * Postępuj zgodnie z instrukcjami w [rozszerzonych dzienników diagnostycznych,](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) aby skonfigurować aplikację do rozszerzonego rejestrowania diagnostycznego. Ponowne wdrożenie aplikacji.
   * Dodaj `<handlerSettings>` pokazane w [rozszerzonych dziennikach diagnostycznych](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do pliku *web.config* aplikacji na żywo przy użyciu konsoli Kudu:
     1. Otwórz **zaawansowane narzędzia** w obszarze Narzędzia **programistyczne.** Wybierz przycisk **Przejdź.&rarr; ** Konsola Kudu otwiera się w nowej karcie przeglądarki lub oknie.
     1. Korzystając z paska nawigacyjnego u góry strony, otwórz **konsolę debugowania** i wybierz **cmd**.
     1. Otwórz foldery do **witryny** > ścieżki**wwwroot**. Edytuj plik *web.config,* wybierając przycisk ołówka. Dodaj `<handlerSettings>` sekcję, jak pokazano w [rozszerzonych dziennikach diagnostycznych](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Wybierz ikonę **Zapisz**.
1. Otwórz **zaawansowane narzędzia** w obszarze Narzędzia **programistyczne.** Wybierz przycisk **Przejdź.&rarr; ** Konsola Kudu otwiera się w nowej karcie przeglądarki lub oknie.
1. Korzystając z paska nawigacyjnego u góry strony, otwórz **konsolę debugowania** i wybierz **cmd**.
1. Otwórz foldery do **witryny** > ścieżki**wwwroot**. Jeśli nie podasz ścieżki dla pliku *aspnetcore-debug.log,* plik pojawi się na liście. Jeśli podano ścieżkę, przejdź do lokalizacji pliku dziennika.
1. Otwórz plik dziennika za pomocą przycisku ołówka obok nazwy pliku.

Wyłącz rejestrowanie debugowania po zakończeniu rozwiązywania problemów:

Aby wyłączyć rozszerzony dziennik debugowania, wykonaj jedną z następujących czynności:

* Usuń `<handlerSettings>` plik *web.config* lokalnie i ponownie wdrożyć aplikację.
* Użyj konsoli Kudu, aby edytować plik *web.config* i usunąć sekcję. `<handlerSettings>` Zapisz plik.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> Niepowodzenie wyłączenia dziennika debugowania może prowadzić do awarii aplikacji lub serwera. Nie ma limitu rozmiaru pliku dziennika. Rejestrowania debugowania należy używać tylko do rozwiązywania problemów z uruchamianiem aplikacji.
>
> Aby ogólne rejestrowanie w aplikacji core ASP.NET po uruchomieniu, należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Powolna lub wisząca aplikacja (usługa Azure App Service)

Gdy aplikacja odpowiada powoli lub zawiesza się na żądanie, zobacz następujące artykuły:

* [Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Używanie rozszerzenia witryny diagnostyki awarii do przechwytywania zrzutu w przypadku sporadyczne problemy z wyjątkami lub problemy z wydajnością w aplikacji Sieci Web platformy Azure](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>Ostrza monitorujące

Bloky monitorowania zapewniają alternatywne środowisko rozwiązywania problemów z metodami opisanymi wcześniej w temacie. Te ostrza mogą być używane do diagnozowania błędów serii 500.

Upewnij się, że są zainstalowane rozszerzenia ASP.NET Core. Jeśli rozszerzenia nie są zainstalowane, zainstaluj je ręcznie:

1. W sekcji bloku **NARZĘDZIA ROZWOJU** wybierz ostrze **Rozszerzenia.**
1. Rozszerzenia **ASP.NET core** powinny pojawić się na liście.
1. Jeśli rozszerzenia nie są zainstalowane, wybierz przycisk **Dodaj.**
1. Wybierz z listy **ASP.NET rozszerzenia rdzenia.**
1. Wybierz **przycisk OK,** aby zaakceptować warunki prawne.
1. Wybierz **przycisk OK** na bloku Dodaj **rozszerzenie.**
1. Komunikat wyskakującym dla informacji wskazuje, kiedy rozszerzenia zostały pomyślnie zainstalowane.

Jeśli rejestrowanie stdout nie jest włączone, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz blok **Narzędzia zaawansowane** w obszarze **NARZĘDZIA PROGRAMISTYCZNE.** Wybierz przycisk **Przejdź.&rarr; ** Konsola Kudu otwiera się w nowej karcie przeglądarki lub oknie.
1. Korzystając z paska nawigacyjnego u góry strony, otwórz **konsolę debugowania** i wybierz **cmd**.
1. Otwórz foldery do **witryny** > ścieżki **wwwroot** i przewiń w dół, aby odsłonić plik *web.config* u dołu listy.
1. Kliknij ikonę ołówka obok pliku *web.config.*
1. Ustaw **stdoutLogEnabled** i `true` zmień ścieżkę **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.
1. Wybierz **pozycję Zapisz,** aby zapisać zaktualizowany plik *web.config.*

Przejdź do aktywacji rejestrowania diagnostycznego:

1. W witrynie Azure portal wybierz blok **dzienników diagnostyki.**
1. Wybierz przełącznik **On** dla **rejestrowania aplikacji (System plików)** i **Szczegółowe komunikaty o błędach**. Wybierz przycisk **Zapisz** u góry bloku.
1. Aby uwzględnić śledzenie żądań nie powiodło się, znane również jako rejestrowanie buforowania zdarzeń żądania niepowodzenia (FREB), wybierz przełącznik **Włącz** dla **śledzenia żądań nie powiodło się**.
1. Wybierz **blok strumienia dziennika,** który jest wyświetlany natychmiast w **obszarze diagnostyki dzienniki** bloku w portalu.
1. Złożyć wniosek do aplikacji.
1. W danych strumienia dziennika jest wskazana przyczyna błędu.

Pamiętaj, aby wyłączyć rejestrowanie stdout po zakończeniu rozwiązywania problemów.

Aby wyświetlić dzienniki śledzenia żądań, które nie powiodły się (dzienniki FREB):

1. Przejdź do **bloku Diagnozowanie i rozwiązywanie problemów** w witrynie Azure portal.
1. Wybierz **dzienniki śledzenia żądań nie powiodło się** z obszaru **NARZĘDZIA POMOCNICZE** paska bocznego.

Zobacz [śledzenia żądań nie powiodło się sekcji Włącz rejestrowanie diagnostyki dla aplikacji sieci web w usłudze Azure App Service temat](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) i często zadawane pytania dotyczące wydajności aplikacji dla aplikacji sieci Web na [platformie Azure: Jak włączyć śledzenie żądań nie powiodło się?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)

Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania diagnostyki dla aplikacji sieci Web w usłudze Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma limitu rozmiaru pliku dziennika ani liczby utworzonych plików dziennika.
>
> W przypadku rutynowego rejestrowania w aplikacji ASP.NET Core należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>Rozwiązywanie problemów w usługach IIS

### <a name="application-event-log-iis"></a>Dziennik zdarzeń aplikacji (IIS)

Dostęp do dziennika zdarzeń aplikacji:

1. Otwórz menu Start, wyszukaj *podgląd zdarzeń*i wybierz aplikację **Podgląd zdarzeń.**
1. W **Podglądzie zdarzeń**otwórz węzeł **Dzienniki systemu Windows.**
1. Wybierz **opcję Aplikacja,** aby otworzyć dziennik zdarzeń aplikacji.
1. Wyszukaj błędy skojarzone z nieudaną aplikacją. Błędy mają wartość *modułu AspNetCore usługi IIS* lub *modułu AspNetCore usługi IIS Express* w kolumnie *Źródło.*

### <a name="run-the-app-at-a-command-prompt"></a>Uruchamianie aplikacji w wierszu polecenia

Wiele błędów uruchamiania nie generuje przydatnych informacji w dzienniku zdarzeń aplikacji. Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingowym.

#### <a name="framework-dependent-deployment"></a>Wdrożenie zależne od struktury

Jeśli aplikacja jest [wdrożeniem zależnym od struktury:](/dotnet/core/deploying/#framework-dependent-deployments-fdd)

1. W wierszu polecenia przejdź do folderu wdrażania i uruchom aplikację, wykonując zestaw aplikacji za pomocą *pliku dotnet.exe*. W poniższym poleceniu zastąp nazwę \<zestawu aplikacji assembly_name `dotnet .\<assembly_name>.dll`>: .
1. Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.
1. Jeśli wystąpią błędy podczas tworzenia żądania do aplikacji, zgłoś żądanie do hosta i portu, w którym nasłuchuje Kestrel. Korzystając z domyślnego hosta i `http://localhost:5000/`księguj, zgłać żądanie do pliku . Jeśli aplikacja odpowiada normalnie na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobne związane z konfiguracją hostingu i mniej prawdopodobne w aplikacji.

#### <a name="self-contained-deployment"></a>Samodzielne wdrożenie

Jeśli aplikacja jest [samodzielnym wdrożeniem:](/dotnet/core/deploying/#self-contained-deployments-scd)

1. W wierszu polecenia przejdź do folderu wdrażania i uruchom plik wykonywalny aplikacji. W poniższym poleceniu zastąp nazwę \<zestawu aplikacji assembly_name `<assembly_name>.exe`>: .
1. Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.
1. Jeśli wystąpią błędy podczas tworzenia żądania do aplikacji, zgłoś żądanie do hosta i portu, w którym nasłuchuje Kestrel. Korzystając z domyślnego hosta i `http://localhost:5000/`księguj, zgłać żądanie do pliku . Jeśli aplikacja odpowiada normalnie na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobne związane z konfiguracją hostingu i mniej prawdopodobne w aplikacji.

### <a name="aspnet-core-module-stdout-log-iis"></a>ASP.NET dziennik stdout modułu rdzenia (IIS)

Aby włączyć i wyświetlić dzienniki stdout:

1. Przejdź do folderu wdrażania witryny w systemie hostingowym.
1. Jeśli folder *dzienników* nie jest obecny, utwórz folder. Aby uzyskać instrukcje dotyczące włączania programu MSBuild do automatycznego tworzenia folderu *dzienników* we wdrożeniu, zobacz temat [struktury katalogów.](xref:host-and-deploy/directory-structure)
1. Edytuj plik *web.config.* Ustaw **stdoutLogEnabled** i `true` zmień ścieżkę **stdoutLogFile,** `.\logs\stdout`aby wskazywała folder *dzienniki* (na przykład ). `stdout`w ścieżce znajduje się prefiks nazwy pliku dziennika. Sygnatura czasowa, identyfikator procesu i rozszerzenie pliku są dodawane automatycznie podczas tworzenia dziennika. Typowy `stdout` plik dziennika, używając jako prefiksu nazwy pliku, nosi nazwę *stdout_20180205184032_5412.log*.
1. Upewnij się, że tożsamość puli aplikacji ma uprawnienia do zapisu w folderze *dzienników.*
1. Zapisz zaktualizowany plik *web.config.*
1. Złożyć wniosek do aplikacji.
1. Przejdź do folderu *dzienników.* Znajdź i otwórz najnowszy dziennik stdout.
1. Przestudiuj dziennik pod kątem błędów.

Wyłącz rejestrowanie stdout po zakończeniu rozwiązywania problemów:

1. Edytuj plik *web.config.*
1. Ustaw **stdoutLogEnabled** na `false`.
1. Zapisz plik.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma limitu rozmiaru pliku dziennika ani liczby utworzonych plików dziennika.
>
> W przypadku rutynowego rejestrowania w aplikacji ASP.NET Core należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-iis"></a>ASP.NET dziennika debugowania modułu rdzenia (IIS)

Dodaj następujące ustawienia obsługi do pliku *web.config* aplikacji, aby włączyć ASP.NET dziennika debugowania modułu rdzenia:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Upewnij się, że ścieżka określona dla dziennika istnieje i że tożsamość puli aplikacji ma uprawnienia do zapisu do lokalizacji.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

### <a name="enable-the-developer-exception-page"></a>Włączanie strony wyjątków dla deweloperów

`ASPNETCORE_ENVIRONMENT` [Zmienną środowiskową można dodać do web.config,](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) aby uruchomić aplikację w środowisku deweloperskim. Tak długo, jak środowisko nie jest zastępowane `UseEnvironment` podczas uruchamiania aplikacji przez konstruktora hosta, ustawienie zmiennej środowiskowej umożliwia [stronie wyjątku dewelopera,](xref:fundamentals/error-handling) aby pojawić się po uruchomieniu aplikacji.

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

Ustawienie zmiennej `ASPNETCORE_ENVIRONMENT` środowiskowej dla jest zalecane tylko do użytku na serwerach przejściowych i testowych, które nie są widoczne w Internecie. Usuń zmienną środowiskową z pliku *web.config* po rozwiązaniu problemu. Aby uzyskać informacje na temat ustawiania zmiennych środowiskowych w *web.config*, zobacz [environmentVariables element podrzędny aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Uzyskiwanie danych z aplikacji

Jeśli aplikacja jest w stanie odpowiadać na żądania, uzyskać żądanie, połączenie i dodatkowe dane z aplikacji przy użyciu wbudowanego oprogramowania pośredniczącego terminalu. Aby uzyskać więcej informacji <xref:test/troubleshoot#obtain-data-from-an-app>i przykładowy kod, zobacz .

### <a name="slow-or-hanging-app-iis"></a>Powolna lub wisząca aplikacja (IIS)

*Zrzut awaryjny* jest migawką pamięci systemu i może pomóc w określeniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikacja ulega awarii lub napotyka wyjątek

Uzyskiwanie i analizowanie zrzutu z [raportowania błędów systemu Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Utwórz folder do przechowywania `c:\dumps`plików zrzutu awaryjnego w pliku . Pula aplikacji musi mieć dostęp do zapisu do folderu.
1. Uruchamianie [skryptu EnableDumps PowerShell:](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)
   * Jeśli aplikacja korzysta z [modelu hostingu w trakcie,](xref:host-and-deploy/iis/index#in-process-hosting-model)uruchom skrypt dla *w3wp.exe:*

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Jeśli aplikacja korzysta z [modelu hostingu poza procesem,](xref:host-and-deploy/iis/index#out-of-process-hosting-model)uruchom skrypt dla *dotnet.exe:*

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Uruchom aplikację w warunkach, które powodują awarię.
1. Po wystąpieniu awarii uruchom [skrypt DisableDumps PowerShell:](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)
   * Jeśli aplikacja korzysta z [modelu hostingu w trakcie,](xref:host-and-deploy/iis/index#in-process-hosting-model)uruchom skrypt dla *w3wp.exe:*

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Jeśli aplikacja korzysta z [modelu hostingu poza procesem,](xref:host-and-deploy/iis/index#out-of-process-hosting-model)uruchom skrypt dla *dotnet.exe:*

     ```console
     .\DisableDumps dotnet.exe
     ```

Po awarii aplikacji i kolekcji zrzutu jest zakończona, aplikacja może zakończyć się normalnie. Skrypt programu PowerShell konfiguruje program WER do zbierania maksymalnie pięciu zrzutów na aplikację.

> [!WARNING]
> Zrzuty awaryjne mogą zająć dużą ilość miejsca na dysku (do kilku gigabajtów).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie

Gdy aplikacja *zawiesza* się (przestaje odpowiadać, ale nie ulega awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [Pliki zrzutu w trybie użytkownika: Wybieranie najlepszego narzędzia,](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) aby wybrać odpowiednie narzędzie do produkcji zrzutu.

#### <a name="analyze-the-dump"></a>Analizowanie zrzutu

Zrzut można analizować przy użyciu kilku metod. Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu w trybie użytkownika](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="clear-package-caches"></a>Wyczyść pamięć podręczną pakietów

Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestawu .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w aplikacji. W niektórych przypadkach niespójne pakiety mogą spowodować przerwanie aplikacji podczas wykonywania głównych uaktualnień. Większość z tych problemów można rozwiązać, postępując zgodnie z następującymi instrukcjami:

1. Usuń *foldery bin* i *obj.*
1. Wyczyść bufory pakietu, wykonując [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z powłoki poleceń.

   Czyszczenie pamięci podręcznych pakietów można również wykonać za pomocą narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonać polecenie `nuget locals all -clear`. *nuget.exe* nie jest w pakiecie zainstalować z systemem operacyjnym Windows dla komputerów stacjonarnych i muszą być uzyskane oddzielnie od [strony internetowej NuGet](https://www.nuget.org/downloads).

1. Przywracanie i odbudowywać projekt.
1. Usuń wszystkie pliki w folderze wdrażania na serwerze przed ponowne wdrożeniem aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Dokumentacja platformy Azure

* [Usługa Application Insights dla platformy ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Zdalne debugowanie aplikacji sieci Web sekcji Rozwiązywanie problemów z aplikacją sieci Web w usłudze Azure App Service przy użyciu programu Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Omówienie diagnostyki usługi Azure App Service](/azure/app-service/app-service-diagnostics)
* [Jak monitorować aplikacje w usłudze Azure App Service](/azure/app-service/web-sites-monitor)
* [Rozwiązywanie problemów z aplikacją internetową w usłudze Azure App Service przy użyciu programu Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Rozwiązywanie problemów z błędami HTTP "502 bad gateway" i "503 usługa niedostępna" w aplikacjach sieci Web platformy Azure](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Często zadawane pytania dotyczące wydajności aplikacji dla aplikacji sieci Web na platformie Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Piaskownica aplikacji Azure Web App (ograniczenia wykonywania środowiska uruchomieniowego usługi App Service)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Usługa Azure Friday: Środowisko diagnostyki i rozwiązywania problemów usługi Azure App Service (12-minutowy film wideo)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Dokumentacja programu Visual Studio

* [Zdalne debugowanie ASP.NET core na usługach IIS na platformie Azure w programie Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure)
* [Zdalne debugowanie ASP.NET rdzenia na komputerze zdalnych usług IIS w programie Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Nauka debugowania przy użyciu programu Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Dokumentacja kodu programu Visual Studio

* [Debugowanie za pomocą kodu programu Visual Studio](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Ten artykuł zawiera informacje na temat typowych błędów uruchamiania aplikacji i instrukcje dotyczące diagnozowania błędów, gdy aplikacja jest wdrażana w usłudze Azure App Service lub usługach IIS:

[Błędy uruchamiania aplikacji](#app-startup-errors)  
W tym artykule wyjaśniono typowe scenariusze kodu stanu HTTP podczas uruchamiania.

[Rozwiązywanie problemów w usłudze Azure App Service](#troubleshoot-on-azure-app-service)  
Zawiera porady dotyczące rozwiązywania problemów dla aplikacji wdrożonych w usłudze Azure App Service.

[Rozwiązywanie problemów w usługach IIS](#troubleshoot-on-iis)  
Zawiera porady dotyczące rozwiązywania problemów dla aplikacji wdrożonych w serwisach IIS lub działających na usługach IIS Express lokalnie. Wskazówki dotyczą zarówno wdrożeń pulpitu w systemie Windows Server, jak i windows.

[Wyczyść pamięć podręczną pakietów](#clear-package-caches)  
Wyjaśniono, co zrobić, gdy niespójne pakiety przerwać aplikację podczas wykonywania głównych uaktualnień lub zmiany wersji pakietu.

[Dodatkowe zasoby](#additional-resources)  
Wyświetla listę dodatkowych tematów rozwiązywania problemów.

## <a name="app-startup-errors"></a>Błędy uruchamiania aplikacji

W programie Visual Studio projekt ASP.NET Core domyślnie ma hosting [usługi IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) podczas debugowania. A *502.5 - Błąd procesu* lub *500.30 - Uruchom błąd,* który występuje podczas debugowania lokalnie można zdiagnozować za pomocą porady w tym temacie.

### <a name="40314-forbidden"></a>403.14 Zabronione

Uruchomienie aplikacji nie powiedzie się. Rejestrowany jest następujący błąd:

```
The Web server is configured to not list the contents of this directory.
```

Błąd jest zwykle spowodowany przez przerwane wdrożenie w systemie hostingowym, który zawiera dowolny z następujących scenariuszy:

* Aplikacja jest wdrażana w niewłaściwym folderze w systemie hostingowym.
* Proces wdrażania nie może przenieść wszystkich plików i folderów aplikacji do folderu wdrażania w systemie hostingowym.
* Brak pliku *web.config* we wdrożeniu lub zawartość pliku *web.config* jest zniekształcona.

Wykonaj poniższe czynności:

1. Usuń wszystkie pliki i foldery z folderu wdrażania w systemie hostingowym.
1. Ponownie rozmieszczaj zawartość folderu *publikowania* aplikacji do systemu hostingowego przy użyciu normalnej metody wdrażania, takiej jak Visual Studio, PowerShell lub wdrożenie ręczne:
   * Upewnij się, że plik *web.config* jest obecny we wdrożeniu i że jego zawartość jest poprawna.
   * Podczas hostingu w usłudze Azure App Service `D:\home\site\wwwroot` upewnij się, że aplikacja została wdrożona w folderze.
   * Gdy aplikacja jest obsługiwana przez usługe IIS, upewnij się, że aplikacja została wdrożona na **ścieżce fizycznej** usług IIS wyświetlanej w **podstawowych ustawieniach menedżera** **usług IIS**.
1. Upewnij się, że wszystkie pliki i foldery aplikacji są wdrażane, porównując wdrożenie w systemie hostingowym z zawartością folderu *publikowania* projektu.

Aby uzyskać więcej informacji na temat układu opublikowanej aplikacji <xref:host-and-deploy/directory-structure>ASP.NET Core, zobacz . Aby uzyskać więcej informacji na temat <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>pliku *web.config,* zobacz .

### <a name="500-internal-server-error"></a>500 — wewnętrzny błąd serwera

Aplikacja uruchamia się, ale błąd uniemożliwia serwerowi spełnienie żądania.

Ten błąd występuje w kodzie aplikacji podczas uruchamiania lub podczas tworzenia odpowiedzi. Odpowiedź może nie zawierać zawartości lub odpowiedź może pojawić się jako *wewnętrzny błąd serwera 500* w przeglądarce. Dziennik zdarzeń aplikacji zwykle stwierdza, że aplikacja została uruchomiona normalnie. Z punktu widzenia serwera jest to poprawne. Aplikacja została uruchomiony, ale nie można wygenerować prawidłową odpowiedź. Uruchom aplikację w wierszu polecenia na serwerze lub włącz ASP.NET dziennik stdout modułu rdzenia, aby rozwiązać problem.

### <a name="5000-in-process-handler-load-failure"></a>500.0 Awaria obciążenia obsługi w trakcie

Proces roboczy kończy się niepowodzeniem. Aplikacja nie uruchamia się.

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) nie może znaleźć programu .NET Core CLR i znaleźć program obsługi żądań w trakcie *(aspnetcorev2_inprocess.dll*). Sprawdź, czy:

* Aplikacja jest przeznaczona dla pakietu [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet lub [metapakietu Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).
* Wersja udostępnionej struktury ASP.NET Core, że obiekty docelowe aplikacji jest zainstalowany na komputerze docelowym.

### <a name="5000-out-of-process-handler-load-failure"></a>500.0 Awaria obciążenia obsługi poza procesem

Proces roboczy kończy się niepowodzeniem. Aplikacja nie uruchamia się.

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) nie może znaleźć obsługi żądań hostingu poza procesem. Upewnij się, że *aspnetcorev2_outofprocess.dll* jest obecny w podfolderze obok *pliku aspnetcorev2.dll*.

### <a name="5025-process-failure"></a>502.5 Błąd procesu

Proces roboczy kończy się niepowodzeniem. Aplikacja nie uruchamia się.

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) próbuje uruchomić proces roboczy, ale nie można go uruchomić. Przyczynę niepowodzenia uruchamiania procesu można zwykle określić na podstawie wpisów w dzienniku zdarzeń aplikacji i ASP.NET dzienniku stdout modułu rdzenia.

Typowym warunkiem awarii jest aplikacja jest nieprawidłowo skonfigurowany ze względu na kierowanie na wersję ASP.NET Core udostępnionej struktury, która nie jest obecny. Sprawdź, które wersje udostępnionej struktury ASP.NET Core są zainstalowane na komputerze docelowym. *Współużytkowana struktura* to zestaw zestawów (plików*dll),* które są zainstalowane na komputerze `Microsoft.AspNetCore.App`i odwołują się do metapakiety, takie jak . Odwołanie do metapakiety można określić minimalną wymaganą wersję. Aby uzyskać więcej informacji, zobacz [Współużytkowane struktury](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Strona błędu *procesu 502.5* jest zwracana, gdy nieprawidłowa konfiguracja hostingu lub aplikacji powoduje niepowodzenie procesu roboczego:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Nie można uruchomić aplikacji (Kod błędu '0x800700c1')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Nie można uruchomić aplikacji, ponieważ nie można załadować zestawu aplikacji (*dll).*

Ten błąd występuje, gdy istnieje niezgodność bitness między opublikowanej aplikacji i w3wp/iisexpress procesu.

Upewnij się, że 32-bitowe ustawienie puli aplikacji jest poprawne:

1. Wybierz pulę aplikacji w **pulach aplikacji**menedżera usług IIS .
1. Wybierz pozycję **Ustawienia zaawansowane** w obszarze **Edytowanie puli aplikacji** w panelu **Akcje.**
1. Ustawianie **włączania aplikacji 32-bitowych:**
   * Jeśli wdrażasz aplikację 32-bitową (x86), `True`ustaw wartość .
   * Jeśli wdrażasz 64-bitową (x64) aplikację, `False`ustaw wartość .

Upewnij się, że nie ma `<Platform>` konfliktu między właściwością MSBuild w pliku projektu a opublikowaną bitowością aplikacji.

### <a name="connection-reset"></a>Resetowanie połączenia

Jeśli po wysłaniu nagłówków wystąpi błąd, jest za późno, aby serwer wysłał **błąd serwera wewnętrznego 500,** gdy wystąpi błąd. Dzieje się tak często, gdy wystąpi błąd podczas serializacji złożonych obiektów dla odpowiedzi. Ten typ błędu pojawia się jako błąd *resetowania połączenia* na kliencie. [Rejestrowanie aplikacji](xref:fundamentals/logging/index) może pomóc w rozwiązywaniu problemów z tego typu błędami.

### <a name="default-startup-limits"></a>Domyślne limity uruchamiania

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) jest skonfigurowany z domyślnym *startupTimeLimit* 120 sekund. Po lewej stronie na wartość domyślną, aplikacja może potrwać do dwóch minut, aby rozpocząć przed moduł rejestruje błąd procesu. Aby uzyskać informacje na temat konfigurowania modułu, zobacz [Atrybuty elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Rozwiązywanie problemów w usłudze Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Dziennik zdarzeń aplikacji (usługa Azure App Service)

Aby uzyskać dostęp do dziennika zdarzeń aplikacji, należy użyć **diagnozowania i rozwiązywania problemów** bloku w witrynie Azure portal:

1. W witrynie Azure portal otwórz aplikację w **usługach App Services**.
1. Kliknij pozycję **Diagnozowanie i rozwiązywanie problemów**.
1. Wybierz nagłówek **Narzędzia diagnostyczne.**
1. W obszarze **Narzędzia pomocnicze**wybierz przycisk **Zdarzenia aplikacji.**
1. Sprawdź ostatni błąd dostarczony przez *wpis IIS AspNetCoreModule* lub *IIS AspNetCoreModule V2* w kolumnie **Źródło.**

Alternatywą dla korzystania **z diagnozowania i rozwiązywania problemów** bloku jest sprawdzenie pliku dziennika zdarzeń aplikacji bezpośrednio za pomocą [Kudu:](https://github.com/projectkudu/kudu/wiki)

1. Otwórz **zaawansowane narzędzia** w obszarze Narzędzia **programistyczne.** Wybierz przycisk **Przejdź.&rarr; ** Konsola Kudu otwiera się w nowej karcie przeglądarki lub oknie.
1. Korzystając z paska nawigacyjnego u góry strony, otwórz **konsolę debugowania** i wybierz **cmd**.
1. Otwórz folder **LogFiles.**
1. Wybierz ikonę ołówka obok pliku *eventlog.xml.*
1. Sprawdź dziennik. Przewiń do dołu dziennika, aby zobaczyć najnowsze zdarzenia.

### <a name="run-the-app-in-the-kudu-console"></a>Uruchamianie aplikacji w konsoli Kudu

Wiele błędów uruchamiania nie generuje przydatnych informacji w dzienniku zdarzeń aplikacji. Aplikację można uruchomić w konsoli zdalnego wykonania [Kudu,](https://github.com/projectkudu/kudu/wiki) aby wykryć błąd:

1. Otwórz **zaawansowane narzędzia** w obszarze Narzędzia **programistyczne.** Wybierz przycisk **Przejdź.&rarr; ** Konsola Kudu otwiera się w nowej karcie przeglądarki lub oknie.
1. Korzystając z paska nawigacyjnego u góry strony, otwórz **konsolę debugowania** i wybierz **cmd**.

#### <a name="test-a-32-bit-x86-app"></a>Testowanie aplikacji 32-bitowej (x86)

**Bieżące wydanie**

1. `cd d:\home\site\wwwroot`
1. Uruchom aplikację:
   * Jeśli aplikacja jest [wdrożeniem zależnym od struktury:](/dotnet/core/deploying/#framework-dependent-deployments-fdd)

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Jeśli aplikacja jest [samodzielnym wdrożeniem:](/dotnet/core/deploying/#self-contained-deployments-scd)

     ```console
     {ASSEMBLY NAME}.exe
     ```

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

**Wdrożenie zależne od struktury uruchomione w wersji zapoznawczej**

*Wymaga zainstalowania rozszerzenia witryny ASP.NET Core {VERSION} (x86).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(jest`{X.Y}` wersją środowiska wykonawczego)
1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Testowanie aplikacji 64-bitowej (x64)

**Bieżące wydanie**

* Jeśli aplikacja jest 64-bitowym (x64) [wdrożeniem zależnym od struktury:](/dotnet/core/deploying/#framework-dependent-deployments-fdd)
  1. `cd D:\Program Files\dotnet`
  1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Jeśli aplikacja jest [samodzielnym wdrożeniem:](/dotnet/core/deploying/#self-contained-deployments-scd)
  1. `cd D:\home\site\wwwroot`
  1. Uruchom aplikację: `{ASSEMBLY NAME}.exe`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

**Wdrożenie zależne od struktury uruchomione w wersji zapoznawczej**

*Wymaga zainstalowania rozszerzenia witryny ASP.NET Core {VERSION} (x64).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(jest`{X.Y}` wersją środowiska wykonawczego)
1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>ASP.NET dziennik stdout modułu rdzenia (usługa Azure App Service)

Dziennik stdout modułu ASP.NET core często rejestruje przydatne komunikaty o błędach, które nie zostały znalezione w dzienniku zdarzeń aplikacji. Aby włączyć i wyświetlić dzienniki stdout:

1. Przejdź do **bloku Diagnozowanie i rozwiązywanie problemów** w witrynie Azure portal.
1. W **obszarze WYBIERZ KATEGORIĘ PROBLEM**wybierz przycisk W dół aplikacji sieci **Web.**
1. W obszarze **Sugerowane** > rozwiązania **Włącz przekierowanie dziennika stdout**wybierz przycisk **Otwórz konsolę Kudu, aby edytować web.config**.
1. W **Konsoli diagnostycznej**Kudu otwórz foldery do **strony** > ścieżki**wwwroot**. Przewiń w dół, aby wyświetlić plik *web.config* u dołu listy.
1. Kliknij ikonę ołówka obok pliku *web.config.*
1. Ustaw **stdoutLogEnabled** i `true` zmień ścieżkę **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.
1. Wybierz **pozycję Zapisz,** aby zapisać zaktualizowany plik *web.config.*
1. Złożyć wniosek do aplikacji.
1. Wróć do witryny Azure Portal. Wybierz ostrze **Narzędzia zaawansowane** w obszarze **NARZĘDZIA PROGRAMISTYCZNE.** Wybierz przycisk **Przejdź.&rarr; ** Konsola Kudu otwiera się w nowej karcie przeglądarki lub oknie.
1. Korzystając z paska nawigacyjnego u góry strony, otwórz **konsolę debugowania** i wybierz **cmd**.
1. Wybierz folder **LogFiles.**
1. Sprawdź kolumnę **Zmodyfikowane** i wybierz ikonę ołówka, aby edytować dziennik stdout z najpóźniejszą datą modyfikacji.
1. Po otwarciu pliku dziennika zostanie wyświetlony błąd.

Wyłącz rejestrowanie stdout po zakończeniu rozwiązywania problemów:

1. W **Konsoli Diagnostycznej**Kudu wróć do **strony** > ścieżki**wwwroot,** aby odsłonić plik *web.config.* Ponownie otwórz plik **web.config,** wybierając ikonę ołówka.
1. Ustaw **stdoutLogEnabled** na `false`.
1. Wybierz **pozycję Zapisz,** aby zapisać plik.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma limitu rozmiaru pliku dziennika ani liczby utworzonych plików dziennika. Rejestrowanie stdout umożliwia rozwiązywanie problemów z uruchamianiem aplikacji.
>
> Aby ogólne rejestrowanie w aplikacji core ASP.NET po uruchomieniu, należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>dziennik debugowania ASP.NET core module (usługa Azure App Service)

Dziennik debugowania modułu ASP.NET core zapewnia dodatkowe, głębsze rejestrowanie z modułu ASP.NET Core. Aby włączyć i wyświetlić dzienniki stdout:

1. Aby włączyć rozszerzony dziennik diagnostyczny, wykonaj jedną z następujących czynności:
   * Postępuj zgodnie z instrukcjami w [rozszerzonych dzienników diagnostycznych,](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) aby skonfigurować aplikację do rozszerzonego rejestrowania diagnostycznego. Ponowne wdrożenie aplikacji.
   * Dodaj `<handlerSettings>` pokazane w [rozszerzonych dziennikach diagnostycznych](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) do pliku *web.config* aplikacji na żywo przy użyciu konsoli Kudu:
     1. Otwórz **zaawansowane narzędzia** w obszarze Narzędzia **programistyczne.** Wybierz przycisk **Przejdź.&rarr; ** Konsola Kudu otwiera się w nowej karcie przeglądarki lub oknie.
     1. Korzystając z paska nawigacyjnego u góry strony, otwórz **konsolę debugowania** i wybierz **cmd**.
     1. Otwórz foldery do **witryny** > ścieżki**wwwroot**. Edytuj plik *web.config,* wybierając przycisk ołówka. Dodaj `<handlerSettings>` sekcję, jak pokazano w [rozszerzonych dziennikach diagnostycznych](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Wybierz ikonę **Zapisz**.
1. Otwórz **zaawansowane narzędzia** w obszarze Narzędzia **programistyczne.** Wybierz przycisk **Przejdź.&rarr; ** Konsola Kudu otwiera się w nowej karcie przeglądarki lub oknie.
1. Korzystając z paska nawigacyjnego u góry strony, otwórz **konsolę debugowania** i wybierz **cmd**.
1. Otwórz foldery do **witryny** > ścieżki**wwwroot**. Jeśli nie podasz ścieżki dla pliku *aspnetcore-debug.log,* plik pojawi się na liście. Jeśli podano ścieżkę, przejdź do lokalizacji pliku dziennika.
1. Otwórz plik dziennika za pomocą przycisku ołówka obok nazwy pliku.

Wyłącz rejestrowanie debugowania po zakończeniu rozwiązywania problemów:

Aby wyłączyć rozszerzony dziennik debugowania, wykonaj jedną z następujących czynności:

* Usuń `<handlerSettings>` plik *web.config* lokalnie i ponownie wdrożyć aplikację.
* Użyj konsoli Kudu, aby edytować plik *web.config* i usunąć sekcję. `<handlerSettings>` Zapisz plik.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> Niepowodzenie wyłączenia dziennika debugowania może prowadzić do awarii aplikacji lub serwera. Nie ma limitu rozmiaru pliku dziennika. Rejestrowania debugowania należy używać tylko do rozwiązywania problemów z uruchamianiem aplikacji.
>
> Aby ogólne rejestrowanie w aplikacji core ASP.NET po uruchomieniu, należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Powolna lub wisząca aplikacja (usługa Azure App Service)

Gdy aplikacja odpowiada powoli lub zawiesza się na żądanie, zobacz następujące artykuły:

* [Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Używanie rozszerzenia witryny diagnostyki awarii do przechwytywania zrzutu w przypadku sporadyczne problemy z wyjątkami lub problemy z wydajnością w aplikacji Sieci Web platformy Azure](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>Ostrza monitorujące

Bloky monitorowania zapewniają alternatywne środowisko rozwiązywania problemów z metodami opisanymi wcześniej w temacie. Te ostrza mogą być używane do diagnozowania błędów serii 500.

Upewnij się, że są zainstalowane rozszerzenia ASP.NET Core. Jeśli rozszerzenia nie są zainstalowane, zainstaluj je ręcznie:

1. W sekcji bloku **NARZĘDZIA ROZWOJU** wybierz ostrze **Rozszerzenia.**
1. Rozszerzenia **ASP.NET core** powinny pojawić się na liście.
1. Jeśli rozszerzenia nie są zainstalowane, wybierz przycisk **Dodaj.**
1. Wybierz z listy **ASP.NET rozszerzenia rdzenia.**
1. Wybierz **przycisk OK,** aby zaakceptować warunki prawne.
1. Wybierz **przycisk OK** na bloku Dodaj **rozszerzenie.**
1. Komunikat wyskakującym dla informacji wskazuje, kiedy rozszerzenia zostały pomyślnie zainstalowane.

Jeśli rejestrowanie stdout nie jest włączone, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz blok **Narzędzia zaawansowane** w obszarze **NARZĘDZIA PROGRAMISTYCZNE.** Wybierz przycisk **Przejdź.&rarr; ** Konsola Kudu otwiera się w nowej karcie przeglądarki lub oknie.
1. Korzystając z paska nawigacyjnego u góry strony, otwórz **konsolę debugowania** i wybierz **cmd**.
1. Otwórz foldery do **witryny** > ścieżki **wwwroot** i przewiń w dół, aby odsłonić plik *web.config* u dołu listy.
1. Kliknij ikonę ołówka obok pliku *web.config.*
1. Ustaw **stdoutLogEnabled** i `true` zmień ścieżkę **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.
1. Wybierz **pozycję Zapisz,** aby zapisać zaktualizowany plik *web.config.*

Przejdź do aktywacji rejestrowania diagnostycznego:

1. W witrynie Azure portal wybierz blok **dzienników diagnostyki.**
1. Wybierz przełącznik **On** dla **rejestrowania aplikacji (System plików)** i **Szczegółowe komunikaty o błędach**. Wybierz przycisk **Zapisz** u góry bloku.
1. Aby uwzględnić śledzenie żądań nie powiodło się, znane również jako rejestrowanie buforowania zdarzeń żądania niepowodzenia (FREB), wybierz przełącznik **Włącz** dla **śledzenia żądań nie powiodło się**.
1. Wybierz **blok strumienia dziennika,** który jest wyświetlany natychmiast w **obszarze diagnostyki dzienniki** bloku w portalu.
1. Złożyć wniosek do aplikacji.
1. W danych strumienia dziennika jest wskazana przyczyna błędu.

Pamiętaj, aby wyłączyć rejestrowanie stdout po zakończeniu rozwiązywania problemów.

Aby wyświetlić dzienniki śledzenia żądań, które nie powiodły się (dzienniki FREB):

1. Przejdź do **bloku Diagnozowanie i rozwiązywanie problemów** w witrynie Azure portal.
1. Wybierz **dzienniki śledzenia żądań nie powiodło się** z obszaru **NARZĘDZIA POMOCNICZE** paska bocznego.

Zobacz [śledzenia żądań nie powiodło się sekcji Włącz rejestrowanie diagnostyki dla aplikacji sieci web w usłudze Azure App Service temat](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) i często zadawane pytania dotyczące wydajności aplikacji dla aplikacji sieci Web na [platformie Azure: Jak włączyć śledzenie żądań nie powiodło się?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)

Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania diagnostyki dla aplikacji sieci Web w usłudze Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma limitu rozmiaru pliku dziennika ani liczby utworzonych plików dziennika.
>
> W przypadku rutynowego rejestrowania w aplikacji ASP.NET Core należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>Rozwiązywanie problemów w usługach IIS

### <a name="application-event-log-iis"></a>Dziennik zdarzeń aplikacji (IIS)

Dostęp do dziennika zdarzeń aplikacji:

1. Otwórz menu Start, wyszukaj *podgląd zdarzeń*i wybierz aplikację **Podgląd zdarzeń.**
1. W **Podglądzie zdarzeń**otwórz węzeł **Dzienniki systemu Windows.**
1. Wybierz **opcję Aplikacja,** aby otworzyć dziennik zdarzeń aplikacji.
1. Wyszukaj błędy skojarzone z nieudaną aplikacją. Błędy mają wartość *modułu AspNetCore usługi IIS* lub *modułu AspNetCore usługi IIS Express* w kolumnie *Źródło.*

### <a name="run-the-app-at-a-command-prompt"></a>Uruchamianie aplikacji w wierszu polecenia

Wiele błędów uruchamiania nie generuje przydatnych informacji w dzienniku zdarzeń aplikacji. Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingowym.

#### <a name="framework-dependent-deployment"></a>Wdrożenie zależne od struktury

Jeśli aplikacja jest [wdrożeniem zależnym od struktury:](/dotnet/core/deploying/#framework-dependent-deployments-fdd)

1. W wierszu polecenia przejdź do folderu wdrażania i uruchom aplikację, wykonując zestaw aplikacji za pomocą *pliku dotnet.exe*. W poniższym poleceniu zastąp nazwę \<zestawu aplikacji assembly_name `dotnet .\<assembly_name>.dll`>: .
1. Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.
1. Jeśli wystąpią błędy podczas tworzenia żądania do aplikacji, zgłoś żądanie do hosta i portu, w którym nasłuchuje Kestrel. Korzystając z domyślnego hosta i `http://localhost:5000/`księguj, zgłać żądanie do pliku . Jeśli aplikacja odpowiada normalnie na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobne związane z konfiguracją hostingu i mniej prawdopodobne w aplikacji.

#### <a name="self-contained-deployment"></a>Samodzielne wdrożenie

Jeśli aplikacja jest [samodzielnym wdrożeniem:](/dotnet/core/deploying/#self-contained-deployments-scd)

1. W wierszu polecenia przejdź do folderu wdrażania i uruchom plik wykonywalny aplikacji. W poniższym poleceniu zastąp nazwę \<zestawu aplikacji assembly_name `<assembly_name>.exe`>: .
1. Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.
1. Jeśli wystąpią błędy podczas tworzenia żądania do aplikacji, zgłoś żądanie do hosta i portu, w którym nasłuchuje Kestrel. Korzystając z domyślnego hosta i `http://localhost:5000/`księguj, zgłać żądanie do pliku . Jeśli aplikacja odpowiada normalnie na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobne związane z konfiguracją hostingu i mniej prawdopodobne w aplikacji.

### <a name="aspnet-core-module-stdout-log-iis"></a>ASP.NET dziennik stdout modułu rdzenia (IIS)

Aby włączyć i wyświetlić dzienniki stdout:

1. Przejdź do folderu wdrażania witryny w systemie hostingowym.
1. Jeśli folder *dzienników* nie jest obecny, utwórz folder. Aby uzyskać instrukcje dotyczące włączania programu MSBuild do automatycznego tworzenia folderu *dzienników* we wdrożeniu, zobacz temat [struktury katalogów.](xref:host-and-deploy/directory-structure)
1. Edytuj plik *web.config.* Ustaw **stdoutLogEnabled** i `true` zmień ścieżkę **stdoutLogFile,** `.\logs\stdout`aby wskazywała folder *dzienniki* (na przykład ). `stdout`w ścieżce znajduje się prefiks nazwy pliku dziennika. Sygnatura czasowa, identyfikator procesu i rozszerzenie pliku są dodawane automatycznie podczas tworzenia dziennika. Typowy `stdout` plik dziennika, używając jako prefiksu nazwy pliku, nosi nazwę *stdout_20180205184032_5412.log*.
1. Upewnij się, że tożsamość puli aplikacji ma uprawnienia do zapisu w folderze *dzienników.*
1. Zapisz zaktualizowany plik *web.config.*
1. Złożyć wniosek do aplikacji.
1. Przejdź do folderu *dzienników.* Znajdź i otwórz najnowszy dziennik stdout.
1. Przestudiuj dziennik pod kątem błędów.

Wyłącz rejestrowanie stdout po zakończeniu rozwiązywania problemów:

1. Edytuj plik *web.config.*
1. Ustaw **stdoutLogEnabled** na `false`.
1. Zapisz plik.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma limitu rozmiaru pliku dziennika ani liczby utworzonych plików dziennika.
>
> W przypadku rutynowego rejestrowania w aplikacji ASP.NET Core należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-iis"></a>ASP.NET dziennika debugowania modułu rdzenia (IIS)

Dodaj następujące ustawienia obsługi do pliku *web.config* aplikacji, aby włączyć ASP.NET dziennika debugowania modułu rdzenia:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Upewnij się, że ścieżka określona dla dziennika istnieje i że tożsamość puli aplikacji ma uprawnienia do zapisu do lokalizacji.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

### <a name="enable-the-developer-exception-page"></a>Włączanie strony wyjątków dla deweloperów

`ASPNETCORE_ENVIRONMENT` [Zmienną środowiskową można dodać do web.config,](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) aby uruchomić aplikację w środowisku deweloperskim. Tak długo, jak środowisko nie jest zastępowane `UseEnvironment` podczas uruchamiania aplikacji przez konstruktora hosta, ustawienie zmiennej środowiskowej umożliwia [stronie wyjątku dewelopera,](xref:fundamentals/error-handling) aby pojawić się po uruchomieniu aplikacji.

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

Ustawienie zmiennej `ASPNETCORE_ENVIRONMENT` środowiskowej dla jest zalecane tylko do użytku na serwerach przejściowych i testowych, które nie są widoczne w Internecie. Usuń zmienną środowiskową z pliku *web.config* po rozwiązaniu problemu. Aby uzyskać informacje na temat ustawiania zmiennych środowiskowych w *web.config*, zobacz [environmentVariables element podrzędny aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Uzyskiwanie danych z aplikacji

Jeśli aplikacja jest w stanie odpowiadać na żądania, uzyskać żądanie, połączenie i dodatkowe dane z aplikacji przy użyciu wbudowanego oprogramowania pośredniczącego terminalu. Aby uzyskać więcej informacji <xref:test/troubleshoot#obtain-data-from-an-app>i przykładowy kod, zobacz .

### <a name="slow-or-hanging-app-iis"></a>Powolna lub wisząca aplikacja (IIS)

*Zrzut awaryjny* jest migawką pamięci systemu i może pomóc w określeniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikacja ulega awarii lub napotyka wyjątek

Uzyskiwanie i analizowanie zrzutu z [raportowania błędów systemu Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Utwórz folder do przechowywania `c:\dumps`plików zrzutu awaryjnego w pliku . Pula aplikacji musi mieć dostęp do zapisu do folderu.
1. Uruchamianie [skryptu EnableDumps PowerShell:](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)
   * Jeśli aplikacja korzysta z [modelu hostingu w trakcie,](xref:host-and-deploy/iis/index#in-process-hosting-model)uruchom skrypt dla *w3wp.exe:*

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Jeśli aplikacja korzysta z [modelu hostingu poza procesem,](xref:host-and-deploy/iis/index#out-of-process-hosting-model)uruchom skrypt dla *dotnet.exe:*

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Uruchom aplikację w warunkach, które powodują awarię.
1. Po wystąpieniu awarii uruchom [skrypt DisableDumps PowerShell:](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)
   * Jeśli aplikacja korzysta z [modelu hostingu w trakcie,](xref:host-and-deploy/iis/index#in-process-hosting-model)uruchom skrypt dla *w3wp.exe:*

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Jeśli aplikacja korzysta z [modelu hostingu poza procesem,](xref:host-and-deploy/iis/index#out-of-process-hosting-model)uruchom skrypt dla *dotnet.exe:*

     ```console
     .\DisableDumps dotnet.exe
     ```

Po awarii aplikacji i kolekcji zrzutu jest zakończona, aplikacja może zakończyć się normalnie. Skrypt programu PowerShell konfiguruje program WER do zbierania maksymalnie pięciu zrzutów na aplikację.

> [!WARNING]
> Zrzuty awaryjne mogą zająć dużą ilość miejsca na dysku (do kilku gigabajtów).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie

Gdy aplikacja *zawiesza* się (przestaje odpowiadać, ale nie ulega awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [Pliki zrzutu w trybie użytkownika: Wybieranie najlepszego narzędzia,](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) aby wybrać odpowiednie narzędzie do produkcji zrzutu.

#### <a name="analyze-the-dump"></a>Analizowanie zrzutu

Zrzut można analizować przy użyciu kilku metod. Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu w trybie użytkownika](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="clear-package-caches"></a>Wyczyść pamięć podręczną pakietów

Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestawu .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w aplikacji. W niektórych przypadkach niespójne pakiety mogą spowodować przerwanie aplikacji podczas wykonywania głównych uaktualnień. Większość z tych problemów można rozwiązać, postępując zgodnie z następującymi instrukcjami:

1. Usuń *foldery bin* i *obj.*
1. Wyczyść bufory pakietu, wykonując [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z powłoki poleceń.

   Czyszczenie pamięci podręcznych pakietów można również wykonać za pomocą narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonać polecenie `nuget locals all -clear`. *nuget.exe* nie jest w pakiecie zainstalować z systemem operacyjnym Windows dla komputerów stacjonarnych i muszą być uzyskane oddzielnie od [strony internetowej NuGet](https://www.nuget.org/downloads).

1. Przywracanie i odbudowywać projekt.
1. Usuń wszystkie pliki w folderze wdrażania na serwerze przed ponowne wdrożeniem aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Dokumentacja platformy Azure

* [Usługa Application Insights dla platformy ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Zdalne debugowanie aplikacji sieci Web sekcji Rozwiązywanie problemów z aplikacją sieci Web w usłudze Azure App Service przy użyciu programu Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Omówienie diagnostyki usługi Azure App Service](/azure/app-service/app-service-diagnostics)
* [Jak monitorować aplikacje w usłudze Azure App Service](/azure/app-service/web-sites-monitor)
* [Rozwiązywanie problemów z aplikacją internetową w usłudze Azure App Service przy użyciu programu Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Rozwiązywanie problemów z błędami HTTP "502 bad gateway" i "503 usługa niedostępna" w aplikacjach sieci Web platformy Azure](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Często zadawane pytania dotyczące wydajności aplikacji dla aplikacji sieci Web na platformie Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Piaskownica aplikacji Azure Web App (ograniczenia wykonywania środowiska uruchomieniowego usługi App Service)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Usługa Azure Friday: Środowisko diagnostyki i rozwiązywania problemów usługi Azure App Service (12-minutowy film wideo)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Dokumentacja programu Visual Studio

* [Zdalne debugowanie ASP.NET core na usługach IIS na platformie Azure w programie Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure)
* [Zdalne debugowanie ASP.NET rdzenia na komputerze zdalnych usług IIS w programie Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Nauka debugowania przy użyciu programu Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Dokumentacja kodu programu Visual Studio

* [Debugowanie za pomocą kodu programu Visual Studio](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Ten artykuł zawiera informacje na temat typowych błędów uruchamiania aplikacji i instrukcje dotyczące diagnozowania błędów, gdy aplikacja jest wdrażana w usłudze Azure App Service lub usługach IIS:

[Błędy uruchamiania aplikacji](#app-startup-errors)  
W tym artykule wyjaśniono typowe scenariusze kodu stanu HTTP podczas uruchamiania.

[Rozwiązywanie problemów w usłudze Azure App Service](#troubleshoot-on-azure-app-service)  
Zawiera porady dotyczące rozwiązywania problemów dla aplikacji wdrożonych w usłudze Azure App Service.

[Rozwiązywanie problemów w usługach IIS](#troubleshoot-on-iis)  
Zawiera porady dotyczące rozwiązywania problemów dla aplikacji wdrożonych w serwisach IIS lub działających na usługach IIS Express lokalnie. Wskazówki dotyczą zarówno wdrożeń pulpitu w systemie Windows Server, jak i windows.

[Wyczyść pamięć podręczną pakietów](#clear-package-caches)  
Wyjaśniono, co zrobić, gdy niespójne pakiety przerwać aplikację podczas wykonywania głównych uaktualnień lub zmiany wersji pakietu.

[Dodatkowe zasoby](#additional-resources)  
Wyświetla listę dodatkowych tematów rozwiązywania problemów.

## <a name="app-startup-errors"></a>Błędy uruchamiania aplikacji

W programie Visual Studio projekt ASP.NET Core domyślnie ma hosting [usługi IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) podczas debugowania. *Błąd procesu 502.5,* który występuje podczas debugowania lokalnie można zdiagnozować za pomocą porad w tym temacie.

### <a name="40314-forbidden"></a>403.14 Zabronione

Uruchomienie aplikacji nie powiedzie się. Rejestrowany jest następujący błąd:

```
The Web server is configured to not list the contents of this directory.
```

Błąd jest zwykle spowodowany przez przerwane wdrożenie w systemie hostingowym, który zawiera dowolny z następujących scenariuszy:

* Aplikacja jest wdrażana w niewłaściwym folderze w systemie hostingowym.
* Proces wdrażania nie może przenieść wszystkich plików i folderów aplikacji do folderu wdrażania w systemie hostingowym.
* Brak pliku *web.config* we wdrożeniu lub zawartość pliku *web.config* jest zniekształcona.

Wykonaj poniższe czynności:

1. Usuń wszystkie pliki i foldery z folderu wdrażania w systemie hostingowym.
1. Ponownie rozmieszczaj zawartość folderu *publikowania* aplikacji do systemu hostingowego przy użyciu normalnej metody wdrażania, takiej jak Visual Studio, PowerShell lub wdrożenie ręczne:
   * Upewnij się, że plik *web.config* jest obecny we wdrożeniu i że jego zawartość jest poprawna.
   * Podczas hostingu w usłudze Azure App Service `D:\home\site\wwwroot` upewnij się, że aplikacja została wdrożona w folderze.
   * Gdy aplikacja jest obsługiwana przez usługe IIS, upewnij się, że aplikacja została wdrożona na **ścieżce fizycznej** usług IIS wyświetlanej w **podstawowych ustawieniach menedżera** **usług IIS**.
1. Upewnij się, że wszystkie pliki i foldery aplikacji są wdrażane, porównując wdrożenie w systemie hostingowym z zawartością folderu *publikowania* projektu.

Aby uzyskać więcej informacji na temat układu opublikowanej aplikacji <xref:host-and-deploy/directory-structure>ASP.NET Core, zobacz . Aby uzyskać więcej informacji na temat <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>pliku *web.config,* zobacz .

### <a name="500-internal-server-error"></a>500 — wewnętrzny błąd serwera

Aplikacja uruchamia się, ale błąd uniemożliwia serwerowi spełnienie żądania.

Ten błąd występuje w kodzie aplikacji podczas uruchamiania lub podczas tworzenia odpowiedzi. Odpowiedź może nie zawierać zawartości lub odpowiedź może pojawić się jako *wewnętrzny błąd serwera 500* w przeglądarce. Dziennik zdarzeń aplikacji zwykle stwierdza, że aplikacja została uruchomiona normalnie. Z punktu widzenia serwera jest to poprawne. Aplikacja została uruchomiony, ale nie można wygenerować prawidłową odpowiedź. Uruchom aplikację w wierszu polecenia na serwerze lub włącz ASP.NET dziennik stdout modułu rdzenia, aby rozwiązać problem.

### <a name="5025-process-failure"></a>502.5 Błąd procesu

Proces roboczy kończy się niepowodzeniem. Aplikacja nie uruchamia się.

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) próbuje uruchomić proces roboczy, ale nie można go uruchomić. Przyczynę niepowodzenia uruchamiania procesu można zwykle określić na podstawie wpisów w dzienniku zdarzeń aplikacji i ASP.NET dzienniku stdout modułu rdzenia.

Typowym warunkiem awarii jest aplikacja jest nieprawidłowo skonfigurowany ze względu na kierowanie na wersję ASP.NET Core udostępnionej struktury, która nie jest obecny. Sprawdź, które wersje udostępnionej struktury ASP.NET Core są zainstalowane na komputerze docelowym. *Współużytkowana struktura* to zestaw zestawów (plików*dll),* które są zainstalowane na komputerze `Microsoft.AspNetCore.App`i odwołują się do metapakiety, takie jak . Odwołanie do metapakiety można określić minimalną wymaganą wersję. Aby uzyskać więcej informacji, zobacz [Współużytkowane struktury](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Strona błędu *procesu 502.5* jest zwracana, gdy nieprawidłowa konfiguracja hostingu lub aplikacji powoduje niepowodzenie procesu roboczego:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Nie można uruchomić aplikacji (Kod błędu '0x800700c1')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Nie można uruchomić aplikacji, ponieważ nie można załadować zestawu aplikacji (*dll).*

Ten błąd występuje, gdy istnieje niezgodność bitness między opublikowanej aplikacji i w3wp/iisexpress procesu.

Upewnij się, że 32-bitowe ustawienie puli aplikacji jest poprawne:

1. Wybierz pulę aplikacji w **pulach aplikacji**menedżera usług IIS .
1. Wybierz pozycję **Ustawienia zaawansowane** w obszarze **Edytowanie puli aplikacji** w panelu **Akcje.**
1. Ustawianie **włączania aplikacji 32-bitowych:**
   * Jeśli wdrażasz aplikację 32-bitową (x86), `True`ustaw wartość .
   * Jeśli wdrażasz 64-bitową (x64) aplikację, `False`ustaw wartość .

Upewnij się, że nie ma `<Platform>` konfliktu między właściwością MSBuild w pliku projektu a opublikowaną bitowością aplikacji.

### <a name="connection-reset"></a>Resetowanie połączenia

Jeśli po wysłaniu nagłówków wystąpi błąd, jest za późno, aby serwer wysłał **błąd serwera wewnętrznego 500,** gdy wystąpi błąd. Dzieje się tak często, gdy wystąpi błąd podczas serializacji złożonych obiektów dla odpowiedzi. Ten typ błędu pojawia się jako błąd *resetowania połączenia* na kliencie. [Rejestrowanie aplikacji](xref:fundamentals/logging/index) może pomóc w rozwiązywaniu problemów z tego typu błędami.

### <a name="default-startup-limits"></a>Domyślne limity uruchamiania

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) jest skonfigurowany z domyślnym *startupTimeLimit* 120 sekund. Po lewej stronie na wartość domyślną, aplikacja może potrwać do dwóch minut, aby rozpocząć przed moduł rejestruje błąd procesu. Aby uzyskać informacje na temat konfigurowania modułu, zobacz [Atrybuty elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Rozwiązywanie problemów w usłudze Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Dziennik zdarzeń aplikacji (usługa Azure App Service)

Aby uzyskać dostęp do dziennika zdarzeń aplikacji, należy użyć **diagnozowania i rozwiązywania problemów** bloku w witrynie Azure portal:

1. W witrynie Azure portal otwórz aplikację w **usługach App Services**.
1. Kliknij pozycję **Diagnozowanie i rozwiązywanie problemów**.
1. Wybierz nagłówek **Narzędzia diagnostyczne.**
1. W obszarze **Narzędzia pomocnicze**wybierz przycisk **Zdarzenia aplikacji.**
1. Sprawdź ostatni błąd dostarczony przez *wpis IIS AspNetCoreModule* lub *IIS AspNetCoreModule V2* w kolumnie **Źródło.**

Alternatywą dla korzystania **z diagnozowania i rozwiązywania problemów** bloku jest sprawdzenie pliku dziennika zdarzeń aplikacji bezpośrednio za pomocą [Kudu:](https://github.com/projectkudu/kudu/wiki)

1. Otwórz **zaawansowane narzędzia** w obszarze Narzędzia **programistyczne.** Wybierz przycisk **Przejdź.&rarr; ** Konsola Kudu otwiera się w nowej karcie przeglądarki lub oknie.
1. Korzystając z paska nawigacyjnego u góry strony, otwórz **konsolę debugowania** i wybierz **cmd**.
1. Otwórz folder **LogFiles.**
1. Wybierz ikonę ołówka obok pliku *eventlog.xml.*
1. Sprawdź dziennik. Przewiń do dołu dziennika, aby zobaczyć najnowsze zdarzenia.

### <a name="run-the-app-in-the-kudu-console"></a>Uruchamianie aplikacji w konsoli Kudu

Wiele błędów uruchamiania nie generuje przydatnych informacji w dzienniku zdarzeń aplikacji. Aplikację można uruchomić w konsoli zdalnego wykonania [Kudu,](https://github.com/projectkudu/kudu/wiki) aby wykryć błąd:

1. Otwórz **zaawansowane narzędzia** w obszarze Narzędzia **programistyczne.** Wybierz przycisk **Przejdź.&rarr; ** Konsola Kudu otwiera się w nowej karcie przeglądarki lub oknie.
1. Korzystając z paska nawigacyjnego u góry strony, otwórz **konsolę debugowania** i wybierz **cmd**.

#### <a name="test-a-32-bit-x86-app"></a>Testowanie aplikacji 32-bitowej (x86)

**Bieżące wydanie**

1. `cd d:\home\site\wwwroot`
1. Uruchom aplikację:
   * Jeśli aplikacja jest [wdrożeniem zależnym od struktury:](/dotnet/core/deploying/#framework-dependent-deployments-fdd)

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Jeśli aplikacja jest [samodzielnym wdrożeniem:](/dotnet/core/deploying/#self-contained-deployments-scd)

     ```console
     {ASSEMBLY NAME}.exe
     ```

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

**Wdrożenie zależne od struktury uruchomione w wersji zapoznawczej**

*Wymaga zainstalowania rozszerzenia witryny ASP.NET Core {VERSION} (x86).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(jest`{X.Y}` wersją środowiska wykonawczego)
1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Testowanie aplikacji 64-bitowej (x64)

**Bieżące wydanie**

* Jeśli aplikacja jest 64-bitowym (x64) [wdrożeniem zależnym od struktury:](/dotnet/core/deploying/#framework-dependent-deployments-fdd)
  1. `cd D:\Program Files\dotnet`
  1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Jeśli aplikacja jest [samodzielnym wdrożeniem:](/dotnet/core/deploying/#self-contained-deployments-scd)
  1. `cd D:\home\site\wwwroot`
  1. Uruchom aplikację: `{ASSEMBLY NAME}.exe`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

**Wdrożenie zależne od struktury uruchomione w wersji zapoznawczej**

*Wymaga zainstalowania rozszerzenia witryny ASP.NET Core {VERSION} (x64).*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(jest`{X.Y}` wersją środowiska wykonawczego)
1. Uruchom aplikację: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Dane wyjściowe konsoli z aplikacji, pokazujące wszystkie błędy, są przekazywane do konsoli Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>ASP.NET dziennik stdout modułu rdzenia (usługa Azure App Service)

Dziennik stdout modułu ASP.NET core często rejestruje przydatne komunikaty o błędach, które nie zostały znalezione w dzienniku zdarzeń aplikacji. Aby włączyć i wyświetlić dzienniki stdout:

1. Przejdź do **bloku Diagnozowanie i rozwiązywanie problemów** w witrynie Azure portal.
1. W **obszarze WYBIERZ KATEGORIĘ PROBLEM**wybierz przycisk W dół aplikacji sieci **Web.**
1. W obszarze **Sugerowane** > rozwiązania **Włącz przekierowanie dziennika stdout**wybierz przycisk **Otwórz konsolę Kudu, aby edytować web.config**.
1. W **Konsoli diagnostycznej**Kudu otwórz foldery do **strony** > ścieżki**wwwroot**. Przewiń w dół, aby wyświetlić plik *web.config* u dołu listy.
1. Kliknij ikonę ołówka obok pliku *web.config.*
1. Ustaw **stdoutLogEnabled** i `true` zmień ścieżkę **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.
1. Wybierz **pozycję Zapisz,** aby zapisać zaktualizowany plik *web.config.*
1. Złożyć wniosek do aplikacji.
1. Wróć do witryny Azure Portal. Wybierz ostrze **Narzędzia zaawansowane** w obszarze **NARZĘDZIA PROGRAMISTYCZNE.** Wybierz przycisk **Przejdź.&rarr; ** Konsola Kudu otwiera się w nowej karcie przeglądarki lub oknie.
1. Korzystając z paska nawigacyjnego u góry strony, otwórz **konsolę debugowania** i wybierz **cmd**.
1. Wybierz folder **LogFiles.**
1. Sprawdź kolumnę **Zmodyfikowane** i wybierz ikonę ołówka, aby edytować dziennik stdout z najpóźniejszą datą modyfikacji.
1. Po otwarciu pliku dziennika zostanie wyświetlony błąd.

Wyłącz rejestrowanie stdout po zakończeniu rozwiązywania problemów:

1. W **Konsoli Diagnostycznej**Kudu wróć do **strony** > ścieżki**wwwroot,** aby odsłonić plik *web.config.* Ponownie otwórz plik **web.config,** wybierając ikonę ołówka.
1. Ustaw **stdoutLogEnabled** na `false`.
1. Wybierz **pozycję Zapisz,** aby zapisać plik.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma limitu rozmiaru pliku dziennika ani liczby utworzonych plików dziennika. Rejestrowanie stdout umożliwia rozwiązywanie problemów z uruchamianiem aplikacji.
>
> Aby ogólne rejestrowanie w aplikacji core ASP.NET po uruchomieniu, należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Powolna lub wisząca aplikacja (usługa Azure App Service)

Gdy aplikacja odpowiada powoli lub zawiesza się na żądanie, zobacz następujące artykuły:

* [Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Używanie rozszerzenia witryny diagnostyki awarii do przechwytywania zrzutu w przypadku sporadyczne problemy z wyjątkami lub problemy z wydajnością w aplikacji Sieci Web platformy Azure](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>Ostrza monitorujące

Bloky monitorowania zapewniają alternatywne środowisko rozwiązywania problemów z metodami opisanymi wcześniej w temacie. Te ostrza mogą być używane do diagnozowania błędów serii 500.

Upewnij się, że są zainstalowane rozszerzenia ASP.NET Core. Jeśli rozszerzenia nie są zainstalowane, zainstaluj je ręcznie:

1. W sekcji bloku **NARZĘDZIA ROZWOJU** wybierz ostrze **Rozszerzenia.**
1. Rozszerzenia **ASP.NET core** powinny pojawić się na liście.
1. Jeśli rozszerzenia nie są zainstalowane, wybierz przycisk **Dodaj.**
1. Wybierz z listy **ASP.NET rozszerzenia rdzenia.**
1. Wybierz **przycisk OK,** aby zaakceptować warunki prawne.
1. Wybierz **przycisk OK** na bloku Dodaj **rozszerzenie.**
1. Komunikat wyskakującym dla informacji wskazuje, kiedy rozszerzenia zostały pomyślnie zainstalowane.

Jeśli rejestrowanie stdout nie jest włączone, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz blok **Narzędzia zaawansowane** w obszarze **NARZĘDZIA PROGRAMISTYCZNE.** Wybierz przycisk **Przejdź.&rarr; ** Konsola Kudu otwiera się w nowej karcie przeglądarki lub oknie.
1. Korzystając z paska nawigacyjnego u góry strony, otwórz **konsolę debugowania** i wybierz **cmd**.
1. Otwórz foldery do **witryny** > ścieżki **wwwroot** i przewiń w dół, aby odsłonić plik *web.config* u dołu listy.
1. Kliknij ikonę ołówka obok pliku *web.config.*
1. Ustaw **stdoutLogEnabled** i `true` zmień ścieżkę **stdoutLogFile** na: `\\?\%home%\LogFiles\stdout`.
1. Wybierz **pozycję Zapisz,** aby zapisać zaktualizowany plik *web.config.*

Przejdź do aktywacji rejestrowania diagnostycznego:

1. W witrynie Azure portal wybierz blok **dzienników diagnostyki.**
1. Wybierz przełącznik **On** dla **rejestrowania aplikacji (System plików)** i **Szczegółowe komunikaty o błędach**. Wybierz przycisk **Zapisz** u góry bloku.
1. Aby uwzględnić śledzenie żądań nie powiodło się, znane również jako rejestrowanie buforowania zdarzeń żądania niepowodzenia (FREB), wybierz przełącznik **Włącz** dla **śledzenia żądań nie powiodło się**.
1. Wybierz **blok strumienia dziennika,** który jest wyświetlany natychmiast w **obszarze diagnostyki dzienniki** bloku w portalu.
1. Złożyć wniosek do aplikacji.
1. W danych strumienia dziennika jest wskazana przyczyna błędu.

Pamiętaj, aby wyłączyć rejestrowanie stdout po zakończeniu rozwiązywania problemów.

Aby wyświetlić dzienniki śledzenia żądań, które nie powiodły się (dzienniki FREB):

1. Przejdź do **bloku Diagnozowanie i rozwiązywanie problemów** w witrynie Azure portal.
1. Wybierz **dzienniki śledzenia żądań nie powiodło się** z obszaru **NARZĘDZIA POMOCNICZE** paska bocznego.

Zobacz [śledzenia żądań nie powiodło się sekcji Włącz rejestrowanie diagnostyki dla aplikacji sieci web w usłudze Azure App Service temat](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) i często zadawane pytania dotyczące wydajności aplikacji dla aplikacji sieci Web na [platformie Azure: Jak włączyć śledzenie żądań nie powiodło się?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)

Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania diagnostyki dla aplikacji sieci Web w usłudze Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma limitu rozmiaru pliku dziennika ani liczby utworzonych plików dziennika.
>
> W przypadku rutynowego rejestrowania w aplikacji ASP.NET Core należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>Rozwiązywanie problemów w usługach IIS

### <a name="application-event-log-iis"></a>Dziennik zdarzeń aplikacji (IIS)

Dostęp do dziennika zdarzeń aplikacji:

1. Otwórz menu Start, wyszukaj *podgląd zdarzeń*i wybierz aplikację **Podgląd zdarzeń.**
1. W **Podglądzie zdarzeń**otwórz węzeł **Dzienniki systemu Windows.**
1. Wybierz **opcję Aplikacja,** aby otworzyć dziennik zdarzeń aplikacji.
1. Wyszukaj błędy skojarzone z nieudaną aplikacją. Błędy mają wartość *modułu AspNetCore usługi IIS* lub *modułu AspNetCore usługi IIS Express* w kolumnie *Źródło.*

### <a name="run-the-app-at-a-command-prompt"></a>Uruchamianie aplikacji w wierszu polecenia

Wiele błędów uruchamiania nie generuje przydatnych informacji w dzienniku zdarzeń aplikacji. Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingowym.

#### <a name="framework-dependent-deployment"></a>Wdrożenie zależne od struktury

Jeśli aplikacja jest [wdrożeniem zależnym od struktury:](/dotnet/core/deploying/#framework-dependent-deployments-fdd)

1. W wierszu polecenia przejdź do folderu wdrażania i uruchom aplikację, wykonując zestaw aplikacji za pomocą *pliku dotnet.exe*. W poniższym poleceniu zastąp nazwę \<zestawu aplikacji assembly_name `dotnet .\<assembly_name>.dll`>: .
1. Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.
1. Jeśli wystąpią błędy podczas tworzenia żądania do aplikacji, zgłoś żądanie do hosta i portu, w którym nasłuchuje Kestrel. Korzystając z domyślnego hosta i `http://localhost:5000/`księguj, zgłać żądanie do pliku . Jeśli aplikacja odpowiada normalnie na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobne związane z konfiguracją hostingu i mniej prawdopodobne w aplikacji.

#### <a name="self-contained-deployment"></a>Samodzielne wdrożenie

Jeśli aplikacja jest [samodzielnym wdrożeniem:](/dotnet/core/deploying/#self-contained-deployments-scd)

1. W wierszu polecenia przejdź do folderu wdrażania i uruchom plik wykonywalny aplikacji. W poniższym poleceniu zastąp nazwę \<zestawu aplikacji assembly_name `<assembly_name>.exe`>: .
1. Dane wyjściowe konsoli z aplikacji, pokazujące błędy, są zapisywane w oknie konsoli.
1. Jeśli wystąpią błędy podczas tworzenia żądania do aplikacji, zgłoś żądanie do hosta i portu, w którym nasłuchuje Kestrel. Korzystając z domyślnego hosta i `http://localhost:5000/`księguj, zgłać żądanie do pliku . Jeśli aplikacja odpowiada normalnie na adres punktu końcowego Kestrel, problem jest bardziej prawdopodobne związane z konfiguracją hostingu i mniej prawdopodobne w aplikacji.

### <a name="aspnet-core-module-stdout-log-iis"></a>ASP.NET dziennik stdout modułu rdzenia (IIS)

Aby włączyć i wyświetlić dzienniki stdout:

1. Przejdź do folderu wdrażania witryny w systemie hostingowym.
1. Jeśli folder *dzienników* nie jest obecny, utwórz folder. Aby uzyskać instrukcje dotyczące włączania programu MSBuild do automatycznego tworzenia folderu *dzienników* we wdrożeniu, zobacz temat [struktury katalogów.](xref:host-and-deploy/directory-structure)
1. Edytuj plik *web.config.* Ustaw **stdoutLogEnabled** i `true` zmień ścieżkę **stdoutLogFile,** `.\logs\stdout`aby wskazywała folder *dzienniki* (na przykład ). `stdout`w ścieżce znajduje się prefiks nazwy pliku dziennika. Sygnatura czasowa, identyfikator procesu i rozszerzenie pliku są dodawane automatycznie podczas tworzenia dziennika. Typowy `stdout` plik dziennika, używając jako prefiksu nazwy pliku, nosi nazwę *stdout_20180205184032_5412.log*.
1. Upewnij się, że tożsamość puli aplikacji ma uprawnienia do zapisu w folderze *dzienników.*
1. Zapisz zaktualizowany plik *web.config.*
1. Złożyć wniosek do aplikacji.
1. Przejdź do folderu *dzienników.* Znajdź i otwórz najnowszy dziennik stdout.
1. Przestudiuj dziennik pod kątem błędów.

Wyłącz rejestrowanie stdout po zakończeniu rozwiązywania problemów:

1. Edytuj plik *web.config.*
1. Ustaw **stdoutLogEnabled** na `false`.
1. Zapisz plik.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Niepowodzenie wyłączenia dziennika stdout może prowadzić do awarii aplikacji lub serwera. Nie ma limitu rozmiaru pliku dziennika ani liczby utworzonych plików dziennika.
>
> W przypadku rutynowego rejestrowania w aplikacji ASP.NET Core należy użyć biblioteki rejestrowania, która ogranicza rozmiar pliku dziennika i obraca dzienniki. Aby uzyskać więcej informacji, zobacz [dostawców rejestrowania innych firm](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="enable-the-developer-exception-page"></a>Włączanie strony wyjątków dla deweloperów

`ASPNETCORE_ENVIRONMENT` [Zmienną środowiskową można dodać do web.config,](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) aby uruchomić aplikację w środowisku deweloperskim. Tak długo, jak środowisko nie jest zastępowane `UseEnvironment` podczas uruchamiania aplikacji przez konstruktora hosta, ustawienie zmiennej środowiskowej umożliwia [stronie wyjątku dewelopera,](xref:fundamentals/error-handling) aby pojawić się po uruchomieniu aplikacji.

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

Ustawienie zmiennej `ASPNETCORE_ENVIRONMENT` środowiskowej dla jest zalecane tylko do użytku na serwerach przejściowych i testowych, które nie są widoczne w Internecie. Usuń zmienną środowiskową z pliku *web.config* po rozwiązaniu problemu. Aby uzyskać informacje na temat ustawiania zmiennych środowiskowych w *web.config*, zobacz [environmentVariables element podrzędny aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Uzyskiwanie danych z aplikacji

Jeśli aplikacja jest w stanie odpowiadać na żądania, uzyskać żądanie, połączenie i dodatkowe dane z aplikacji przy użyciu wbudowanego oprogramowania pośredniczącego terminalu. Aby uzyskać więcej informacji <xref:test/troubleshoot#obtain-data-from-an-app>i przykładowy kod, zobacz .

### <a name="slow-or-hanging-app-iis"></a>Powolna lub wisząca aplikacja (IIS)

*Zrzut awaryjny* jest migawką pamięci systemu i może pomóc w określeniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikacja ulega awarii lub napotyka wyjątek

Uzyskiwanie i analizowanie zrzutu z [raportowania błędów systemu Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Utwórz folder do przechowywania `c:\dumps`plików zrzutu awaryjnego w pliku . Pula aplikacji musi mieć dostęp do zapisu do folderu.
1. Uruchamianie [skryptu EnableDumps PowerShell:](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)
   * Jeśli aplikacja korzysta z [modelu hostingu w trakcie,](xref:host-and-deploy/iis/index#in-process-hosting-model)uruchom skrypt dla *w3wp.exe:*

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Jeśli aplikacja korzysta z [modelu hostingu poza procesem,](xref:host-and-deploy/iis/index#out-of-process-hosting-model)uruchom skrypt dla *dotnet.exe:*

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Uruchom aplikację w warunkach, które powodują awarię.
1. Po wystąpieniu awarii uruchom [skrypt DisableDumps PowerShell:](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)
   * Jeśli aplikacja korzysta z [modelu hostingu w trakcie,](xref:host-and-deploy/iis/index#in-process-hosting-model)uruchom skrypt dla *w3wp.exe:*

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Jeśli aplikacja korzysta z [modelu hostingu poza procesem,](xref:host-and-deploy/iis/index#out-of-process-hosting-model)uruchom skrypt dla *dotnet.exe:*

     ```console
     .\DisableDumps dotnet.exe
     ```

Po awarii aplikacji i kolekcji zrzutu jest zakończona, aplikacja może zakończyć się normalnie. Skrypt programu PowerShell konfiguruje program WER do zbierania maksymalnie pięciu zrzutów na aplikację.

> [!WARNING]
> Zrzuty awaryjne mogą zająć dużą ilość miejsca na dysku (do kilku gigabajtów).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie

Gdy aplikacja *zawiesza* się (przestaje odpowiadać, ale nie ulega awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [Pliki zrzutu w trybie użytkownika: Wybieranie najlepszego narzędzia,](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) aby wybrać odpowiednie narzędzie do produkcji zrzutu.

#### <a name="analyze-the-dump"></a>Analizowanie zrzutu

Zrzut można analizować przy użyciu kilku metod. Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu w trybie użytkownika](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="clear-package-caches"></a>Wyczyść pamięć podręczną pakietów

Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestawu .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w aplikacji. W niektórych przypadkach niespójne pakiety mogą spowodować przerwanie aplikacji podczas wykonywania głównych uaktualnień. Większość z tych problemów można rozwiązać, postępując zgodnie z następującymi instrukcjami:

1. Usuń *foldery bin* i *obj.*
1. Wyczyść bufory pakietu, wykonując [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z powłoki poleceń.

   Czyszczenie pamięci podręcznych pakietów można również wykonać za pomocą narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonać polecenie `nuget locals all -clear`. *nuget.exe* nie jest w pakiecie zainstalować z systemem operacyjnym Windows dla komputerów stacjonarnych i muszą być uzyskane oddzielnie od [strony internetowej NuGet](https://www.nuget.org/downloads).

1. Przywracanie i odbudowywać projekt.
1. Usuń wszystkie pliki w folderze wdrażania na serwerze przed ponowne wdrożeniem aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Dokumentacja platformy Azure

* [Usługa Application Insights dla platformy ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Zdalne debugowanie aplikacji sieci Web sekcji Rozwiązywanie problemów z aplikacją sieci Web w usłudze Azure App Service przy użyciu programu Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Omówienie diagnostyki usługi Azure App Service](/azure/app-service/app-service-diagnostics)
* [Jak monitorować aplikacje w usłudze Azure App Service](/azure/app-service/web-sites-monitor)
* [Rozwiązywanie problemów z aplikacją internetową w usłudze Azure App Service przy użyciu programu Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Rozwiązywanie problemów z błędami HTTP "502 bad gateway" i "503 usługa niedostępna" w aplikacjach sieci Web platformy Azure](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Rozwiązywanie problemów związanych z niską wydajnością aplikacji internetowych w usłudze Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Często zadawane pytania dotyczące wydajności aplikacji dla aplikacji sieci Web na platformie Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Piaskownica aplikacji Azure Web App (ograniczenia wykonywania środowiska uruchomieniowego usługi App Service)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Usługa Azure Friday: Środowisko diagnostyki i rozwiązywania problemów usługi Azure App Service (12-minutowy film wideo)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Dokumentacja programu Visual Studio

* [Zdalne debugowanie ASP.NET core na usługach IIS na platformie Azure w programie Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure)
* [Zdalne debugowanie ASP.NET rdzenia na komputerze zdalnych usług IIS w programie Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Nauka debugowania przy użyciu programu Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Dokumentacja kodu programu Visual Studio

* [Debugowanie za pomocą kodu programu Visual Studio](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
