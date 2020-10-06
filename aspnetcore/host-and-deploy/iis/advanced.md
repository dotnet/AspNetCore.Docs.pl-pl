---
title: Konfiguracja zaawansowana
author: rick-anderson
description: Zaawansowana konfiguracja przy użyciu modułu ASP.NET Core i Internet Information Services (IIS).
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: ad2480faeea2f07e51585f5bc6a1c63b3a0b1668
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755291"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a>Zaawansowana konfiguracja modułu ASP.NET Core i usług IIS

W tym artykule omówiono zaawansowane opcje konfiguracji i scenariusze dotyczące modułu ASP.NET Core i usług IIS.

## <a name="modify-the-stack-size"></a>Modyfikowanie rozmiaru stosu

*Stosuje się tylko w przypadku korzystania z modelu hostingu w procesie.*

Skonfiguruj zarządzany rozmiar stosu przy użyciu `stackSize` Ustawienia w bajtach w `web.config` pliku. Domyślny rozmiar to 1 048 576 bajtów (1 MB). Poniższy przykład zmienia rozmiar stosu na 2 MB (2 097 152 bajtów):

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

Serwer proxy utworzony między modułem ASP.NET Core a Kestrel używa protokołu HTTP. Nie ma ryzyka podsłuchiwanie ruchu między modułem i Kestrel z lokalizacji poza serwerem.

Token parowania jest używany w celu zagwarantowania, że żądania odbierane przez Kestrel zostały przekazane przez usługi IIS i nie pochodzą z innego źródła. Token parowania jest tworzony i ustawiany jako zmienna środowiskowa ( `ASPNETCORE_TOKEN` ) przez moduł. Token parowania jest również ustawiany w nagłówku ( `MS-ASPNETCORE-TOKEN` ) na każdym żądaniu z serwerem proxy. Oprogramowanie pośredniczące usług IIS sprawdza każde odebrane żądanie, aby potwierdzić, że wartość nagłówka tokenu parowania jest zgodna z wartością zmiennej środowiskowej. Jeśli wartości tokenu są niezgodne, żądanie zostanie zarejestrowane i odrzucone. Zmienna środowiskowa tokena parowania i ruch między modułem i Kestrel nie są dostępne z lokalizacji poza serwerem. Bez znajomości wartości tokenu parowania osoba atakująca nie może przesłać żądań, które pomijają Ewidencjonowanie oprogramowania pośredniczącego usług IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>Moduł ASP.NET Core z konfiguracją udostępnioną usług IIS

Instalator modułu ASP.NET Core jest uruchamiany z uprawnieniami `TrustedInstaller` konta. Ponieważ konto systemu lokalnego nie ma uprawnień do modyfikowania dla ścieżki udziału używanej przez udostępnioną konfigurację usług IIS, Instalator zgłasza błąd odmowy dostępu podczas próby skonfigurowania ustawień modułu w `applicationHost.config` pliku w udziale.

W przypadku korzystania z konfiguracji udostępnionej przez usługi IIS na tym samym komputerze, na którym znajduje się instalacja usług IIS, uruchom Instalatora pakietu ASP.NET Core hostowania z `OPT_NO_SHARED_CONFIG_CHECK` parametrem ustawionym na `1` :

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Jeśli ścieżka do konfiguracji udostępnionej nie znajduje się na tym samym komputerze, na którym znajduje się instalacja usług IIS, wykonaj następujące czynności:

1. Wyłącz konfigurację udostępnioną usług IIS.
1. Uruchom instalatora.
1. Wyeksportuj zaktualizowany `applicationHost.config` plik do udziału plików.
1. Włącz ponownie konfigurację udostępnioną usług IIS.

## <a name="data-protection"></a>Ochrona danych

[Stos ochrony danych ASP.NET Core](xref:security/data-protection/introduction) jest używany przez kilka ASP.NET Core [middlewares](xref:fundamentals/middleware/index), w tym oprogramowanie pośredniczące używane podczas uwierzytelniania. Nawet jeśli interfejsy API ochrony danych nie są wywoływane przez kod użytkownika, należy skonfigurować ochronę danych przy użyciu skryptu wdrożenia lub w kodzie użytkownika, aby utworzyć trwały [Magazyn kluczy](xref:security/data-protection/implementation/key-management)kryptograficznych. Jeśli ochrona danych nie jest skonfigurowana, klucze są przechowywane w pamięci i usuwane po ponownym uruchomieniu aplikacji.

Jeśli pierścień klucza ochrony danych jest przechowywany w pamięci po ponownym uruchomieniu aplikacji:

* cookieTokeny uwierzytelniania na podstawie wszystkich są unieważnione. 
* Użytkownicy muszą ponownie zalogować się przy następnym żądaniu. 
* Nie można już odszyfrować żadnych danych chronionych za pomocą dzwonka klucza. Może to obejmować [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) i [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata).

Aby skonfigurować ochronę danych w ramach usług IIS w celu utrwalenia pierścienia kluczy, należy użyć **jednej** z następujących metod:

* **Tworzenie kluczy rejestru ochrony danych**

  Klucze ochrony danych używane przez aplikacje ASP.NET Core są przechowywane w rejestrze zewnętrznym dla aplikacji. Aby utrzymać klucze dla danej aplikacji, należy utworzyć klucze rejestru dla puli aplikacji.

  W przypadku autonomicznych, nieopartych na webfarmie instalacji usług IIS [skrypt ochrony danych Provision-AutoGenKeys.ps1 programu PowerShell](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) może być używany dla każdej puli aplikacji używanej w aplikacji ASP.NET Core. Ten skrypt tworzy klucz rejestru w rejestrze HKLM, który jest dostępny tylko dla konta procesu roboczego puli aplikacji aplikacji. Klucze są szyfrowane przy użyciu funkcji DPAPI z kluczem dla całego komputera.

  W scenariuszach farmy sieci Web aplikacja może być skonfigurowana pod kątem używania ścieżki UNC do przechowywania tego dzwonka klucza ochrony danych. Domyślnie klucze nie są szyfrowane. Upewnij się, że uprawnienia do pliku dla udziału sieciowego są ograniczone do konta systemu Windows, w którym działa aplikacja. Certyfikat x509 może służyć do ochrony kluczy w spoczynku. Rozważ zastosowanie mechanizmu umożliwiającego użytkownikom przekazywanie certyfikatów. Umieść certyfikaty w zaufanym magazynie certyfikatów użytkownika i upewnij się, że są one dostępne na wszystkich komputerach, na których działa aplikacja użytkownika. Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/configuration/overview>.

* **Konfigurowanie puli aplikacji usług IIS w celu załadowania profilu użytkownika**

  To ustawienie znajduje się w sekcji **model procesów** w obszarze **Ustawienia zaawansowane** dla puli aplikacji. Ustaw **Załaduj profil użytkownika** na `True` . Po ustawieniu opcji `True` klucze są przechowywane w katalogu profilu użytkownika i chronione przy użyciu funkcji DPAPI z kluczem specyficznym dla konta użytkownika. Klucze są utrwalane w `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` folderze.

  Należy również włączyć [ `setProfileEnvironment` atrybut](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) puli aplikacji. Wartość domyślna `setProfileEnvironment` to `true` . W niektórych scenariuszach (na przykład system operacyjny Windows) `setProfileEnvironment` jest ustawiony na `false` . Jeśli klucze nie są przechowywane w katalogu profilu użytkownika zgodnie z oczekiwaniami:

  1. Przejdź do folderu `%windir%/system32/inetsrv/config`.
  1. Otwórz plik `applicationHost.config`.
  1. Znajdź `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.
  1. Upewnij się, że `setProfileEnvironment` atrybut nie istnieje, który jest wartością domyślną `true` , lub jawnie ustaw wartość atrybutu na `true` .

* **Używanie systemu plików jako magazynu kluczy**

  Dostosuj kod aplikacji, [tak aby używał systemu plików jako magazynu kluczy](xref:security/data-protection/configuration/overview). Użyj certyfikatu x509 do ochrony pierścienia kluczy i upewnij się, że certyfikat jest certyfikatem zaufanym. Jeśli certyfikat jest podpisany z podpisem własnym, umieść certyfikat w zaufanym magazynie głównym.

  W przypadku korzystania z usług IIS w kolektywie serwerów sieci Web:

  * Użyj udziału plików, do którego mają dostęp wszystkie maszyny.
  * Wdróż certyfikat x509 na każdym komputerze. Konfigurowanie [ochrony danych w kodzie](xref:security/data-protection/configuration/overview).

* **Ustawianie zasad dla całej maszyny na potrzeby ochrony danych**

  System ochrony danych ma ograniczoną obsługę ustawiania domyślnych [zasad komputera](xref:security/data-protection/configuration/machine-wide-policy) dla wszystkich aplikacji korzystających z interfejsów API ochrony danych. Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/introduction>.

## <a name="iis-configuration"></a>Konfiguracja usług IIS

**Systemy operacyjne Windows Server**

Włącz rolę serwera **serwera sieci Web (IIS)** i Ustanów usługi ról.

1. Użyj kreatora **dodawania ról i funkcji** z menu **Zarządzaj** lub łącza w **Menedżer serwera**. W kroku **role serwera** zaznacz pole wyboru **serwer sieci Web (IIS)**.

   ![W kroku wybierz role serwera zostanie wybrana rola IIS serwera sieci Web.](index/_static/server-roles-ws2016.png)

1. Po wykonaniu **kroków krok po kroku** **usługi ról** są ładowane dla serwera sieci Web (IIS). Wybierz żądane usługi roli IIS lub zaakceptuj domyślne usługi ról.

   ![Domyślne usługi ról są wybrane w kroku Wybierz usługi ról.](index/_static/role-services-ws2016.png)

   **Uwierzytelnianie systemu Windows (opcjonalnie)**  
   Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **Zabezpieczenia serwera sieci Web**  >  **Security**. Wybierz funkcję **uwierzytelniania systemu Windows** . Aby uzyskać więcej informacji, zobacz [uwierzytelnianie `<windowsAuthentication>` systemu Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

   **Obiekty WebSockets (opcjonalnie)**  
   Obiekty WebSockets są obsługiwane w ASP.NET Core 1,1 lub nowszych. Aby włączyć obiekty WebSockets, rozwiń następujące węzły: projektowanie aplikacji **serwera sieci Web**  >  **Application Development**. Wybierz funkcję **protokołu WebSocket** . Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).

1. Przejdź do kroku **potwierdzenia** , aby zainstalować rolę i usługi serwera sieci Web. Po zainstalowaniu roli **serwera sieci Web (IIS)** nie jest wymagane ponowne uruchomienie serwera ani usług IIS.

**Systemy operacyjne Windows dla komputerów stacjonarnych**

Włącz **konsolę zarządzania usług IIS** i **usługi World Wide Web**.

1. Przejdź do pozycji **Panel sterowania**  >  **programy**  >  **programy i funkcje**  >  **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).

1. Otwórz węzeł **Internet Information Services** . Otwórz węzeł **Narzędzia do zarządzania siecią Web** .

1. Zaznacz pole wyboru w obszarze **Konsola zarządzania usługami IIS**.

1. Zaznacz pole wyboru **usług World Wide Web Services**.

1. Zaakceptuj domyślne funkcje **usług World Wide Web Services** lub Dostosuj funkcje usług IIS.

   **Uwierzytelnianie systemu Windows (opcjonalnie)**  
   Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **World Wide Web usług**  >  **zabezpieczenia**. Wybierz funkcję **uwierzytelniania systemu Windows** . Aby uzyskać więcej informacji, zobacz [uwierzytelnianie `<windowsAuthentication>` systemu Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

   **Obiekty WebSockets (opcjonalnie)**  
   Obiekty WebSockets są obsługiwane w ASP.NET Core 1,1 lub nowszych. Aby włączyć obiekty WebSockets, rozwiń następujące węzły: **World Wide Web Services**  >  **Application Development Features**. Wybierz funkcję **protokołu WebSocket** . Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).

1. Jeśli instalacja usług IIS wymaga ponownego uruchomienia, należy ponownie uruchomić system.

![Konsola zarządzania usługami IIS i usługi World Wide Web są wybrane w funkcjach systemu Windows.](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a>Katalogi wirtualne

[Katalogi wirtualne usług IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) nie są obsługiwane w aplikacjach ASP.NET Core. Aplikacja może być hostowana jako [podaplikacja](#sub-applications).

## <a name="sub-applications"></a>Aplikacje podrzędne

Aplikacja ASP.NET Core może być hostowana jako [podaplikacja usług IIS (podrzędna)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications). Ścieżka aplikacji podrzędnej jest częścią adresu URL aplikacji głównej.

Linki do zasobów statycznych w aplikacji podrzędnej powinny używać notacji z ukośnikiem ( `~/` ). Notacja "ukośnik" wyzwala [pomocnika tagów](xref:mvc/views/tag-helpers/intro) , aby dołączyć pathbase podaplikacji do renderowanego linku względnego. W przypadku aplikacji podrzędnej w `/subapp_path` programie obraz połączony z programem `src="~/image.png"` jest renderowany jako `src="/subapp_path/image.png"` . Oprogramowanie pośredniczące aplikacji głównej nie przetwarza żądania pliku statycznego. Żądanie jest przetwarzane przez oprogramowanie pośredniczące plików statycznych aplikacji podrzędnej.

Jeśli atrybut statycznego zasobu `src` jest ustawiony na ścieżkę bezwzględną (na przykład `src="/image.png"` ), link jest renderowany bez pathbase podaplikacji. Oprogramowanie pośredniczące pliku statycznego aplikacji głównej próbuje obsłużyć zasób z poziomu [głównego katalogu sieci Web](xref:fundamentals/index#web-root)aplikacji głównej, co spowoduje, że odpowiedź na *404 nie zostanie znaleziona* , chyba że statyczny zasób jest dostępny z poziomu aplikacji głównej.

Aby hostować aplikację ASP.NET Core jako aplikację podrzędną w innej aplikacji ASP.NET Core:

1. Ustanów pulę aplikacji dla aplikacji podrzędnej. Ustaw **wersję środowiska .NET CLR** na **Brak kodu zarządzanego** , ponieważ podstawowe środowisko uruchomieniowe języka wspólnego (CoreCLR) dla platformy .NET Core jest uruchomione w celu hostowania aplikacji w procesie roboczym, a nie na pulpicie CLR (.NET CLR).

1. Dodaj lokację główną w Menedżerze usług IIS przy użyciu aplikacji podrzędnej w folderze w lokacji głównej.

1. Kliknij prawym przyciskiem myszy folder subapp w Menedżerze usług IIS, a następnie wybierz polecenie **Konwertuj na aplikację**.

1. W oknie dialogowym **Dodawanie aplikacji** Użyj przycisku **Wybierz** dla **puli aplikacji** , aby przypisać pulę aplikacji utworzoną dla aplikacji podrzędnej. Wybierz przycisk **OK**.

Przypisanie oddzielnej puli aplikacji do aplikacji podrzędnej jest wymagane w przypadku korzystania z modelu hostingu w procesie.

Aby uzyskać więcej informacji na temat modelu hostingu w procesie i konfigurowania modułu ASP.NET Core, zobacz <xref:host-and-deploy/aspnet-core-module> .

## <a name="application-pools"></a>Pule aplikacji

Izolacja puli aplikacji jest określana przez model hostingu:

* Hosting w procesie: aplikacje muszą być uruchamiane w oddzielnych pulach aplikacji.
* Hosting poza procesem: zalecamy izolowanie aplikacji od siebie, uruchamiając każdą aplikację w jej własnej puli aplikacji.

Okno dialogowe **Dodaj witrynę sieci Web** usług IIS domyślnie umożliwia pojedynczej puli aplikacji na aplikację. Po podaniu **nazwy witryny** tekst zostanie automatycznie przeniesiony do pola tekstowego **Pula aplikacji** . Nowa pula aplikacji jest tworzona przy użyciu nazwy lokacji, gdy zostanie dodana lokacja.

## <a name="application-pool-no-locidentity"></a>Pula aplikacji Identity

Konto tożsamości puli aplikacji umożliwia uruchamianie aplikacji na unikatowym koncie bez konieczności tworzenia domen ani kont lokalnych oraz zarządzania nimi. W przypadku usług IIS 8,0 lub nowszych proces roboczy administratora usług IIS tworzy konto wirtualne o nazwie nowej puli aplikacji i domyślnie uruchamia procesy robocze puli aplikacji w ramach tego konta. W konsoli zarządzania usługami IIS w obszarze **Ustawienia zaawansowane** dla puli aplikacji upewnij się, że **Identity** ustawiono opcję Użyj `ApplicationPoolIdentity` :

![Okno dialogowe Zaawansowane ustawienia puli aplikacji](index/_static/apppool-identity.png)

Proces zarządzania usługami IIS tworzy Bezpieczny identyfikator z nazwą puli aplikacji w systemie zabezpieczeń systemu Windows. Zasoby mogą być zabezpieczone przy użyciu tej tożsamości. Jednak ta tożsamość nie jest kontem użytkownika rzeczywistego i nie jest wyświetlana w konsoli zarządzania użytkownikami systemu Windows.

Jeśli proces roboczy usług IIS wymaga podwyższonego poziomu dostępu do aplikacji, Zmodyfikuj listę Access Control (ACL) dla katalogu zawierającego aplikację:

1. Otwórz Eksploratora Windows i przejdź do katalogu.

1. Kliknij prawym przyciskiem myszy katalog i wybierz polecenie **Właściwości**.

1. Na karcie **zabezpieczenia** wybierz przycisk **Edytuj** , a następnie przycisk **Dodaj** .

1. Wybierz przycisk **lokalizacje** i upewnij się, że wybrano system.

1. Wprowadź `IIS AppPool\{APP POOL NAME}` Format, gdzie symbol zastępczy `{APP POOL NAME}` to nazwa puli aplikacji, w polu **Wprowadź nazwy obiektów do wybrania** . Wybierz przycisk **Sprawdź nazwy** . W polu *Domyślna pula aplikacji* Sprawdź nazwy używane przez program `IIS AppPool\DefaultAppPool` . Po wybraniu przycisku **Sprawdź nazwy** `DefaultAppPool` w obszarze nazwy obiektów jest wskazywana wartość. Nie można wprowadzić nazwy puli aplikacji bezpośrednio w obszarze nazw obiektów. Użyj `IIS AppPool\{APP POOL NAME}` formatu, gdzie symbol zastępczy `{APP POOL NAME}` jest nazwą puli aplikacji, podczas sprawdzania nazwy obiektu.

   ![Okno dialogowe Wybieranie użytkowników lub grup dla folderu aplikacji: Nazwa puli aplikacji "domyślna pula" jest dołączana do "puli aplikacji IIS \" w obszarze nazw obiektów przed wybraniem pozycji" Sprawdź nazwy ".](index/_static/select-users-or-groups-1.png)

1. Wybierz przycisk **OK**.

   ![Okno dialogowe Wybieranie użytkowników lub grup dla folderu aplikacji: po wybraniu pozycji "Sprawdź nazwy" w obszarze nazwy obiektów zostanie wyświetlona nazwa obiektu "domyślna pula aplikacji".](index/_static/select-users-or-groups-2.png)

1. &amp;Uprawnienia do wykonywania odczytu powinny być przyznawane domyślnie. Podaj dodatkowe uprawnienia zgodnie z wymaganiami.

Dostęp można także udzielić w wierszu polecenia przy użyciu narzędzia **icacls** . Użycie funkcji *Domyślna pula aplikacji* jako przykładu powoduje użycie następującego polecenia:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Aby uzyskać więcej informacji, zobacz temat [icacls](/windows-server/administration/windows-commands/icacls) .

## <a name="http2-support"></a>Obsługa protokołu HTTP/2

[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwany z ASP.NET Core w następujących scenariuszach wdrażania usług IIS:

* W procesie
  * Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy
  * Połączenie TLS 1,2 lub nowsze
* Pozaprocesowe
  * Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy
  * Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotnego serwera proxy z [serwerem Kestrel](xref:fundamentals/servers/kestrel) korzysta z protokołu HTTP/1.1.
  * Połączenie TLS 1,2 lub nowsze

W przypadku wdrożenia w procesie podczas ustanawiania połączenia HTTP/2 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Raporty `HTTP/2` . W przypadku wdrożenia poza procesem w przypadku ustanowienia połączenia HTTP/2 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Raporty `HTTP/1.1` .

Aby uzyskać więcej informacji na temat modeli hostingu w procesie i poza procesami, zobacz <xref:host-and-deploy/aspnet-core-module> .

Protokół HTTP/2 jest domyślnie włączony. Połączenia powracają do protokołu HTTP/1.1, jeśli połączenie HTTP/2 nie zostało ustanowione. Aby uzyskać więcej informacji na temat konfiguracji protokołu HTTP/2 z wdrożeniami usług IIS, zobacz [http/2 w usługach IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Żądania inspekcji wstępnej CORS

*Ta sekcja dotyczy tylko ASP.NET Core aplikacji przeznaczonych dla .NET Framework.*

W przypadku aplikacji ASP.NET Core, która jest przeznaczona dla .NET Framework, żądania opcji nie są domyślnie przesyłane do aplikacji w usługach IIS. Aby dowiedzieć się, jak skonfigurować obsługę usług IIS w programie w `web.config` celu przekazywania żądań opcji, zobacz [Włączanie żądań między źródłami w programie ASP.NET Web API 2: jak działa mechanizm CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Moduł inicjalizacji aplikacji i limit czasu bezczynności

Hostowane w usługach IIS przez moduł ASP.NET Core w wersji 2:

* [Moduł inicjalizacji aplikacji](#application-initialization-module): hostowana [w procesie](xref:host-and-deploy/iis/in-process-hosting) lub [poza procesem](xref:host-and-deploy/iis/out-of-process-hosting) aplikacja może zostać skonfigurowana do automatycznego uruchamiania na potrzeby ponownego uruchomienia procesu roboczego lub ponownego uruchomienia serwera.
* [Limit czasu bezczynności](#idle-timeout): w trakcie okresu braku aktywności aplikacja hostowana [w procesie](xref:host-and-deploy/iis/in-process-hosting) może zostać skonfigurowana w taki sposób, aby nie przekroczyć limitu czasu.

### <a name="application-initialization-module"></a>Moduł inicjalizacji aplikacji

*Dotyczy aplikacji hostowanych w procesie i pozaprocesowe.*

[Inicjowanie aplikacji IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) to funkcja usług IIS, która wysyła do aplikacji żądanie HTTP, gdy pula aplikacji zostanie uruchomiona lub zostanie odtworzona. Żądanie wyzwala uruchomienie aplikacji. Domyślnie usługi IIS wysyłają żądanie do głównego adresu URL aplikacji ( `/` ) w celu zainicjowania aplikacji (zobacz [dodatkowe zasoby](#application-initialization-module-and-idle-timeout-additional-resources) , aby uzyskać więcej informacji na temat konfiguracji).

Upewnij się, że funkcja inicjowania roli inicjalizacji aplikacji IIS jest włączona:

Na komputerach z systemem Windows 7 lub nowszym w przypadku lokalnego korzystania z usług IIS:

1. Przejdź do pozycji **Panel sterowania**  >  **programy**  >  **programy i funkcje**  >  **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).
1. Otwórz **Internet Information Services**  >  **World Wide Web Services**  >  **funkcje projektowania aplikacji**Internet Information Services World Wide Web Services.
1. Zaznacz pole wyboru dla **inicjowania aplikacji**.

W systemie Windows Server 2008 R2 lub nowszym:

1. Otwórz **Kreatora dodawania ról i funkcji**.
1. W panelu **Wybierz usługi ról** Otwórz węzeł **Programowanie aplikacji** .
1. Zaznacz pole wyboru dla **inicjowania aplikacji**.

Użyj jednego z poniższych metod, aby włączyć moduł inicjowania aplikacji dla lokacji:

* Za pomocą Menedżera usług IIS:

  1. W panelu **połączenia** wybierz pozycję **Pule aplikacji** .
  1. Kliknij prawym przyciskiem myszy pulę aplikacji aplikacji na liście i wybierz pozycję **Ustawienia zaawansowane**.
  1. Domyślny **tryb uruchamiania** to `OnDemand` . Ustaw **tryb uruchamiania** na `AlwaysRunning` . Wybierz przycisk **OK**.
  1. Otwórz węzeł **Lokacje** w panelu **połączenia** .
  1. Kliknij prawym przyciskiem myszy aplikację i wybierz pozycję Zarządzaj ustawieniami zaawansowanymi **witryny sieci Web**  >  **Advanced Settings**.
  1. Ustawienie domyślnego **wstępnego ładowania jest włączone** `False` . Ustaw **funkcję wstępnego ładowania** na `True` . Wybierz przycisk **OK**.

* Przy użyciu `web.config` , Dodaj `<applicationInitialization>` element z `doAppInitAfterRestart` ustawionym do `true` `<system.webServer>` elementów w pliku aplikacji `web.config` :

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a>Limit czasu bezczynności

*Dotyczy tylko aplikacji hostowanych w procesie.*

Aby zapobiec przekroczeniu przez aplikację, należy ustawić limit czasu bezczynności puli aplikacji przy użyciu Menedżera usług IIS:

1. W panelu **połączenia** wybierz pozycję **Pule aplikacji** .
1. Kliknij prawym przyciskiem myszy pulę aplikacji aplikacji na liście i wybierz pozycję **Ustawienia zaawansowane**.
1. Domyślny **limit czasu bezczynności (w minutach)** to `20` minuty. Ustaw **limit czasu bezczynności (w minutach)** na `0` (zero). Wybierz przycisk **OK**.
1. Odtwórz proces roboczy.

Aby zapobiec przekroczeniu limitu [czasu hostowanych przez aplikacje](xref:host-and-deploy/iis/out-of-process-hosting) aplikacji, użyj jednej z następujących metod:

* Wyślij polecenie ping do aplikacji z zewnętrznej usługi, aby było ono uruchomione.
* Jeśli aplikacja obsługuje tylko usługi w tle, należy unikać hostingu usług IIS i używać [usługi systemu Windows do hostowania aplikacji ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Dodatkowe zasoby dotyczące modułu inicjalizacji aplikacji i limitu czasu bezczynności

* [Inicjowanie aplikacji usług IIS 8,0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Inicjowanie `<applicationInitialization>` aplikacji ](/iis/configuration/system.webserver/applicationinitialization/).
* [Ustawienia modelu procesów dla `<processModel>` puli aplikacji ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="module-schema-and-configuration-file-locations"></a>Lokalizacje pliku modułu, schematu i konfiguracji

### <a name="module"></a>Moduł

**IIS (x86/amd64)**:

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

**IIS Express (x86/amd64)**:

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a>Schemat

**IIS**

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

**IIS Express**

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a>Konfigurowanie

**IIS**

* `%windir%\System32\inetsrv\config\applicationHost.config`

**IIS Express**

* Program Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`

* *iisexpress.exe* Interfejs `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`

Pliki można znaleźć, wyszukując je `aspnetcore` w `applicationHost.config` pliku.
