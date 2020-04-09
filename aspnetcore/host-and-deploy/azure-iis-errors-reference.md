---
title: Typowe odwołania do błędów dla usługi Azure App Service i usług IIS z ASP.NET Core
author: rick-anderson
description: Uzyskaj porady dotyczące rozwiązywania problemów z typowymi błędami podczas hostowania aplikacji ASP.NET Core w usłudze Azure Apps i usługach IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 635c4cf6f12e62ca7e795b3b3b47e9445b945551
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511603"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a>Typowe odwołania do błędów dla usługi Azure App Service i usług IIS z ASP.NET Core

::: moniker range=">= aspnetcore-2.2"

W tym temacie opisano typowe błędy i zawiera porady dotyczące rozwiązywania problemów z określonymi błędami podczas hostowania aplikacji ASP.NET Core w usłudze Azure Apps Service i usługach IIS.

Aby uzyskać ogólne wskazówki dotyczące rozwiązywania problemów, zobacz <xref:test/troubleshoot-azure-iis>.

Zbierz wymienione poniżej informacje.

* Zachowanie przeglądarki (kod stanu i komunikat o błędzie)
* Wpisy dziennika zdarzeń aplikacji
  * Usługa Azure &ndash; <xref:test/troubleshoot-azure-iis>App Service Zobacz .
  * IIS
    1. Wybierz **polecenie Start** w menu **Windows,** wpisz podgląd *zdarzeń*i naciśnij **klawisz Enter**.
    1. Po otwarciu **Podglądu zdarzeń** rozwiń pozycję **Aplikacja** **dzienników** > systemu Windows na pasku bocznym.
* Element stdout modułu ASP.NET Core i wpisy dziennika debugowania
  * Usługa Azure &ndash; <xref:test/troubleshoot-azure-iis>App Service Zobacz .
  * Usługi IIS &ndash; Postępuj zgodnie z instrukcjami w sekcji [Tworzenie i przekierowywanie dziennika](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) dziennika dziennika oraz [Rozszerzone dzienniki diagnostyczne](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) w temacie ASP.NET Moduł podstawowy.

Porównaj informacje o błędach z następującymi typowymi błędami. Jeśli zostanie znalezione dopasowanie, postępuj zgodnie z poradami dotyczącymi rozwiązywania problemów.

Lista błędów w tym temacie nie jest wyczerpująca. Jeśli wystąpi błąd, którego nie ma na liście, otwórz nowy problem za pomocą przycisku **Opinie o zawartości** u dołu tego tematu ze szczegółowymi instrukcjami dotyczącymi odtworzenia błędu.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>Aktualizacja systemu operacyjnego usunęła 32-bitowy moduł ASP.NET core

**Dziennik aplikacji:** Nie można załadować modułu DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll.** Dane są błędem.

Rozwiązywanie problemów:

Pliki inne niż system operacyjny w katalogu **C:\Windows\SysWOW64\inetsrv** nie są zachowywane podczas uaktualniania systemu operacyjnego. Jeśli moduł ASP.NET Core jest zainstalowany przed uaktualnieniem systemu operacyjnego, a następnie każda pula aplikacji jest uruchamiana w trybie 32-bitowym po uaktualnieniu systemu operacyjnego, ten problem występuje. Po uaktualnieniu systemu operacyjnego napraw moduł ASP.NET Core. Zobacz [Instalowanie pakietu hostingu .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Wybierz **opcję Napraw** po uruchomieniu instalatora.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Brak rozszerzenia lokacji, 32-bitowe (x86) i 64-bitowe (x64) rozszerzenia lokacji lub nieprawidłowy zestaw bitowości procesu

*Dotyczy aplikacji hostowanych przez usługi Azure App Services.*

* **Przeglądarka:** Błąd HTTP 500.0 — błąd obciążenia obsługi w trakcie programu ANCM

* **Dziennik aplikacji:** Wywoływanie hostfxr, aby znaleźć program obsługi żądań inprocess nie powiodło się bez znalezienia żadnych natywnych zależności. Nie można odnaleźć obsługi żądań inprocess. Przechwycone dane wyjściowe z wywoływania hostfxr: Nie można było znaleźć żadnej zgodnej wersji frameworka. Nie znaleziono określonej struktury "Microsoft.AspNetCore.App", wersji\*'{VERSION}-preview- ' . Nie można uruchomić aplikacji "/LM/W3SVC/1416782824/ROOT", kod błędu "0x8000ffff".

* **ASP.NET dziennik stdout modułu rdzenia:** Nie można było znaleźć żadnej zgodnej wersji ramowej. Nie znaleziono określonej struktury "Microsoft.AspNetCore.App", wersji\*'{VERSION}-preview- ' .

* **ASP.NET dziennik debugowania modułu rdzenia:** Wywoływanie hostfxr, aby znaleźć program obsługi żądań inprocess nie powiodło się bez znalezienia żadnych natywnych zależności. Najprawdopodobniej oznacza to, że aplikacja jest nieprawidłowo skonfigurowana, sprawdź wersje microsoft.NetCore.App i Microsoft.AspNetCore.App, które są przeznaczone dla aplikacji i są zainstalowane na komputerze. Nie powiodło się HRESULT zwrócony: 0x8000ffff. Nie można odnaleźć obsługi żądań inprocess. Nie można było znaleźć żadnej zgodnej wersji ramowej. Nie znaleziono określonej struktury "Microsoft.AspNetCore.App", wersji\*'{VERSION}-preview- ' .

Rozwiązywanie problemów:

* Jeśli aplikacja jest uruchomiona w czasie wykonywania w wersji zapoznawczej, zainstaluj rozszerzenie lokacji 32-bitowe (x86) **lub** 64-bitowe (x64), które odpowiada bitowości aplikacji i wersji środowiska wykonawczego aplikacji. **Nie należy instalować zarówno rozszerzenia lub wiele wersji środowiska wykonawczego rozszerzenia.**

  * ASP.NET Core {RUNTIME VERSION} (x86) Środowisko uruchomieniowe
  * ASP.NET Core {RUNTIME VERSION} (x64) Środowisko uruchomieniowe

  Uruchom ponownie aplikację. Poczekaj kilka sekund na ponowne uruchomienie aplikacji.

* Jeśli aplikacja jest uruchomiona w czasie wykonywania w wersji zapoznawczej i są zainstalowane [rozszerzenia lokacji](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) 32-bitowe (x86) i 64-bitowe (x64), odinstaluj rozszerzenie lokacji, które nie odpowiada bitowości aplikacji. Po usunięciu rozszerzenia witryny uruchom ponownie aplikację. Poczekaj kilka sekund na ponowne uruchomienie aplikacji.

* Jeśli uruchomienie aplikacji w czasie wykonywania w wersji zapoznawczej i bitowości rozszerzenia witryny odpowiada wersji aplikacji, upewnij się, że *wersja środowiska uruchomieniowego* rozszerzenia witryny w wersji podglądu jest zgodna z wersją środowiska uruchomieniowego aplikacji.

* Upewnij się, że **platforma** aplikacji w **ustawieniach aplikacji** pasuje do bitowości aplikacji.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Aplikacja x86 jest wdrażana, ale pula aplikacji nie jest włączona dla aplikacji 32-bitowych

* **Przeglądarka:** Błąd HTTP 500.30 — błąd uruchomienia w trakcie procesu ANCM

* **Dziennik aplikacji:** Aplikacja '/LM/W3SVC/5/ROOT' z fizycznym rootem "{PATH}" nawiedziła nieoczekiwany zarządzany wyjątek, kod wyjątku = '0xe0434352'. Sprawdź dzienniki stderr, aby uzyskać więcej informacji. Aplikacja "/LM/W3SVC/5/ROOT" z fizycznym katalogiem głównym "{PATH}" nie może załadować clr i aplikacji zarządzanej. Wątek roboczy CLR został przedwcześnie wyjęty

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika jest tworzony, ale pusty.

* **ASP.NET dziennik debugowania modułu rdzenia:** Nie powiodło się HRESULT zwrócone: 0x8007023e

Ten scenariusz jest zalewkowany przez SDK podczas publikowania aplikacji niezależnej. SDK generuje błąd, `win10-x64` jeśli identyfikator RID nie pasuje do obiektu docelowego platformy (na przykład RID z `<PlatformTarget>x86</PlatformTarget>` w pliku projektu).

Rozwiązywanie problemów:

W przypadku wdrożenia zależnego od`<PlatformTarget>x86</PlatformTarget>`struktury x86 ( włącz pulę aplikacji usług IIS dla aplikacji 32-bitowych. W Menedżerze usług IIS otwórz **ustawienia zaawansowane** puli aplikacji i ustaw opcję Włącz **32-bitowe aplikacje** na **True**.

## <a name="platform-conflicts-with-rid"></a>Platforma powoduje konflikt z rid

* **Przeglądarka:** Błąd HTTP 502.5 - Błąd procesu

* **Dziennik aplikacji:** Aplikacja 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' z fizycznym\{katalogiem głównym 'C: PATH}\' nie\{może rozpocząć procesu z wierszem polecenia '"C: PATH}{ASSEMBLY}. {exe|dll}" ', ErrorCode = '0x80004005 : ff.

* **ASP.NET dziennik stdout modułu rdzenia:** Nieobsługiwał się wyjątek: System.BadImageFormatException: Nie można załadować pliku lub zestawu "{ASSEMBLY}.dll". Podjęto próbę załadowania programu o nieprawidłowym formacie.

Rozwiązywanie problemów:

* Upewnij się, że aplikacja działa lokalnie na Kestrel. Błąd procesu może być wynikiem problemu w aplikacji. Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.

* Jeśli ten wyjątek występuje podczas wdrażania usługi Azure Apps podczas uaktualniania aplikacji i wdrażania nowszych zestawów, ręcznie usuń wszystkie pliki z poprzedniego wdrożenia. Pokutowanie niezgodnych zestawów może `System.BadImageFormatException` spowodować wyjątek podczas wdrażania uaktualnionej aplikacji.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>Nieprawidłowa lub zatrzymana witryna internetowa punktu końcowego URI

* **Przeglądarka:** ERR_CONNECTION_REFUSED **--OR--** Nie można nawiązać połączenia

* **Dziennik aplikacji:** Brak wpisu

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika nie jest tworzony.

* **ASP.NET dziennik debugowania modułu rdzenia:** Plik dziennika nie jest tworzony.

Rozwiązywanie problemów:

* Upewnij się, że używany jest poprawny punkt końcowy URI dla aplikacji. Sprawdź powiązania.

* Upewnij się, że witryna sieci Web iIS nie jest w stanie *Zatrzymane.*

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>Funkcje serwera CoreWebEngine lub W3SVC wyłączone

**Wyjątek systemu operacyjnego:** Funkcje IIS 7.0 CoreWebEngine i W3SVC muszą być zainstalowane, aby korzystać z modułu ASP.NET Core.

Rozwiązywanie problemów:

Upewnij się, że właściwa rola i funkcje są włączone. Zobacz [Konfiguracja usług IIS](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Brak niepoprawnej ścieżki fizycznej lub aplikacji witryny

* **Przeglądarka:** 403 Zabronione - Odmowa dostępu **--OR--** 403.14 Zabronione - Serwer sieci Web jest skonfigurowany tak, aby nie wymieniać zawartości tego katalogu.

* **Dziennik aplikacji:** Brak wpisu

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika nie jest tworzony.

* **ASP.NET dziennik debugowania modułu rdzenia:** Plik dziennika nie jest tworzony.

Rozwiązywanie problemów:

Sprawdź **podstawowe ustawienia** witryny IIS i folder aplikacji fizycznej. Upewnij się, że aplikacja znajduje się w folderze w witrynie IIS **Ścieżka fizyczna**.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Nieprawidłowa rola, ASP.NET nie zainstalowano modułu rdzenia lub nieprawidłowe uprawnienia

* **Przeglądarka:** 500.19 Wewnętrzny błąd serwera — nie można uzyskać dostępu do żądanej strony, ponieważ powiązane dane konfiguracyjne strony są nieprawidłowe. **--OR--** Nie można wyświetlić tej strony

* **Dziennik aplikacji:** Brak wpisu

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika nie jest tworzony.

* **ASP.NET dziennik debugowania modułu rdzenia:** Plik dziennika nie jest tworzony.

Rozwiązywanie problemów:

* Upewnij się, że właściwa rola jest włączona. Zobacz [Konfiguracja usług IIS](xref:host-and-deploy/iis/index#iis-configuration).

* Otwórz **funkcje & programy** lub **aplikacje & funkcje** i upewnij się, że jest zainstalowany **hosting systemu Windows Server.** Jeśli **hosting systemu Windows Server** nie znajduje się na liście zainstalowanych programów, pobierz i zainstaluj pakiet hostingowy .NET Core.

  [Aktualny instalator pakietu hostingu .NET Core (pobieranie bezpośrednie)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Aby uzyskać więcej informacji, zobacz [Instalowanie pakietu hostingowego .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Upewnij się, że **tożsamość** **modelu procesu** > **puli** > aplikacji jest ustawiona na **ApplicationPoolIdentity** lub tożsamość niestandardowa ma odpowiednie uprawnienia dostępu do folderu wdrażania aplikacji.

* Jeśli odinstalowałeś ASP.NET Core Hosting Bundle i zainstalowałeś wcześniejszą wersję pakietu hostingowego, plik *applicationHost.config* nie zawiera sekcji dla ASP.NET Core Module. Otwórz *plik applicationHost.config* w *%windir%/System32/inetsrv/config* i znajdź grupę `<configuration><configSections><sectionGroup name="system.webServer">` sekcji. Jeśli w grupie sekcji brakuje sekcji ASP.NET Core Module, dodaj element sekcji:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Alternatywnie, zainstaluj najnowszą wersję ASP.NET Core Hosting Bundle. Najnowsza wersja jest wstecznie kompatybilna z obsługiwanymi aplikacjami ASP.NET Core.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>Niepoprawny procesPath, brak zmiennej PATH, Nie zainstalowano pakietu hostingowego, system/usługi IIS nie zostały ponownie uruchomione, nieinstalowane vc++ redystrybucyjne lub naruszenie zasad dostępu dotnet.exe

* **Przeglądarka:** Błąd HTTP 500.0 — błąd obciążenia obsługi w trakcie programu ANCM

* **Dziennik aplikacji:** Aplikacja 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' z fizycznym\{katalogiem głównym 'C: PATH}\' nie może rozpocząć procesu z wierszem polecenia '"{...}" ", Kod Błędu = '0x80070002 : 0. Nie można uruchomić aplikacji "{PATH}". Nie znaleziono pliku wykonywalnego w polu '{PATH}'. Nie można uruchomić aplikacji "/LM/W3SVC/2/ROOT", kod błędu "0x8007023e".

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika nie jest tworzony.

* **ASP.NET dziennik debugowania modułu rdzenia:** Dziennik zdarzeń: nie można uruchomić aplikacji "{PATH}". Nie znaleziono pliku wykonywalnego w polu '{PATH}'. Nie powiodło się HRESULT zwrócone: 0x8007023e

Rozwiązywanie problemów:

* Upewnij się, że aplikacja działa lokalnie na Kestrel. Błąd procesu może być wynikiem problemu w aplikacji. Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.

* Sprawdź *processPath* atrybut elementu `<aspNetCore>` w *web.config,* aby potwierdzić, że jest `dotnet` to dla wdrożenia `.\{ASSEMBLY}.exe` zależnego od struktury (FDD) lub [dla samodzielnego wdrożenia (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).

* W przypadku pliku FDD *program dotnet.exe* może nie być dostępny za pośrednictwem ustawień PATH. Upewnij się, że w ustawieniach ścieżki systemowej istnieje *C:\Program Files\dotnet.\\ *

* W przypadku pliku FDD *program dotnet.exe* może nie być dostępny dla tożsamości użytkownika puli aplikacji. Upewnij się, że tożsamość użytkownika puli aplikacji ma dostęp do katalogu *C:\Program Files\dotnet.* Upewnij się, że nie ma reguł odmowy skonfigurowanych dla tożsamości użytkownika puli aplikacji w *katalogach C:\Program Files\dotnet* i app.

* Mogło zostać wdrożone fdd i .NET Core zainstalowane bez ponownego uruchamiania IIS. Uruchom ponownie serwer lub uruchom ponownie usługi IIS przez wykonanie **net stop było /y,** a następnie **net start w3svc** z wiersza polecenia.

* FDD może zostały wdrożone bez instalowania środowiska uruchomieniowego .NET Core w systemie hostingu. Jeśli środowisko uruchomieniowe .NET Core nie zostało zainstalowane, uruchom **instalator pakietu hostingowego .NET Core** w systemie.

  [Aktualny instalator pakietu hostingu .NET Core (pobieranie bezpośrednie)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Aby uzyskać więcej informacji, zobacz [Instalowanie pakietu hostingowego .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Jeśli wymagane jest określone środowisko wykonawcze, pobierz środowisko wykonawcze z [archiwum pobierania platformy .NET](https://dotnet.microsoft.com/download/archives) i zainstaluj je w systemie. Zakończ instalację, uruchamiając ponownie system lub ponownie uruchamiając usługę IIS, wykonując **net stop,** a następnie **net start w3svc** z wiersza polecenia.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Nieprawidłowe \<argumenty elementu> aspNetCore

* **Przeglądarka:** Błąd HTTP 500.0 — błąd obciążenia obsługi w trakcie programu ANCM

* **Dziennik aplikacji:** Wywoływanie hostfxr, aby znaleźć program obsługi żądań inprocess nie powiodło się bez znalezienia żadnych natywnych zależności. Najprawdopodobniej oznacza to, że aplikacja jest nieprawidłowo skonfigurowana, sprawdź wersje microsoft.NetCore.App i Microsoft.AspNetCore.App, które są przeznaczone dla aplikacji i są zainstalowane na komputerze. Nie można odnaleźć obsługi żądań inprocess. Przechwycone dane wyjściowe z wywoływania hostfxr: Czy chcesz uruchomić polecenia SDK dotnet? Zainstaluj dotnet SDK https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 z: Nie można uruchomić aplikacji "/LM/W3SVC/3/ROOT", Kod błędu "0x8000ffff".

* **ASP.NET dziennik stdout modułu rdzenia:** Czy chcesz uruchomić polecenia dotnet SDK? Zainstaluj dotnet SDK z:https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409

* **ASP.NET dziennik debugowania modułu rdzenia:** Wywoływanie hostfxr, aby znaleźć program obsługi żądań inprocess nie powiodło się bez znalezienia żadnych natywnych zależności. Najprawdopodobniej oznacza to, że aplikacja jest nieprawidłowo skonfigurowana, sprawdź wersje microsoft.NetCore.App i Microsoft.AspNetCore.App, które są przeznaczone dla aplikacji i są zainstalowane na komputerze. Nie powiodło się HRESULT zwrócone: 0x8000ffffff nie można znaleźć obsługi żądań inprocess. Przechwycone dane wyjściowe z wywoływania hostfxr: Czy chcesz uruchomić polecenia SDK dotnet? Zainstaluj dotnet SDK https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 z: Failed HRESULT zwrócony: 0x8000ffff

Rozwiązywanie problemów:

* Upewnij się, że aplikacja działa lokalnie na Kestrel. Błąd procesu może być wynikiem problemu w aplikacji. Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.

* Sprawdź atrybut *arguments* w `<aspNetCore>` elemencie w *web.config,* aby potwierdzić, że jest to (a) `.\{ASSEMBLY}.dll` dla wdrożenia zależnego od struktury (FDD); lub (b) nie obecny,`arguments=""`pusty ciąg ( ) lub lista`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`argumentów aplikacji ( ) dla samodzielnego wdrożenia (SCD).

## <a name="missing-net-core-shared-framework"></a>Brak udostępnionej struktury .NET Core

* **Przeglądarka:** Błąd HTTP 500.0 — błąd obciążenia obsługi w trakcie programu ANCM

* **Dziennik aplikacji:** Wywoływanie hostfxr, aby znaleźć program obsługi żądań inprocess nie powiodło się bez znalezienia żadnych natywnych zależności. Najprawdopodobniej oznacza to, że aplikacja jest nieprawidłowo skonfigurowana, sprawdź wersje microsoft.NetCore.App i Microsoft.AspNetCore.App, które są przeznaczone dla aplikacji i są zainstalowane na komputerze. Nie można odnaleźć obsługi żądań inprocess. Przechwycone dane wyjściowe z wywoływania hostfxr: Nie można było znaleźć żadnej zgodnej wersji frameworka. Nie znaleziono określonej struktury "Microsoft.AspNetCore.App", wersja "{VERSION}".

Nie można uruchomić aplikacji '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.

* **ASP.NET dziennik stdout modułu rdzenia:** Nie można było znaleźć żadnej zgodnej wersji ramowej. Nie znaleziono określonej struktury "Microsoft.AspNetCore.App", wersja "{VERSION}".

* **ASP.NET dziennik debugowania modułu rdzenia:** Nie powiodło się HRESULT zwrócony: 0x8000ffff

Rozwiązywanie problemów:

W przypadku wdrożenia zależnego od struktury (FDD) upewnij się, że w systemie zainstalowano odpowiednie środowisko wykonawcze.

## <a name="stopped-application-pool"></a>Zatrzymana pula aplikacji

* **Przeglądarka:** Usługa 503 niedostępna

* **Dziennik aplikacji:** Brak wpisu

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika nie jest tworzony.

* **ASP.NET dziennik debugowania modułu rdzenia:** Plik dziennika nie jest tworzony.

Rozwiązywanie problemów:

Upewnij się, że pula aplikacji nie jest w stanie *zatrzymane.*

## <a name="sub-application-includes-a-handlers-section"></a>Podulasz zawiera \<sekcję> programu obsługi

* **Przeglądarka:** Błąd HTTP 500.19 — wewnętrzny błąd serwera

* **Dziennik aplikacji:** Brak wpisu

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika aplikacji głównej jest tworzony i pokazuje normalne działanie. Plik dziennika aplikacji podrzędnej nie jest tworzony.

* **ASP.NET dziennik debugowania modułu rdzenia:** Plik dziennika aplikacji głównej jest tworzony i pokazuje normalne działanie. Plik dziennika aplikacji podrzędnej nie jest tworzony.

Rozwiązywanie problemów:

Upewnij się, że plik *web.config* aplikacji podkoszowej nie zawiera `<handlers>` sekcji lub że podzaukonia nie dziedziczy obsługi aplikacji nadrzędnej.

Sekcja aplikacji nadrzędnej `<system.webServer>` *web.config* jest umieszczana `<location>` wewnątrz elementu. Właściwość jest <xref:System.Configuration.SectionInformation.InheritInChildApplications*> `false` ustawiona, aby wskazać, że ustawienia określone w [ \<lokalizacji>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nie są dziedziczone przez aplikacje, które znajdują się w podkatalogu aplikacji nadrzędnej. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.

## <a name="stdout-log-path-incorrect"></a>nieprawidłowa ścieżka dziennika stdout

* **Przeglądarka:** Aplikacja odpowiada normalnie.

* **Dziennik aplikacji:** Nie można uruchomić przekierowania stdout w pliku C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Komunikat o wyjątku: HRESULT 0x80070005 zwrócony z {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84. Nie można zatrzymać przekierowania stdout w pliku C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Komunikat o wyjątku: HRESULT 0x80070002 zwrócony w {PATH}. Nie można uruchomić przekierowania stdout w {PATH}\aspnetcorev2_inprocess.dll.

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika nie jest tworzony.

* **ASP.NET dziennik debugowania modułu rdzenia:** Nie można uruchomić przekierowania stdout w pliku C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Komunikat o wyjątku: HRESULT 0x80070005 zwrócony z {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84. Nie można zatrzymać przekierowania stdout w pliku C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Komunikat o wyjątku: HRESULT 0x80070002 zwrócony w {PATH}. Nie można uruchomić przekierowania stdout w {PATH}\aspnetcorev2_inprocess.dll.

Rozwiązywanie problemów:

* Ścieżka `stdoutLogFile` określona `<aspNetCore>` w elemencie *web.config* nie istnieje. Aby uzyskać więcej informacji, zobacz [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* Użytkownik puli aplikacji nie ma dostępu do zapisu do ścieżki dziennika stdout.

## <a name="application-configuration-general-issue"></a>Ogólny problem konfiguracji aplikacji

* **Przeglądarka:** Błąd HTTP 500.0 — błąd ładowania obsługi w trakcie programu ANCM **--OR--** Błąd HTTP 500.30 — niepowodzenie uruchamiania w trakcie ancm

* **Dziennik aplikacji:** Zmiennej

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika jest tworzony, ale pusty lub utworzony z normalnymi wpisami, aż do momentu awarii aplikacji.

* **ASP.NET dziennik debugowania modułu rdzenia:** Zmiennej

Rozwiązywanie problemów:

Nie można uruchomić procesu, najprawdopodobniej z powodu problemu z konfiguracją aplikacji lub programowaniem.

Aby uzyskać więcej informacji, zobacz następujące tematy:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

W tym temacie opisano typowe błędy i zawiera porady dotyczące rozwiązywania problemów z określonymi błędami podczas hostowania aplikacji ASP.NET Core w usłudze Azure Apps Service i usługach IIS.

Aby uzyskać ogólne wskazówki dotyczące rozwiązywania problemów, zobacz <xref:test/troubleshoot-azure-iis>.

Zbierz wymienione poniżej informacje.

* Zachowanie przeglądarki (kod stanu i komunikat o błędzie)
* Wpisy dziennika zdarzeń aplikacji
  * Usługa Azure &ndash; <xref:test/troubleshoot-azure-iis>App Service Zobacz .
  * IIS
    1. Wybierz **polecenie Start** w menu **Windows,** wpisz podgląd *zdarzeń*i naciśnij **klawisz Enter**.
    1. Po otwarciu **Podglądu zdarzeń** rozwiń pozycję **Aplikacja** **dzienników** > systemu Windows na pasku bocznym.
* Element stdout modułu ASP.NET Core i wpisy dziennika debugowania
  * Usługa Azure &ndash; <xref:test/troubleshoot-azure-iis>App Service Zobacz .
  * Usługi IIS &ndash; Postępuj zgodnie z instrukcjami w sekcji [Tworzenie i przekierowywanie dziennika](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) dziennika dziennika oraz [Rozszerzone dzienniki diagnostyczne](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) w temacie ASP.NET Moduł podstawowy.

Porównaj informacje o błędach z następującymi typowymi błędami. Jeśli zostanie znalezione dopasowanie, postępuj zgodnie z poradami dotyczącymi rozwiązywania problemów.

Lista błędów w tym temacie nie jest wyczerpująca. Jeśli wystąpi błąd, którego nie ma na liście, otwórz nowy problem za pomocą przycisku **Opinie o zawartości** u dołu tego tematu ze szczegółowymi instrukcjami dotyczącymi odtworzenia błędu.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>Aktualizacja systemu operacyjnego usunęła 32-bitowy moduł ASP.NET core

**Dziennik aplikacji:** Nie można załadować modułu DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll.** Dane są błędem.

Rozwiązywanie problemów:

Pliki inne niż system operacyjny w katalogu **C:\Windows\SysWOW64\inetsrv** nie są zachowywane podczas uaktualniania systemu operacyjnego. Jeśli moduł ASP.NET Core jest zainstalowany przed uaktualnieniem systemu operacyjnego, a następnie każda pula aplikacji jest uruchamiana w trybie 32-bitowym po uaktualnieniu systemu operacyjnego, ten problem występuje. Po uaktualnieniu systemu operacyjnego napraw moduł ASP.NET Core. Zobacz [Instalowanie pakietu hostingu .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Wybierz **opcję Napraw** po uruchomieniu instalatora.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Brak rozszerzenia lokacji, 32-bitowe (x86) i 64-bitowe (x64) rozszerzenia lokacji lub nieprawidłowy zestaw bitowości procesu

*Dotyczy aplikacji hostowanych przez usługi Azure App Services.*

* **Przeglądarka:** Błąd HTTP 500.0 — błąd obciążenia obsługi w trakcie programu ANCM

* **Dziennik aplikacji:** Wywoływanie hostfxr, aby znaleźć program obsługi żądań inprocess nie powiodło się bez znalezienia żadnych natywnych zależności. Nie można odnaleźć obsługi żądań inprocess. Przechwycone dane wyjściowe z wywoływania hostfxr: Nie można było znaleźć żadnej zgodnej wersji frameworka. Nie znaleziono określonej struktury "Microsoft.AspNetCore.App", wersji\*'{VERSION}-preview- ' . Nie można uruchomić aplikacji "/LM/W3SVC/1416782824/ROOT", kod błędu "0x8000ffff".

* **ASP.NET dziennik stdout modułu rdzenia:** Nie można było znaleźć żadnej zgodnej wersji ramowej. Nie znaleziono określonej struktury "Microsoft.AspNetCore.App", wersji\*'{VERSION}-preview- ' .

Rozwiązywanie problemów:

* Jeśli aplikacja jest uruchomiona w czasie wykonywania w wersji zapoznawczej, zainstaluj rozszerzenie lokacji 32-bitowe (x86) **lub** 64-bitowe (x64), które odpowiada bitowości aplikacji i wersji środowiska wykonawczego aplikacji. **Nie należy instalować zarówno rozszerzenia lub wiele wersji środowiska wykonawczego rozszerzenia.**

  * ASP.NET Core {RUNTIME VERSION} (x86) Środowisko uruchomieniowe
  * ASP.NET Core {RUNTIME VERSION} (x64) Środowisko uruchomieniowe

  Uruchom ponownie aplikację. Poczekaj kilka sekund na ponowne uruchomienie aplikacji.

* Jeśli aplikacja jest uruchomiona w czasie wykonywania w wersji zapoznawczej i są zainstalowane [rozszerzenia lokacji](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) 32-bitowe (x86) i 64-bitowe (x64), odinstaluj rozszerzenie lokacji, które nie odpowiada bitowości aplikacji. Po usunięciu rozszerzenia witryny uruchom ponownie aplikację. Poczekaj kilka sekund na ponowne uruchomienie aplikacji.

* Jeśli uruchomienie aplikacji w czasie wykonywania w wersji zapoznawczej i bitowości rozszerzenia witryny odpowiada wersji aplikacji, upewnij się, że *wersja środowiska uruchomieniowego* rozszerzenia witryny w wersji podglądu jest zgodna z wersją środowiska uruchomieniowego aplikacji.

* Upewnij się, że **platforma** aplikacji w **ustawieniach aplikacji** pasuje do bitowości aplikacji.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Aplikacja x86 jest wdrażana, ale pula aplikacji nie jest włączona dla aplikacji 32-bitowych

* **Przeglądarka:** Błąd HTTP 500.30 — błąd uruchomienia w trakcie procesu ANCM

* **Dziennik aplikacji:** Aplikacja '/LM/W3SVC/5/ROOT' z fizycznym rootem "{PATH}" nawiedziła nieoczekiwany zarządzany wyjątek, kod wyjątku = '0xe0434352'. Sprawdź dzienniki stderr, aby uzyskać więcej informacji. Aplikacja "/LM/W3SVC/5/ROOT" z fizycznym katalogiem głównym "{PATH}" nie może załadować clr i aplikacji zarządzanej. Wątek roboczy CLR został przedwcześnie wyjęty

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika jest tworzony, ale pusty.

Ten scenariusz jest zalewkowany przez SDK podczas publikowania aplikacji niezależnej. SDK generuje błąd, `win10-x64` jeśli identyfikator RID nie pasuje do obiektu docelowego platformy (na przykład RID z `<PlatformTarget>x86</PlatformTarget>` w pliku projektu).

Rozwiązywanie problemów:

W przypadku wdrożenia zależnego od`<PlatformTarget>x86</PlatformTarget>`struktury x86 ( włącz pulę aplikacji usług IIS dla aplikacji 32-bitowych. W Menedżerze usług IIS otwórz **ustawienia zaawansowane** puli aplikacji i ustaw opcję Włącz **32-bitowe aplikacje** na **True**.

## <a name="platform-conflicts-with-rid"></a>Platforma powoduje konflikt z rid

* **Przeglądarka:** Błąd HTTP 502.5 - Błąd procesu

* **Dziennik aplikacji:** Aplikacja 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' z fizycznym\{katalogiem głównym 'C: PATH}\' nie\{może rozpocząć procesu z wierszem polecenia '"C: PATH}{ASSEMBLY}. {exe|dll}" ', ErrorCode = '0x80004005 : ff.

* **ASP.NET dziennik stdout modułu rdzenia:** Nieobsługiwał się wyjątek: System.BadImageFormatException: Nie można załadować pliku lub zestawu "{ASSEMBLY}.dll". Podjęto próbę załadowania programu o nieprawidłowym formacie.

Rozwiązywanie problemów:

* Upewnij się, że aplikacja działa lokalnie na Kestrel. Błąd procesu może być wynikiem problemu w aplikacji. Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.

* Jeśli ten wyjątek występuje podczas wdrażania usługi Azure Apps podczas uaktualniania aplikacji i wdrażania nowszych zestawów, ręcznie usuń wszystkie pliki z poprzedniego wdrożenia. Pokutowanie niezgodnych zestawów może `System.BadImageFormatException` spowodować wyjątek podczas wdrażania uaktualnionej aplikacji.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>Nieprawidłowa lub zatrzymana witryna internetowa punktu końcowego URI

* **Przeglądarka:** ERR_CONNECTION_REFUSED **--OR--** Nie można nawiązać połączenia

* **Dziennik aplikacji:** Brak wpisu

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika nie jest tworzony.

Rozwiązywanie problemów:

* Upewnij się, że używany jest poprawny punkt końcowy URI dla aplikacji. Sprawdź powiązania.

* Upewnij się, że witryna sieci Web iIS nie jest w stanie *Zatrzymane.*

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>Funkcje serwera CoreWebEngine lub W3SVC wyłączone

**Wyjątek systemu operacyjnego:** Funkcje IIS 7.0 CoreWebEngine i W3SVC muszą być zainstalowane, aby korzystać z modułu ASP.NET Core.

Rozwiązywanie problemów:

Upewnij się, że właściwa rola i funkcje są włączone. Zobacz [Konfiguracja usług IIS](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Brak niepoprawnej ścieżki fizycznej lub aplikacji witryny

* **Przeglądarka:** 403 Zabronione - Odmowa dostępu **--OR--** 403.14 Zabronione - Serwer sieci Web jest skonfigurowany tak, aby nie wymieniać zawartości tego katalogu.

* **Dziennik aplikacji:** Brak wpisu

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika nie jest tworzony.

Rozwiązywanie problemów:

Sprawdź **podstawowe ustawienia** witryny IIS i folder aplikacji fizycznej. Upewnij się, że aplikacja znajduje się w folderze w witrynie IIS **Ścieżka fizyczna**.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Nieprawidłowa rola, ASP.NET nie zainstalowano modułu rdzenia lub nieprawidłowe uprawnienia

* **Przeglądarka:** 500.19 Wewnętrzny błąd serwera — nie można uzyskać dostępu do żądanej strony, ponieważ powiązane dane konfiguracyjne strony są nieprawidłowe. **--OR--** Nie można wyświetlić tej strony

* **Dziennik aplikacji:** Brak wpisu

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika nie jest tworzony.

Rozwiązywanie problemów:

* Upewnij się, że właściwa rola jest włączona. Zobacz [Konfiguracja usług IIS](xref:host-and-deploy/iis/index#iis-configuration).

* Otwórz **funkcje & programy** lub **aplikacje & funkcje** i upewnij się, że jest zainstalowany **hosting systemu Windows Server.** Jeśli **hosting systemu Windows Server** nie znajduje się na liście zainstalowanych programów, pobierz i zainstaluj pakiet hostingowy .NET Core.

  [Aktualny instalator pakietu hostingu .NET Core (pobieranie bezpośrednie)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Aby uzyskać więcej informacji, zobacz [Instalowanie pakietu hostingowego .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Upewnij się, że **tożsamość** **modelu procesu** > **puli** > aplikacji jest ustawiona na **ApplicationPoolIdentity** lub tożsamość niestandardowa ma odpowiednie uprawnienia dostępu do folderu wdrażania aplikacji.

* Jeśli odinstalowałeś ASP.NET Core Hosting Bundle i zainstalowałeś wcześniejszą wersję pakietu hostingowego, plik *applicationHost.config* nie zawiera sekcji dla ASP.NET Core Module. Otwórz *plik applicationHost.config* w *%windir%/System32/inetsrv/config* i znajdź grupę `<configuration><configSections><sectionGroup name="system.webServer">` sekcji. Jeśli w grupie sekcji brakuje sekcji ASP.NET Core Module, dodaj element sekcji:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Alternatywnie, zainstaluj najnowszą wersję ASP.NET Core Hosting Bundle. Najnowsza wersja jest wstecznie kompatybilna z obsługiwanymi aplikacjami ASP.NET Core.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>Niepoprawny procesPath, brak zmiennej PATH, Nie zainstalowano pakietu hostingowego, system/usługi IIS nie zostały ponownie uruchomione, nieinstalowane vc++ redystrybucyjne lub naruszenie zasad dostępu dotnet.exe

* **Przeglądarka:** Błąd HTTP 502.5 - Błąd procesu

* **Dziennik aplikacji:** Aplikacja 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' z fizycznym\{katalogiem głównym 'C: PATH}\' nie może rozpocząć procesu z wierszem polecenia '"{...}" ", Kod Błędu = '0x80070002 : 0.

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika jest tworzony, ale pusty.

Rozwiązywanie problemów:

* Upewnij się, że aplikacja działa lokalnie na Kestrel. Błąd procesu może być wynikiem problemu w aplikacji. Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.

* Sprawdź *processPath* atrybut elementu `<aspNetCore>` w *web.config,* aby potwierdzić, że jest `dotnet` to dla wdrożenia `.\{ASSEMBLY}.exe` zależnego od struktury (FDD) lub [dla samodzielnego wdrożenia (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).

* W przypadku pliku FDD *program dotnet.exe* może nie być dostępny za pośrednictwem ustawień PATH. Upewnij się, że w ustawieniach ścieżki systemowej istnieje *C:\Program Files\dotnet.\\ *

* W przypadku pliku FDD *program dotnet.exe* może nie być dostępny dla tożsamości użytkownika puli aplikacji. Upewnij się, że tożsamość użytkownika puli aplikacji ma dostęp do katalogu *C:\Program Files\dotnet.* Upewnij się, że nie ma reguł odmowy skonfigurowanych dla tożsamości użytkownika puli aplikacji w *katalogach C:\Program Files\dotnet* i app.

* Mogło zostać wdrożone fdd i .NET Core zainstalowane bez ponownego uruchamiania IIS. Uruchom ponownie serwer lub uruchom ponownie usługi IIS przez wykonanie **net stop było /y,** a następnie **net start w3svc** z wiersza polecenia.

* FDD może zostały wdrożone bez instalowania środowiska uruchomieniowego .NET Core w systemie hostingu. Jeśli środowisko uruchomieniowe .NET Core nie zostało zainstalowane, uruchom **instalator pakietu hostingowego .NET Core** w systemie.

  [Aktualny instalator pakietu hostingu .NET Core (pobieranie bezpośrednie)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Aby uzyskać więcej informacji, zobacz [Instalowanie pakietu hostingowego .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Jeśli wymagane jest określone środowisko wykonawcze, pobierz środowisko wykonawcze z [archiwum pobierania platformy .NET](https://dotnet.microsoft.com/download/archives) i zainstaluj je w systemie. Zakończ instalację, uruchamiając ponownie system lub ponownie uruchamiając usługę IIS, wykonując **net stop,** a następnie **net start w3svc** z wiersza polecenia.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Nieprawidłowe \<argumenty elementu> aspNetCore

* **Przeglądarka:** Błąd HTTP 502.5 - Błąd procesu

* **Dziennik aplikacji:** Aplikacja 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' z fizycznym\{katalogiem głównym 'C: PATH}\' nie może rozpocząć procesu z wierszem polecenia '"dotnet' . \{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.

* **ASP.NET dziennik stdout modułu rdzenia:** Aplikacja do wykonania nie istnieje:\{'PATH ASSEMBLY}.dll'

Rozwiązywanie problemów:

* Upewnij się, że aplikacja działa lokalnie na Kestrel. Błąd procesu może być wynikiem problemu w aplikacji. Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.

* Sprawdź atrybut *arguments* w `<aspNetCore>` elemencie w *web.config,* aby potwierdzić, że jest to (a) `.\{ASSEMBLY}.dll` dla wdrożenia zależnego od struktury (FDD); lub (b) nie obecny,`arguments=""`pusty ciąg ( ) lub lista`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`argumentów aplikacji ( ) dla samodzielnego wdrożenia (SCD).

Rozwiązywanie problemów:

W przypadku wdrożenia zależnego od struktury (FDD) upewnij się, że w systemie zainstalowano odpowiednie środowisko wykonawcze.

## <a name="stopped-application-pool"></a>Zatrzymana pula aplikacji

* **Przeglądarka:** Usługa 503 niedostępna

* **Dziennik aplikacji:** Brak wpisu

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika nie jest tworzony.

Rozwiązywanie problemów:

Upewnij się, że pula aplikacji nie jest w stanie *zatrzymane.*

## <a name="sub-application-includes-a-handlers-section"></a>Podulasz zawiera \<sekcję> programu obsługi

* **Przeglądarka:** Błąd HTTP 500.19 — wewnętrzny błąd serwera

* **Dziennik aplikacji:** Brak wpisu

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika aplikacji głównej jest tworzony i pokazuje normalne działanie. Plik dziennika aplikacji podrzędnej nie jest tworzony.

Rozwiązywanie problemów:

Upewnij się, że plik *web.config* aplikacji podrzędnej `<handlers>` nie zawiera sekcji.

## <a name="stdout-log-path-incorrect"></a>nieprawidłowa ścieżka dziennika stdout

* **Przeglądarka:** Aplikacja odpowiada normalnie.

* **Dziennik aplikacji:** Ostrzeżenie: Nie można utworzyć stdoutLogFile? \\ \{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika nie jest tworzony.

Rozwiązywanie problemów:

* Ścieżka `stdoutLogFile` określona `<aspNetCore>` w elemencie *web.config* nie istnieje. Aby uzyskać więcej informacji, zobacz [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* Użytkownik puli aplikacji nie ma dostępu do zapisu do ścieżki dziennika stdout.

## <a name="application-configuration-general-issue"></a>Ogólny problem konfiguracji aplikacji

* **Przeglądarka:** Błąd HTTP 502.5 - Błąd procesu

* **Dziennik aplikacji:** Aplikacja 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' z fizycznym\{katalogiem głównym 'C: PATH}\' utworzyła proces z wierszem polecenia '"C:\{PATH}\{ASSEMBLY}. {exe|dll}" ale uległ awarii lub nie odpowiedział lub nie nasłuchał na danym porcie '{PORT}', ErrorCode = '{ERROR CODE}'

* **ASP.NET dziennik stdout modułu rdzenia:** Plik dziennika jest tworzony, ale pusty.

Rozwiązywanie problemów:

Nie można uruchomić procesu, najprawdopodobniej z powodu problemu z konfiguracją aplikacji lub programowaniem.

Aby uzyskać więcej informacji, zobacz następujące tematy:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end
