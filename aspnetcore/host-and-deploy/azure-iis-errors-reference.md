---
title: Informacje dotyczące typowych błędów dla Azure App Service i usług IIS z ASP.NET Core
author: rick-anderson
description: Uzyskaj porady dotyczące rozwiązywania problemów z typowymi błędami podczas hostowania aplikacji ASP.NET Core w usłudze Azure Apps i usługach IIS.
monikerRange: '>= aspnetcore-2.1'
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
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 5ec40181927ac491bbec473407609bafc34b78dc
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627419"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a>Informacje dotyczące typowych błędów dla Azure App Service i usług IIS z ASP.NET Core

::: moniker range=">= aspnetcore-2.2"

W tym temacie opisano typowe błędy i przedstawiono porady dotyczące rozwiązywania problemów w przypadku hostowania aplikacji ASP.NET Core w usłudze Azure Apps i usługach IIS.

Ogólne wskazówki dotyczące rozwiązywania problemów znajdują się w temacie <xref:test/troubleshoot-azure-iis> .

Zbierz wymienione poniżej informacje.

* Zachowanie przeglądarki (kod stanu i komunikat o błędzie)
* Wpisy dziennika zdarzeń aplikacji
  * Azure App Service: zobacz <xref:test/troubleshoot-azure-iis> .
  * IIS
    1. Wybierz pozycję **Rozpocznij** w menu **systemu Windows** , wpisz *Podgląd zdarzeń*i naciśnij klawisz **Enter**.
    1. Po otwarciu **Podgląd zdarzeń** rozwiń pozycję **Dzienniki systemu Windows** > **Application** na pasku bocznym.
* Element stdout modułu ASP.NET Core i wpisy dziennika debugowania
  * Azure App Service: zobacz <xref:test/troubleshoot-azure-iis> .
  * IIS: Postępuj zgodnie z instrukcjami w sekcjach [Tworzenie dziennika i przekierowanie](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) i [udoskonalone dzienniki diagnostyczne](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) w temacie ASP.NET Core Module.

Porównaj informacje o błędach z następującymi typowymi błędami. Jeśli zostanie znalezione dopasowanie, postępuj zgodnie z zaleceniami dotyczącymi rozwiązywania problemów.

Lista błędów w tym temacie nie jest wyczerpująca. Jeśli wystąpi błąd niewymieniony w tym miejscu, Otwórz nowy problem przy użyciu przycisku **opinii o zawartości** w dolnej części tego tematu ze szczegółowymi instrukcjami dotyczącymi sposobu odtworzenia błędu.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>Uaktualnienie systemu operacyjnego usunęło 32-bitowy moduł ASP.NET Core

**Dziennik aplikacji:** Nie można załadować **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** biblioteki DLL modułu. Dane są błędem.

Rozwiązywanie problemów:

Pliki inne niż systemowe w katalogu **C:\Windows\SysWOW64\inetsrv** nie są zachowywane podczas uaktualniania systemu operacyjnego. Jeśli moduł ASP.NET Core zostanie zainstalowany przed uaktualnieniem systemu operacyjnego, a następnie każda pula aplikacji zostanie uruchomiona w trybie 32-bitowym po uaktualnieniu systemu operacyjnego, ten problem wystąpi. Po uaktualnieniu systemu operacyjnego napraw moduł ASP.NET Core. Zobacz [Instalowanie pakietu hostingu platformy .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Wybierz pozycję **napraw** , gdy Instalator zostanie uruchomiony.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Brak zainstalowanego rozszerzenia witryny, 32-bitowe (x86) i 64-bit (x64) lub niewłaściwy zestaw bitów procesu

*Dotyczy aplikacji hostowanych przez usługę Azure App Services.*

* **Przeglądarka:** Błąd HTTP 500,0 — błąd ładowania procedury obsługi ANCM w procesie

* **Dziennik aplikacji:** Wywoływanie hostfxr w celu znalezienia procedury obsługi żądania przetworzenia nie powiodło się bez wyszukiwania natywnych zależności. Nie można znaleźć procedury obsługi żądania nieprzetwarzania. Przechwycono dane wyjściowe z wywołania hostfxr: nie można odnaleźć żadnej zgodnej wersji platformy. Nie znaleziono określonej struktury "Microsoft. AspNetCore. app" w wersji "{VERSION}-Preview- \* ". Nie można uruchomić aplikacji "/LM/W3SVC/1416782824/ROOT", ErrorCode "0x8000FFFF".

* **Dziennik stdout modułu ASP.NET Core:** Nie można znaleźć żadnej zgodnej wersji platformy. Nie znaleziono określonej struktury "Microsoft. AspNetCore. app" w wersji "{VERSION}-Preview- \* ".

* **Dziennik debugowania modułu ASP.NET Core:** Wywoływanie hostfxr w celu znalezienia procedury obsługi żądania przetworzenia nie powiodło się bez wyszukiwania natywnych zależności. Najbardziej prawdopodobną przyczyną jest to, że aplikacja jest nieprawidłowo skonfigurowana. Sprawdź wersje programów Microsoft. servicecore. app i Microsoft. AspNetCore. app, które są przeznaczone dla aplikacji, i są zainstalowane na komputerze. Zwrócony błąd HRESULT: 0x8000FFFF. Nie można znaleźć procedury obsługi żądania nieprzetwarzania. Nie można znaleźć żadnej zgodnej wersji platformy. Nie znaleziono określonej struktury "Microsoft. AspNetCore. app" w wersji "{VERSION}-Preview- \* ".

Rozwiązywanie problemów:

* Jeśli aplikacja jest uruchamiana w środowisku uruchomieniowym w wersji zapoznawczej, należy zainstalować rozszerzenie witryny 32-bitowe (x86) **lub** 64-bit (x64), które jest zgodne z bitową aplikacją i wersją środowiska uruchomieniowego aplikacji. **Nie instaluj obu rozszerzeń ani wielu wersji tego rozszerzenia.**

  * Środowisko uruchomieniowe ASP.NET Core {RUNTIME} (x86)
  * Środowisko uruchomieniowe ASP.NET Core {RUNTIME} (x64)

  Uruchom ponownie aplikację. Poczekaj kilka sekund, aż aplikacja zostanie ponownie uruchomiona.

* Jeśli aplikacja jest uruchamiana w środowisku uruchomieniowym wersji zapoznawczej, a zainstalowane są [rozszerzenia lokacji](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) 32-bitowe (x86) i 64-bitowe (x64), Odinstaluj rozszerzenie lokacji, które nie jest zgodne z bitową aplikacją. Po usunięciu rozszerzenia witryny należy ponownie uruchomić aplikację. Poczekaj kilka sekund, aż aplikacja zostanie ponownie uruchomiona.

* Jeśli aplikacja jest uruchamiana w środowisku uruchomieniowym w wersji zapoznawczej, a liczba bitów rozszerzenia lokacji jest zgodna z tą, upewnij się, że *wersja środowiska uruchomieniowego* rozszerzenia witryny w wersji zapoznawczej jest zgodna z wersją środowiska uruchomieniowego aplikacji.

* Upewnij się, że **platforma** aplikacji w **ustawieniach aplikacji** jest zgodna z bitową aplikacją.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Aplikacja x86 została wdrożona, ale Pula aplikacji nie jest włączona dla aplikacji 32-bitowych

* **Przeglądarka:** Błąd HTTP 500,30 — niepowodzenie uruchomienia ANCM w procesie

* **Dziennik aplikacji:** Aplikacja "/LM/W3SVC/5/ROOT" z fizycznym elementem głównym "{PATH}" napotkała nieoczekiwany wyjątek zarządzany, kod wyjątku = "0xe0434352". Aby uzyskać więcej informacji, Sprawdź dzienniki stderr. Aplikacja "/LM/W3SVC/5/ROOT" z fizycznym elementem głównym "{PATH}" nie może załadować środowiska CLR i aplikacji zarządzanej. Wątek roboczy CLR zakończony przedwcześnie

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika jest tworzony, ale pusty.

* **Dziennik debugowania modułu ASP.NET Core:** Zwrócony błąd HRESULT: 0x8007023e

Ten scenariusz jest zalewkowany przez zestaw SDK podczas publikowania aplikacji samodzielnej. Zestaw SDK generuje błąd, jeśli identyfikator RID nie jest zgodny z obiektem docelowym platformy (na przykład `win10-x64` RID z `<PlatformTarget>x86</PlatformTarget>` w pliku projektu).

Rozwiązywanie problemów:

W przypadku wdrożenia opartego na architekturze x86 ( `<PlatformTarget>x86</PlatformTarget>` ) Włącz pulę aplikacji usług IIS dla aplikacji 32-bitowych. W Menedżerze usług IIS Otwórz **Zaawansowane ustawienia** puli aplikacji i ustaw **wartość True**dla **aplikacji 32-bitowych** .

## <a name="platform-conflicts-with-rid"></a>Konflikty platformy z identyfikatorem RID

* **Przeglądarka:** Błąd HTTP 502,5 — niepowodzenie procesu

* **Dziennik aplikacji:** Nie można uruchomić procesu "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}" z fizycznym elementem głównym "C: Path}" \{ \' przy użyciu wiersza polecenia "" c: \{ Path} {Assembly}. { exe | dll} "", ErrorCode = "0x80004005: FF.

* **Dziennik stdout modułu ASP.NET Core:** Nieobsługiwany wyjątek: System. BadImageFormatException: nie można załadować pliku lub zestawu "{ASSEMBLY}. dll". Podjęto próbę załadowania programu z nieprawidłowym formatem.

Rozwiązywanie problemów:

* Upewnij się, że aplikacja działa lokalnie na Kestrel. Niepowodzenie procesu może być wynikiem problemu w aplikacji. Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.

* Jeśli ten wyjątek występuje w przypadku wdrożenia aplikacji platformy Azure podczas uaktualniania aplikacji i wdrażania nowszych zestawów, ręcznie usuń wszystkie pliki z wcześniejszego wdrożenia. Niezgodne zestawy mogą powodować `System.BadImageFormatException` wyjątek podczas wdrażania uaktualnionej aplikacji.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>Nieprawidłowy punkt końcowy identyfikatora URI lub zatrzymano witrynę sieci Web

* **Przeglądarka:** ERR_CONNECTION_REFUSED **--lub--** nie można nawiązać połączenia

* **Dziennik aplikacji:** Brak wpisu

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika nie został utworzony.

* **Dziennik debugowania modułu ASP.NET Core:** Plik dziennika nie został utworzony.

Rozwiązywanie problemów:

* Potwierdź, że prawidłowy punkt końcowy URI aplikacji jest używany. Sprawdź powiązania.

* Upewnij się, że witryna internetowa usług IIS nie jest w stanie *zatrzymania* .

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>Funkcje serwera CoreWebEngine lub W3SVC są wyłączone

**Wyjątek systemu operacyjnego:** Aby można było używać modułu ASP.NET Core, należy zainstalować funkcje CoreWebEngine i W3SVC usług IIS 7,0.

Rozwiązywanie problemów:

Upewnij się, że są włączone odpowiednie role i funkcje. Zobacz [Konfiguracja usług IIS](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Brak nieprawidłowej ścieżki fizycznej witryny sieci Web lub aplikacji

* **Przeglądarka:** 403 zabroniony — odmowa dostępu **--lub--** 403,14 zabronione — serwer sieci Web jest skonfigurowany do wyświetlania zawartości tego katalogu.

* **Dziennik aplikacji:** Brak wpisu

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika nie został utworzony.

* **Dziennik debugowania modułu ASP.NET Core:** Plik dziennika nie został utworzony.

Rozwiązywanie problemów:

Sprawdź **Ustawienia podstawowe** witryny sieci Web usług IIS i folder aplikacji fizycznych. Upewnij się, że aplikacja znajduje się w folderze w **ścieżce fizycznej**witryny sieci Web usług IIS.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Nieprawidłowa rola, moduł ASP.NET Core nie jest zainstalowany lub nieprawidłowe uprawnienia

* **Przeglądarka:** 500,19 wewnętrzny błąd serwera — nie można uzyskać dostępu do żądanej strony, ponieważ powiązane dane konfiguracji strony są nieprawidłowe. **--Lub--** Nie można wyświetlić tej strony

* **Dziennik aplikacji:** Brak wpisu

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika nie został utworzony.

* **Dziennik debugowania modułu ASP.NET Core:** Plik dziennika nie został utworzony.

Rozwiązywanie problemów:

* Upewnij się, że jest włączona właściwa rola. Zobacz [Konfiguracja usług IIS](xref:host-and-deploy/iis/index#iis-configuration).

* Otwórz aplet **programy & funkcje** lub **aplikacje & funkcje** i upewnij się, że **Host systemu Windows Server** jest zainstalowany. Jeśli **Host systemu Windows Server** nie znajduje się na liście zainstalowanych programów, Pobierz i zainstaluj pakiet hostingu platformy .NET Core.

  [Bieżący Instalator pakietu hostingu platformy .NET Core (Pobieranie bezpośrednie)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Aby uzyskać więcej informacji, zobacz [Instalowanie pakietu hostingu .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Upewnij się, że **Application Pool** > **model procesów** puli aplikacji > **Identity** ma wartość **ApplicationPool Identity ** lub że niestandardowa tożsamość ma odpowiednie uprawnienia dostępu do folderu wdrożenia aplikacji.

* Jeśli odinstalowano pakiet hostingu ASP.NET Core i zainstalowano wcześniejszą wersję pakietu hostingu, plik *applicationHost.config* nie zawiera sekcji dla modułu ASP.NET Core. Otwórz *applicationHost.config* w lokalizacji *% windir%/system32/inetsrv/config* i Znajdź `<configuration><configSections><sectionGroup name="system.webServer">` grupę sekcji. Jeśli w grupie sekcji brakuje sekcji modułu ASP.NET Core, Dodaj element Section:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Alternatywnie Zainstaluj najnowszą wersję pakietu hostingu ASP.NET Core. Najnowsza wersja jest wstecznie zgodna z obsługiwanymi ASP.NET Core aplikacjami.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>Nieprawidłowa processPath, brakująca zmienna PATH, pakiet hostingu nie został zainstalowany, nie uruchomiono systemu/usług IIS, pakiet redystrybucyjny programu VC + + nie został zainstalowany lub dotnet.exe naruszenie dostępu

* **Przeglądarka:** Błąd HTTP 500,0 — błąd ładowania procedury obsługi ANCM w procesie

* **Dziennik aplikacji:** Nie można uruchomić procesu "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}" z fizycznym elementem głównym "C: \{ Path} \' " przy użyciu wiersza polecenia "" {...} " ", ErrorCode =" 0x80070002:0. Aplikacja "{PATH}" nie mogła zostać uruchomiona. Nie znaleziono pliku wykonywalnego w lokalizacji "{PATH}". Nie można uruchomić aplikacji "/LM/W3SVC/2/ROOT", ErrorCode "0x8007023e".

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika nie został utworzony.

* **Dziennik debugowania modułu ASP.NET Core:** Dziennik zdarzeń: aplikacja "{PATH}" nie mogła zostać uruchomiona. Nie znaleziono pliku wykonywalnego w lokalizacji "{PATH}". Zwrócony błąd HRESULT: 0x8007023e

Rozwiązywanie problemów:

* Upewnij się, że aplikacja działa lokalnie na Kestrel. Niepowodzenie procesu może być wynikiem problemu w aplikacji. Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.

* Sprawdź atrybut *processPath* w `<aspNetCore>` elemencie w *web.config* , aby upewnić się, że jest to `dotnet` wdrożenie zależne od platformy (FDD) lub `.\{ASSEMBLY}.exe` [wdrożenie samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).

* W przypadku elementu FDD *dotnet.exe* może być niedostępna za pośrednictwem ustawień ścieżki. Upewnij się, że w ustawieniach ścieżki systemowej istnieje wartość *C:\Program Files\dotnet \\ * .

* W przypadku elementu FDD *dotnet.exe* może być niedostępna dla tożsamości użytkownika puli aplikacji. Upewnij się, że tożsamość użytkownika puli aplikacji ma dostęp do katalogu *C:\Program Files\dotnet* . Upewnij się, że nie ma skonfigurowanych reguł odmowy dla tożsamości użytkownika puli aplikacji w *folderze C:\Program Files\dotnet* i katalogach aplikacji.

* FDD mógł zostać wdrożony, a platforma .NET Core została zainstalowana bez ponownego uruchamiania usług IIS. Uruchom ponownie serwer lub Uruchom ponownie usługi IIS, wykonując polecenie **net stop** , a następnie **net start W3SVC** z wiersza polecenia.

* FDD mógł zostać wdrożony bez instalowania środowiska uruchomieniowego .NET Core w systemie hostingu. Jeśli środowisko uruchomieniowe platformy .NET Core nie zostało zainstalowane, uruchom **Instalatora pakietu hostingu programu .NET Core** w systemie.

  [Bieżący Instalator pakietu hostingu platformy .NET Core (Pobieranie bezpośrednie)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Aby uzyskać więcej informacji, zobacz [Instalowanie pakietu hostingu .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Jeśli jest wymagane określone środowisko uruchomieniowe, Pobierz środowisko uruchomieniowe z [archiwów pobierania programu .NET](https://dotnet.microsoft.com/download/archives) i zainstaluj je w systemie. Aby ukończyć instalację, należy ponownie uruchomić system lub ponownie uruchomić usługi IIS, wykonując polecenie **net stop** , a następnie **net start W3SVC** z wiersza polecenia.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Nieprawidłowe argumenty \<aspNetCore> elementu

* **Przeglądarka:** Błąd HTTP 500,0 — błąd ładowania procedury obsługi ANCM w procesie

* **Dziennik aplikacji:** Wywoływanie hostfxr w celu znalezienia procedury obsługi żądania przetworzenia nie powiodło się bez wyszukiwania natywnych zależności. Najbardziej prawdopodobną przyczyną jest to, że aplikacja jest nieprawidłowo skonfigurowana. Sprawdź wersje programów Microsoft. servicecore. app i Microsoft. AspNetCore. app, które są przeznaczone dla aplikacji, i są zainstalowane na komputerze. Nie można znaleźć procedury obsługi żądania nieprzetwarzania. Przechwycone dane wyjściowe z wywołania hostfxr: Czy chodziło o uruchamianie poleceń zestawu dotnet SDK? Zainstaluj zestaw SDK dotnet z: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 nie można uruchomić aplikacji "/LM/W3SVC/3/root", ErrorCode "0x8000FFFF".

* **Dziennik stdout modułu ASP.NET Core:** Czy chodziło o uruchamianie poleceń zestawu SDK dotnet? Zainstaluj zestaw SDK dotnet z: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409

* **Dziennik debugowania modułu ASP.NET Core:** Wywoływanie hostfxr w celu znalezienia procedury obsługi żądania przetworzenia nie powiodło się bez wyszukiwania natywnych zależności. Najbardziej prawdopodobną przyczyną jest to, że aplikacja jest nieprawidłowo skonfigurowana. Sprawdź wersje programów Microsoft. servicecore. app i Microsoft. AspNetCore. app, które są przeznaczone dla aplikacji, i są zainstalowane na komputerze. Zwrócony błąd HRESULT: 0x8000FFFF nie może odnaleźć procedury obsługi żądania unprocess. Przechwycone dane wyjściowe z wywołania hostfxr: Czy chodziło o uruchamianie poleceń zestawu dotnet SDK? Zainstaluj zestaw SDK dotnet z: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 zwrócony błąd HRESULT: 0x8000FFFF

Rozwiązywanie problemów:

* Upewnij się, że aplikacja działa lokalnie na Kestrel. Niepowodzenie procesu może być wynikiem problemu w aplikacji. Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.

* Sprawdź atrybut *arguments* `<aspNetCore>` elementu w *web.config* , aby upewnić się, że jest (a) `.\{ASSEMBLY}.dll` dla wdrożenia zależnego od platformy (FDD); lub (b) nie istnieje, pusty ciąg ( `arguments=""` ) lub lista argumentów aplikacji ( `arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"` ) dla wdrożenia samodzielnego (SCD).

## <a name="missing-net-core-shared-framework"></a>Brak współdzielonej platformy .NET Core

* **Przeglądarka:** Błąd HTTP 500,0 — błąd ładowania procedury obsługi ANCM w procesie

* **Dziennik aplikacji:** Wywoływanie hostfxr w celu znalezienia procedury obsługi żądania przetworzenia nie powiodło się bez wyszukiwania natywnych zależności. Najbardziej prawdopodobną przyczyną jest to, że aplikacja jest nieprawidłowo skonfigurowana. Sprawdź wersje programów Microsoft. servicecore. app i Microsoft. AspNetCore. app, które są przeznaczone dla aplikacji, i są zainstalowane na komputerze. Nie można znaleźć procedury obsługi żądania nieprzetwarzania. Przechwycono dane wyjściowe z wywołania hostfxr: nie można odnaleźć żadnej zgodnej wersji platformy. Nie znaleziono określonej struktury "Microsoft. AspNetCore. app" w wersji "{VERSION}".

Nie można uruchomić aplikacji "/LM/W3SVC/5/ROOT", ErrorCode "0x8000FFFF".

* **Dziennik stdout modułu ASP.NET Core:** Nie można znaleźć żadnej zgodnej wersji platformy. Nie znaleziono określonej struktury "Microsoft. AspNetCore. app" w wersji "{VERSION}".

* **Dziennik debugowania modułu ASP.NET Core:** Zwrócony błąd HRESULT: 0x8000FFFF

Rozwiązywanie problemów:

W przypadku wdrażania zależnego od platformy (FDD) Upewnij się, że w systemie jest zainstalowane odpowiednie środowisko uruchomieniowe.

## <a name="stopped-application-pool"></a>Zatrzymana Pula aplikacji

* **Przeglądarka:** usługa 503 jest niedostępna

* **Dziennik aplikacji:** Brak wpisu

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika nie został utworzony.

* **Dziennik debugowania modułu ASP.NET Core:** Plik dziennika nie został utworzony.

Rozwiązywanie problemów:

Upewnij się, że Pula aplikacji nie jest w stanie *zatrzymania* .

## <a name="sub-application-includes-a-handlers-section"></a>Podaplikacja zawiera \<handlers> sekcję

* **Przeglądarka:** Błąd HTTP 500,19 — wewnętrzny błąd serwera

* **Dziennik aplikacji:** Brak wpisu

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika aplikacji głównej jest tworzony i pokazuje normalną operację. Nie utworzono pliku dziennika aplikacji podrzędnej.

* **Dziennik debugowania modułu ASP.NET Core:** Plik dziennika aplikacji głównej jest tworzony i pokazuje normalną operację. Nie utworzono pliku dziennika aplikacji podrzędnej.

Rozwiązywanie problemów:

Upewnij się, że plik *web.config* aplikacji podrzędnej nie zawiera `<handlers>` sekcji lub że aplikacja podrzędna nie dziedziczy programów obsługi aplikacji nadrzędnej.

Sekcjaweb.configaplikacji nadrzędnej `<system.webServer>` jest *web.config* umieszczona wewnątrz `<location>` elementu. <xref:System.Configuration.SectionInformation.InheritInChildApplications*>Właściwość jest ustawiona na `false` , aby wskazać, że ustawienia określone w [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elemencie nie są dziedziczone przez aplikacje, które znajdują się w podkatalogu aplikacji nadrzędnej. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.

## <a name="stdout-log-path-incorrect"></a>Nieprawidłowa ścieżka dziennika stdout

* **Przeglądarka:** Aplikacja reaguje zwykle.

* **Dziennik aplikacji:** Nie można uruchomić przekierowania stdout w lokalizacji C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Komunikat wyjątku: HRESULT 0x80070005 zwrócony w {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp: 84. Nie można zatrzymać przekierowania strumienia stdout w lokalizacji C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Komunikat o wyjątku: HRESULT 0x80070002 zwrócony w {PATH}. Nie można uruchomić przekierowania stdout w ścieżce {PATH} \aspnetcorev2_inprocess.dll.

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika nie został utworzony.

* **Dziennik debugowania modułu ASP.NET Core:** Nie można uruchomić przekierowania stdout w lokalizacji C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Komunikat wyjątku: HRESULT 0x80070005 zwrócony w {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp: 84. Nie można zatrzymać przekierowania strumienia stdout w lokalizacji C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Komunikat o wyjątku: HRESULT 0x80070002 zwrócony w {PATH}. Nie można uruchomić przekierowania stdout w ścieżce {PATH} \aspnetcorev2_inprocess.dll.

Rozwiązywanie problemów:

* `stdoutLogFile`Ścieżka określona w `<aspNetCore>` elemencie *web.config* nie istnieje. Aby uzyskać więcej informacji, zobacz [ASP.NET Core Module: Tworzenie i przekierowywanie dzienników](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* Użytkownik puli aplikacji nie ma dostępu do zapisu w ścieżce dziennika stdout.

## <a name="application-configuration-general-issue"></a>Ogólny problem z konfiguracją aplikacji

* **Przeglądarka:** Błąd HTTP 500,0 — błąd ładowania procedury obsługi ANCM w procesie — **lub —** błąd HTTP 500,30-ANCM w procesie — niepowodzenie uruchamiania

* **Dziennik aplikacji:** Zmiennej

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika jest tworzony, ale pusty lub tworzony przy użyciu zwykłych wpisów do momentu awarii aplikacji.

* **Dziennik debugowania modułu ASP.NET Core:** Zmiennej

Rozwiązywanie problemów:

Nie można uruchomić procesu, prawdopodobnie z powodu problemu z konfiguracją lub programowaniem aplikacji.

Aby uzyskać więcej informacji, zobacz następujące tematy:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

W tym temacie opisano typowe błędy i przedstawiono porady dotyczące rozwiązywania problemów w przypadku hostowania aplikacji ASP.NET Core w usłudze Azure Apps i usługach IIS.

Ogólne wskazówki dotyczące rozwiązywania problemów znajdują się w temacie <xref:test/troubleshoot-azure-iis> .

Zbierz wymienione poniżej informacje.

* Zachowanie przeglądarki (kod stanu i komunikat o błędzie)
* Wpisy dziennika zdarzeń aplikacji
  * Azure App Service: zobacz <xref:test/troubleshoot-azure-iis> .
  * IIS
    1. Wybierz pozycję **Rozpocznij** w menu **systemu Windows** , wpisz *Podgląd zdarzeń*i naciśnij klawisz **Enter**.
    1. Po otwarciu **Podgląd zdarzeń** rozwiń pozycję **Dzienniki systemu Windows** > **Application** na pasku bocznym.
* Element stdout modułu ASP.NET Core i wpisy dziennika debugowania
  * Azure App Service: zobacz <xref:test/troubleshoot-azure-iis> .
  * IIS: Postępuj zgodnie z instrukcjami w sekcjach [Tworzenie dziennika i przekierowanie](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) i [udoskonalone dzienniki diagnostyczne](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) w temacie ASP.NET Core Module.

Porównaj informacje o błędach z następującymi typowymi błędami. Jeśli zostanie znalezione dopasowanie, postępuj zgodnie z zaleceniami dotyczącymi rozwiązywania problemów.

Lista błędów w tym temacie nie jest wyczerpująca. Jeśli wystąpi błąd niewymieniony w tym miejscu, Otwórz nowy problem przy użyciu przycisku **opinii o zawartości** w dolnej części tego tematu ze szczegółowymi instrukcjami dotyczącymi sposobu odtworzenia błędu.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>Uaktualnienie systemu operacyjnego usunęło 32-bitowy moduł ASP.NET Core

**Dziennik aplikacji:** Nie można załadować **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** biblioteki DLL modułu. Dane są błędem.

Rozwiązywanie problemów:

Pliki inne niż systemowe w katalogu **C:\Windows\SysWOW64\inetsrv** nie są zachowywane podczas uaktualniania systemu operacyjnego. Jeśli moduł ASP.NET Core zostanie zainstalowany przed uaktualnieniem systemu operacyjnego, a następnie każda pula aplikacji zostanie uruchomiona w trybie 32-bitowym po uaktualnieniu systemu operacyjnego, ten problem wystąpi. Po uaktualnieniu systemu operacyjnego napraw moduł ASP.NET Core. Zobacz [Instalowanie pakietu hostingu platformy .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Wybierz pozycję **napraw** , gdy Instalator zostanie uruchomiony.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Brak zainstalowanego rozszerzenia witryny, 32-bitowe (x86) i 64-bit (x64) lub niewłaściwy zestaw bitów procesu

*Dotyczy aplikacji hostowanych przez usługę Azure App Services.*

* **Przeglądarka:** Błąd HTTP 500,0 — błąd ładowania procedury obsługi ANCM w procesie

* **Dziennik aplikacji:** Wywoływanie hostfxr w celu znalezienia procedury obsługi żądania przetworzenia nie powiodło się bez wyszukiwania natywnych zależności. Nie można znaleźć procedury obsługi żądania nieprzetwarzania. Przechwycono dane wyjściowe z wywołania hostfxr: nie można odnaleźć żadnej zgodnej wersji platformy. Nie znaleziono określonej struktury "Microsoft. AspNetCore. app" w wersji "{VERSION}-Preview- \* ". Nie można uruchomić aplikacji "/LM/W3SVC/1416782824/ROOT", ErrorCode "0x8000FFFF".

* **Dziennik stdout modułu ASP.NET Core:** Nie można znaleźć żadnej zgodnej wersji platformy. Nie znaleziono określonej struktury "Microsoft. AspNetCore. app" w wersji "{VERSION}-Preview- \* ".

Rozwiązywanie problemów:

* Jeśli aplikacja jest uruchamiana w środowisku uruchomieniowym w wersji zapoznawczej, należy zainstalować rozszerzenie witryny 32-bitowe (x86) **lub** 64-bit (x64), które jest zgodne z bitową aplikacją i wersją środowiska uruchomieniowego aplikacji. **Nie instaluj obu rozszerzeń ani wielu wersji tego rozszerzenia.**

  * Środowisko uruchomieniowe ASP.NET Core {RUNTIME} (x86)
  * Środowisko uruchomieniowe ASP.NET Core {RUNTIME} (x64)

  Uruchom ponownie aplikację. Poczekaj kilka sekund, aż aplikacja zostanie ponownie uruchomiona.

* Jeśli aplikacja jest uruchamiana w środowisku uruchomieniowym wersji zapoznawczej, a zainstalowane są [rozszerzenia lokacji](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) 32-bitowe (x86) i 64-bitowe (x64), Odinstaluj rozszerzenie lokacji, które nie jest zgodne z bitową aplikacją. Po usunięciu rozszerzenia witryny należy ponownie uruchomić aplikację. Poczekaj kilka sekund, aż aplikacja zostanie ponownie uruchomiona.

* Jeśli aplikacja jest uruchamiana w środowisku uruchomieniowym w wersji zapoznawczej, a liczba bitów rozszerzenia lokacji jest zgodna z tą, upewnij się, że *wersja środowiska uruchomieniowego* rozszerzenia witryny w wersji zapoznawczej jest zgodna z wersją środowiska uruchomieniowego aplikacji.

* Upewnij się, że **platforma** aplikacji w **ustawieniach aplikacji** jest zgodna z bitową aplikacją.

Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Aplikacja x86 została wdrożona, ale Pula aplikacji nie jest włączona dla aplikacji 32-bitowych

* **Przeglądarka:** Błąd HTTP 500,30 — niepowodzenie uruchomienia ANCM w procesie

* **Dziennik aplikacji:** Aplikacja "/LM/W3SVC/5/ROOT" z fizycznym elementem głównym "{PATH}" napotkała nieoczekiwany wyjątek zarządzany, kod wyjątku = "0xe0434352". Aby uzyskać więcej informacji, Sprawdź dzienniki stderr. Aplikacja "/LM/W3SVC/5/ROOT" z fizycznym elementem głównym "{PATH}" nie może załadować środowiska CLR i aplikacji zarządzanej. Wątek roboczy CLR zakończony przedwcześnie

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika jest tworzony, ale pusty.

Ten scenariusz jest zalewkowany przez zestaw SDK podczas publikowania aplikacji samodzielnej. Zestaw SDK generuje błąd, jeśli identyfikator RID nie jest zgodny z obiektem docelowym platformy (na przykład `win10-x64` RID z `<PlatformTarget>x86</PlatformTarget>` w pliku projektu).

Rozwiązywanie problemów:

W przypadku wdrożenia opartego na architekturze x86 ( `<PlatformTarget>x86</PlatformTarget>` ) Włącz pulę aplikacji usług IIS dla aplikacji 32-bitowych. W Menedżerze usług IIS Otwórz **Zaawansowane ustawienia** puli aplikacji i ustaw **wartość True**dla **aplikacji 32-bitowych** .

## <a name="platform-conflicts-with-rid"></a>Konflikty platformy z identyfikatorem RID

* **Przeglądarka:** Błąd HTTP 502,5 — niepowodzenie procesu

* **Dziennik aplikacji:** Nie można uruchomić procesu "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}" z fizycznym elementem głównym "C: Path}" \{ \' przy użyciu wiersza polecenia "" c: \{ Path} {Assembly}. { exe | dll} "", ErrorCode = "0x80004005: FF.

* **Dziennik stdout modułu ASP.NET Core:** Nieobsługiwany wyjątek: System. BadImageFormatException: nie można załadować pliku lub zestawu "{ASSEMBLY}. dll". Podjęto próbę załadowania programu z nieprawidłowym formatem.

Rozwiązywanie problemów:

* Upewnij się, że aplikacja działa lokalnie na Kestrel. Niepowodzenie procesu może być wynikiem problemu w aplikacji. Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.

* Jeśli ten wyjątek występuje w przypadku wdrożenia aplikacji platformy Azure podczas uaktualniania aplikacji i wdrażania nowszych zestawów, ręcznie usuń wszystkie pliki z wcześniejszego wdrożenia. Niezgodne zestawy mogą powodować `System.BadImageFormatException` wyjątek podczas wdrażania uaktualnionej aplikacji.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>Nieprawidłowy punkt końcowy identyfikatora URI lub zatrzymano witrynę sieci Web

* **Przeglądarka:** ERR_CONNECTION_REFUSED **--lub--** nie można nawiązać połączenia

* **Dziennik aplikacji:** Brak wpisu

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika nie został utworzony.

Rozwiązywanie problemów:

* Potwierdź, że prawidłowy punkt końcowy URI aplikacji jest używany. Sprawdź powiązania.

* Upewnij się, że witryna internetowa usług IIS nie jest w stanie *zatrzymania* .

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>Funkcje serwera CoreWebEngine lub W3SVC są wyłączone

**Wyjątek systemu operacyjnego:** Aby można było używać modułu ASP.NET Core, należy zainstalować funkcje CoreWebEngine i W3SVC usług IIS 7,0.

Rozwiązywanie problemów:

Upewnij się, że są włączone odpowiednie role i funkcje. Zobacz [Konfiguracja usług IIS](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Brak nieprawidłowej ścieżki fizycznej witryny sieci Web lub aplikacji

* **Przeglądarka:** 403 zabroniony — odmowa dostępu **--lub--** 403,14 zabronione — serwer sieci Web jest skonfigurowany do wyświetlania zawartości tego katalogu.

* **Dziennik aplikacji:** Brak wpisu

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika nie został utworzony.

Rozwiązywanie problemów:

Sprawdź **Ustawienia podstawowe** witryny sieci Web usług IIS i folder aplikacji fizycznych. Upewnij się, że aplikacja znajduje się w folderze w **ścieżce fizycznej**witryny sieci Web usług IIS.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Nieprawidłowa rola, moduł ASP.NET Core nie jest zainstalowany lub nieprawidłowe uprawnienia

* **Przeglądarka:** 500,19 wewnętrzny błąd serwera — nie można uzyskać dostępu do żądanej strony, ponieważ powiązane dane konfiguracji strony są nieprawidłowe. **--Lub--** Nie można wyświetlić tej strony

* **Dziennik aplikacji:** Brak wpisu

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika nie został utworzony.

Rozwiązywanie problemów:

* Upewnij się, że jest włączona właściwa rola. Zobacz [Konfiguracja usług IIS](xref:host-and-deploy/iis/index#iis-configuration).

* Otwórz aplet **programy & funkcje** lub **aplikacje & funkcje** i upewnij się, że **Host systemu Windows Server** jest zainstalowany. Jeśli **Host systemu Windows Server** nie znajduje się na liście zainstalowanych programów, Pobierz i zainstaluj pakiet hostingu platformy .NET Core.

  [Bieżący Instalator pakietu hostingu platformy .NET Core (Pobieranie bezpośrednie)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Aby uzyskać więcej informacji, zobacz [Instalowanie pakietu hostingu .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Upewnij się, że **Application Pool** > **model procesów** puli aplikacji > **Identity** ma wartość **ApplicationPool Identity ** lub że niestandardowa tożsamość ma odpowiednie uprawnienia dostępu do folderu wdrożenia aplikacji.

* Jeśli odinstalowano pakiet hostingu ASP.NET Core i zainstalowano wcześniejszą wersję pakietu hostingu, plik *applicationHost.config* nie zawiera sekcji dla modułu ASP.NET Core. Otwórz *applicationHost.config* w lokalizacji *% windir%/system32/inetsrv/config* i Znajdź `<configuration><configSections><sectionGroup name="system.webServer">` grupę sekcji. Jeśli w grupie sekcji brakuje sekcji modułu ASP.NET Core, Dodaj element Section:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Alternatywnie Zainstaluj najnowszą wersję pakietu hostingu ASP.NET Core. Najnowsza wersja jest wstecznie zgodna z obsługiwanymi ASP.NET Core aplikacjami.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>Nieprawidłowa processPath, brakująca zmienna PATH, pakiet hostingu nie został zainstalowany, nie uruchomiono systemu/usług IIS, pakiet redystrybucyjny programu VC + + nie został zainstalowany lub dotnet.exe naruszenie dostępu

* **Przeglądarka:** Błąd HTTP 502,5 — niepowodzenie procesu

* **Dziennik aplikacji:** Nie można uruchomić procesu "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}" z fizycznym elementem głównym "C: \{ Path} \' " przy użyciu wiersza polecenia "" {...} " ", ErrorCode =" 0x80070002:0.

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika jest tworzony, ale pusty.

Rozwiązywanie problemów:

* Upewnij się, że aplikacja działa lokalnie na Kestrel. Niepowodzenie procesu może być wynikiem problemu w aplikacji. Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.

* Sprawdź atrybut *processPath* w `<aspNetCore>` elemencie w *web.config* , aby upewnić się, że jest to `dotnet` wdrożenie zależne od platformy (FDD) lub `.\{ASSEMBLY}.exe` [wdrożenie samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).

* W przypadku elementu FDD *dotnet.exe* może być niedostępna za pośrednictwem ustawień ścieżki. Upewnij się, że w ustawieniach ścieżki systemowej istnieje wartość *C:\Program Files\dotnet \\ * .

* W przypadku elementu FDD *dotnet.exe* może być niedostępna dla tożsamości użytkownika puli aplikacji. Upewnij się, że tożsamość użytkownika puli aplikacji ma dostęp do katalogu *C:\Program Files\dotnet* . Upewnij się, że nie ma skonfigurowanych reguł odmowy dla tożsamości użytkownika puli aplikacji w *folderze C:\Program Files\dotnet* i katalogach aplikacji.

* FDD mógł zostać wdrożony, a platforma .NET Core została zainstalowana bez ponownego uruchamiania usług IIS. Uruchom ponownie serwer lub Uruchom ponownie usługi IIS, wykonując polecenie **net stop** , a następnie **net start W3SVC** z wiersza polecenia.

* FDD mógł zostać wdrożony bez instalowania środowiska uruchomieniowego .NET Core w systemie hostingu. Jeśli środowisko uruchomieniowe platformy .NET Core nie zostało zainstalowane, uruchom **Instalatora pakietu hostingu programu .NET Core** w systemie.

  [Bieżący Instalator pakietu hostingu platformy .NET Core (Pobieranie bezpośrednie)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Aby uzyskać więcej informacji, zobacz [Instalowanie pakietu hostingu .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Jeśli jest wymagane określone środowisko uruchomieniowe, Pobierz środowisko uruchomieniowe z [archiwów pobierania programu .NET](https://dotnet.microsoft.com/download/archives) i zainstaluj je w systemie. Aby ukończyć instalację, należy ponownie uruchomić system lub ponownie uruchomić usługi IIS, wykonując polecenie **net stop** , a następnie **net start W3SVC** z wiersza polecenia.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Nieprawidłowe argumenty \<aspNetCore> elementu

* **Przeglądarka:** Błąd HTTP 502,5 — niepowodzenie procesu

* **Dziennik aplikacji:** Nie można uruchomić procesu "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}" z fizycznym elementem głównym "C: \{ Path}" \' przy użyciu elementu CommandLine "" dotnet ". \{ ASSEMBLY}. dll ", ErrorCode =" 0x80004005:80008081.

* **Dziennik stdout modułu ASP.NET Core:** Aplikacja do wykonania nie istnieje: "ścieżka \{ zestawu}. dll"

Rozwiązywanie problemów:

* Upewnij się, że aplikacja działa lokalnie na Kestrel. Niepowodzenie procesu może być wynikiem problemu w aplikacji. Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.

* Sprawdź atrybut *arguments* `<aspNetCore>` elementu w *web.config* , aby upewnić się, że jest (a) `.\{ASSEMBLY}.dll` dla wdrożenia zależnego od platformy (FDD); lub (b) nie istnieje, pusty ciąg ( `arguments=""` ) lub lista argumentów aplikacji ( `arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"` ) dla wdrożenia samodzielnego (SCD).

Rozwiązywanie problemów:

W przypadku wdrażania zależnego od platformy (FDD) Upewnij się, że w systemie jest zainstalowane odpowiednie środowisko uruchomieniowe.

## <a name="stopped-application-pool"></a>Zatrzymana Pula aplikacji

* **Przeglądarka:** usługa 503 jest niedostępna

* **Dziennik aplikacji:** Brak wpisu

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika nie został utworzony.

Rozwiązywanie problemów:

Upewnij się, że Pula aplikacji nie jest w stanie *zatrzymania* .

## <a name="sub-application-includes-a-handlers-section"></a>Podaplikacja zawiera \<handlers> sekcję

* **Przeglądarka:** Błąd HTTP 500,19 — wewnętrzny błąd serwera

* **Dziennik aplikacji:** Brak wpisu

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika aplikacji głównej jest tworzony i pokazuje normalną operację. Nie utworzono pliku dziennika aplikacji podrzędnej.

Rozwiązywanie problemów:

Upewnij się, że plik *web.config* aplikacji podrzędnej nie zawiera `<handlers>` sekcji.

## <a name="stdout-log-path-incorrect"></a>Nieprawidłowa ścieżka dziennika stdout

* **Przeglądarka:** Aplikacja reaguje zwykle.

* **Dziennik aplikacji:** Ostrzeżenie: nie można utworzyć stdoutLogFile \\ ? \{ ŚCIEŻKA} \ path_doesnt_exist \ stdout_ {proces ID} _ {TIMESTAMP}. log, ErrorCode =-2147024893.

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika nie został utworzony.

Rozwiązywanie problemów:

* `stdoutLogFile`Ścieżka określona w `<aspNetCore>` elemencie *web.config* nie istnieje. Aby uzyskać więcej informacji, zobacz [ASP.NET Core Module: Tworzenie i przekierowywanie dzienników](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* Użytkownik puli aplikacji nie ma dostępu do zapisu w ścieżce dziennika stdout.

## <a name="application-configuration-general-issue"></a>Ogólny problem z konfiguracją aplikacji

* **Przeglądarka:** Błąd HTTP 502,5 — niepowodzenie procesu

* **Dziennik aplikacji:** Utworzono proces "MACHINE/WEBROOT/APPHOST/{ASSEMBLY}" z fizycznym głównym katalogiem "C: \{ Path}" \' przy użyciu elementu CommandLine "" c: \{ Path} \{ . { exe | dll} "", ale wystąpiła awaria lub nie nasłuchuje na danym porcie "{PORT}", ErrorCode = "{ERROR}"

* **Dziennik stdout modułu ASP.NET Core:** Plik dziennika jest tworzony, ale pusty.

Rozwiązywanie problemów:

Nie można uruchomić procesu, prawdopodobnie z powodu problemu z konfiguracją lub programowaniem aplikacji.

Aby uzyskać więcej informacji, zobacz następujące tematy:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end
