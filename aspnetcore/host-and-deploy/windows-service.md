---
title: Host ASP.NET Core w usłudze systemu Windows
author: rick-anderson
description: Dowiedz się, jak hostować aplikację ASP.NET Core w usłudze systemu Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: 5cb61d330df7e15fbd54396207792596ae018fd3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417579"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Host ASP.NET Core w usłudze systemu Windows

::: moniker range=">= aspnetcore-3.0"

Aplikacja ASP.NET Core może być hostowana w systemie Windows jako [usługa systemu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez korzystania z usług IIS. Po uruchomieniu jako usługa systemu Windows aplikacja jest uruchamiana automatycznie po ponownym uruchomieniu serwera.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

* [ASP.NET Core SDK 2.1 lub nowszym](https://dotnet.microsoft.com/download)
* [Program PowerShell 6.2 lub nowszy](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a>Szablon usługi pracownika

Szablon ASP.NET Core Worker Service stanowi punkt wyjścia do pisania długo działających aplikacji usługi. Aby użyć szablonu jako podstawy aplikacji usługi windows:

1. Utwórz aplikację usługi procesu roboczego na podstawie szablonu .NET Core.
1. Postępuj zgodnie ze wskazówkami w sekcji [Konfiguracja aplikacji,](#app-configuration) aby zaktualizować aplikację usługi procesu roboczego, aby mogła działać jako usługa systemu Windows.

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a>Konfiguracja aplikacji

Aplikacja wymaga odwołania do pakietu [dla microsoft.extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).

`IHostBuilder.UseWindowsService`podczas budowania hosta. Jeśli aplikacja jest uruchomiona jako usługa systemu Windows, metoda:

* Ustawia okres istnienia `WindowsServiceLifetime`hosta na .
* Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory). Aby uzyskać więcej informacji, zobacz [sekcję Bieżący katalog i katalog zawartości.](#current-directory-and-content-root)
* Umożliwia rejestrowanie w dzienniku zdarzeń:
  * Nazwa aplikacji jest używana jako domyślna nazwa źródła.
  * Domyślny poziom dziennika to *Ostrzeżenie* lub wyższy dla aplikacji opartej `CreateDefaultBuilder` na szablonie ASP.NET Core, który wywołuje tworzenie hosta.
  * Zastąp domyślny poziom `Logging:EventLog:LogLevel:Default` dziennika za pomocą klucza w *appsettings.json*/*appsettings.{ Środowisko}.json* lub inny dostawca konfiguracji.
  * Tylko administratorzy mogą tworzyć nowe źródła zdarzeń. Jeśli nie można utworzyć źródła zdarzeń przy użyciu nazwy aplikacji, ostrzeżenie jest rejestrowane w źródle *aplikacji,* a dzienniki zdarzeń są wyłączone.

W `CreateHostBuilder` *Program.cs:*

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

W tym temacie dołączone są następujące przykładowe aplikacje:

* Przykład &ndash; usługi procesu roboczego w tle Przykładowa aplikacja niesienia sieci web oparta na [szablonie Usługi procesu roboczego,](#worker-service-template) który używa [hostowanych usług](xref:fundamentals/host/hosted-services) do wykonywania zadań w tle.
* Przykładowa próbka &ndash; aplikacji sieciOwy Razor Pages, która działa jako usługa systemu Windows z [usługami hostowanymi](xref:fundamentals/host/hosted-services) dla zadań w tle.

Wskazówki MVC można znaleźć <xref:mvc/overview> <xref:migration/22-to-30>w artykułach w obszarze i .

## <a name="deployment-type"></a>Typ wdrożenia

Aby uzyskać informacje i porady dotyczące scenariuszy wdrażania, zobacz [wdrożenie aplikacji .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>SDK

W przypadku usługi opartej na aplikacjach sieci web, która używa stron Razor Pages lub struktur MVC, określ składnik Web SDK w pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Jeśli usługa wykonuje tylko zadania w tle (na przykład [usługi hostowane),](xref:fundamentals/host/hosted-services)określ sdk roboczy w pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Wdrożenie zależne od struktury (FDD)

Wdrożenie zależne od struktury (FDD) opiera się na obecności udostępnionej wersji systemu .NET Core w systemie docelowym. Gdy scenariusz FDD zostanie przyjęty zgodnie ze wskazówkami zawartymi w tym artykule, SDK tworzy plik wykonywalny (*.exe*), nazywany *wykonywalnym zależnym od struktury*.

W przypadku korzystania z [pakietu Web SDK](#sdk)plik *web.config,* który jest zwykle produkowany podczas publikowania aplikacji ASP.NET Core, nie jest potrzebny dla aplikacji Usług systemu Windows. Aby wyłączyć tworzenie pliku *web.config,* `<IsTransformWebConfigDisabled>` dodaj właściwość ustawioną na `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Samodzielne wdrażanie (SCD)

Samodzielne wdrożenie (SCD) nie opiera się na obecności udostępnionej struktury w systemie hosta. Środowisko wykonawcze i zależności aplikacji są wdrażane za pomocą aplikacji.

Identyfikator środowiska wykonawczego systemu Windows [(RID)](/dotnet/core/rid-catalog) `<PropertyGroup>` znajduje się w platformie zawierającej platformę docelową:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Aby opublikować wiele identyfikatorów RID:

* Podaj identyfikatory RID na liście rozdzielanych średnikami.
* Nazwa właściwości [ \<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (liczba mnoga).

Aby uzyskać więcej informacji, zobacz [.NET Core RID Catalog](/dotnet/core/rid-catalog).

## <a name="service-user-account"></a>Konto użytkownika usługi

Aby utworzyć konto użytkownika dla usługi, użyj polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z administracyjnej powłoki poleceń programu PowerShell 6.

W systemie Windows 10 October 2018 Update (wersja 1809/kompilacja 10.0.17763) lub nowszej:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

W systemie operacyjnym Windows wcześniej niż Windows 10 October 2018 Update (wersja 1809/build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Po wyświetleniu monitu podaj [silne hasło.](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)

O `-AccountExpires` ile parametr nie zostanie dostarczony do polecenia cmdlet <xref:System.DateTime> [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z wygaśnięciem, konto nie wygaśnie.

Aby uzyskać więcej informacji, zobacz [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) i [Konta użytkowników usług](/windows/desktop/services/service-user-accounts).

Alternatywnym podejściem do zarządzania użytkownikami podczas korzystania z usługi Active Directory jest użycie kont usług zarządzanych. Aby uzyskać więcej informacji, zobacz [Omówienie grupowych kont usług zarządzanych](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Zaloguj się jako prawa do usługi

Aby *ustanowić logowanie jako* prawa do usługi dla konta użytkownika usługi:

1. Otwórz edytor lokalnych zasad zabezpieczeń, uruchamiając *plik secpol.msc*.
1. Rozwiń węzeł **Zasady lokalne** i wybierz pozycję **Przypisanie praw użytkownika**.
1. Otwórz **zasadę Logowanie jako usługi.**
1. Wybierz **pozycję Dodaj użytkownika lub grupę**.
1. Podaj nazwę obiektu (konto użytkownika) przy użyciu jednej z następujących metod:
   1. Wpisz konto użytkownika`{DOMAIN OR COMPUTER NAME\USER}`( ) w polu nazwa obiektu i wybierz **przycisk OK,** aby dodać użytkownika do zasad.
   1. Wybierz **pozycję Zaawansowane**. Wybierz **pozycję Znajdź teraz**. Wybierz konto użytkownika z listy. Kliknij przycisk **OK**. Wybierz **przycisk OK** ponownie, aby dodać użytkownika do zasad.
1. Wybierz **przycisk OK** lub **Zastosuj,** aby zaakceptować zmiany.

## <a name="create-and-manage-the-windows-service"></a>Tworzenie usługi systemu Windows i zarządzanie nią

### <a name="create-a-service"></a>Tworzenie usługi

Użyj poleceń programu PowerShell, aby zarejestrować usługę. Z administracyjnej powłoki poleceń programu PowerShell 6 wykonaj następujące polecenia:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`&ndash; Ścieżka do folderu aplikacji na hoście `d:\myservice`(na przykład ). Nie dołączaj pliku wykonywalnego aplikacji do ścieżki. Ukośnik na końcu nie jest wymagany.
* `{DOMAIN OR COMPUTER NAME\USER}`&ndash; Konto użytkownika usługi (na przykład `Contoso\ServiceUser`).
* `{SERVICE NAME}`&ndash; Nazwa usługi (na `MyService`przykład ).
* `{EXE FILE PATH}`&ndash; Ścieżka wykonywalna aplikacji `d:\myservice\myservice.exe`(na przykład ). Dołącz nazwę pliku wykonywalnego z rozszerzeniem.
* `{DESCRIPTION}`&ndash; Opis usługi (na `My sample service`przykład ).
* `{DISPLAY NAME}`&ndash; Nazwa wyświetlana usługi `My Service`(na przykład ).

### <a name="start-a-service"></a>Uruchamianie usługi

Uruchom usługę za pomocą następującego polecenia programu PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Polecenie trwa kilka sekund, aby uruchomić usługę.

### <a name="determine-a-services-status"></a>Określanie stanu usługi

Aby sprawdzić stan usługi, użyj następującego polecenia programu PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

Stan jest zgłaszany jako jedna z następujących wartości:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Zatrzymywanie usługi

Zatrzymaj usługę za pomocą następującego polecenia programu Powershell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Usuwanie usługi

Po krótkim opóźnieniu zatrzymania usługi usuń usługę za pomocą następującego polecenia Programu Powershell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

Usługi, które wchodzą w interakcję z żądaniami z Internetu lub sieci firmowej i znajdują się za serwerem proxy lub modułem równoważenia obciążenia, mogą wymagać dodatkowej konfiguracji. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Konfigurowanie punktów końcowych

Domyślnie ASP.NET Core wiąże się `http://localhost:5000`z programem . Skonfiguruj adres URL `ASPNETCORE_URLS` i port, ustawiając zmienną środowiskową.

Aby uzyskać dodatkowe podejścia do konfiguracji adresów URL i portów, zobacz odpowiedni artykuł na temat serwera:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Powyższe wskazówki obejmują obsługę punktów końcowych HTTPS. Na przykład skonfiguruj aplikację dla protokołu HTTPS, gdy uwierzytelnianie jest używane z usługą systemu Windows.

> [!NOTE]
> Użycie ASP.NET certyfikatu dewelopera HTTPS w celu zabezpieczenia punktu końcowego usługi nie jest obsługiwane.

## <a name="current-directory-and-content-root"></a>Bieżący katalog i katalog główny zawartości

Bieżący katalog roboczy <xref:System.IO.Directory.GetCurrentDirectory*> zwracany przez wywołanie usługi systemu Windows to folder *C:\\WINDOWS\\system32.* Folder *system32* nie jest odpowiednią lokalizacją do przechowywania plików usługi (na przykład plików ustawień). Użyj jednego z następujących podejść do obsługi i uzyskiwania dostępu do zasobów i plików ustawień usługi.

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>Korzystanie z programu ContentRootPath lub ContentRootFileProvider

Użyj [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) lub <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> zlokalizować zasoby aplikacji.

Gdy aplikacja działa jako <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> usługa, <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> ustawia na [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).

Domyślne pliki ustawień aplikacji, *appsettings.json* i *appsettings.{ Środowisko}.json*, są ładowane z katalogu głównego zawartości aplikacji, wywołując [CreateDefaultBuilder podczas budowy hosta](xref:fundamentals/host/generic-host#set-up-a-host).

W przypadku innych plików ustawień <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>załadowanych przez kod <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>programisty w , nie ma potrzeby, aby zadzwonić . W poniższym przykładzie plik *custom_settings.json* istnieje w katalogu głównym zawartości aplikacji i jest ładowany bez jawnego ustawiania ścieżki podstawowej:

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

Nie próbuj używać <xref:System.IO.Directory.GetCurrentDirectory*> do uzyskania ścieżki zasobów, ponieważ aplikacja usługi systemu Windows zwraca folder *C:\\WINDOWS\\system32* jako bieżący katalog.

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Przechowywanie plików usługi w odpowiednim miejscu na dysku

Określ ścieżkę <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> bezwzględną <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> podczas korzystania z folderu zawierającego pliki.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby rozwiązać problem z <xref:test/troubleshoot>aplikacją Usługi systemu Windows, zobacz .

### <a name="common-errors"></a>Typowe błędy

* Używana jest stara lub wersja wstępna programu PowerShell.
* Zarejestrowana usługa nie używa **opublikowanych** danych wyjściowych aplikacji z polecenia [publikowania dotnet.](/dotnet/core/tools/dotnet-publish) Dane wyjściowe polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) nie jest obsługiwany dla wdrażania aplikacji. Opublikowane zasoby znajdują się w jednym z następujących folderów w zależności od typu wdrożenia:
  * *bin/release/{TARGET FRAMEWORK}/publish* (FDD)
  * *bin/release/{TARGET FRAMEWORK}/{IDENTYFIKATOR ŚRODOWISKA URUCHOMIENIOWEGO}/publish* (SCD)
* Usługa nie jest w stanie RUNNING.
* Ścieżki do zasobów używanych przez aplikację (na przykład certyfikaty) są niepoprawne. Podstawową ścieżką usługi systemu Windows jest *c:\\Windows\\System32*.
* Użytkownik nie ma *logowania jako* praw do usługi.
* Hasło użytkownika wygasło lub zostało niepoprawnie przekazane `New-Service` podczas wykonywania polecenia programu PowerShell.
* Aplikacja wymaga ASP.NET uwierzytelniania core, ale nie jest skonfigurowana do bezpiecznych połączeń (HTTPS).
* Port adresu URL żądania jest niepoprawny lub niepoprawnie skonfigurowany w aplikacji.

### <a name="system-and-application-event-logs"></a>Dzienniki zdarzeń systemu i aplikacji

Dostęp do dzienników zdarzeń systemu i aplikacji:

1. Otwórz menu Start, wyszukaj *podgląd zdarzeń*i wybierz aplikację **Podgląd zdarzeń.**
1. W **Podglądzie zdarzeń**otwórz węzeł **Dzienniki systemu Windows.**
1. Wybierz **system,** aby otworzyć dziennik zdarzeń systemu. Wybierz **opcję Aplikacja,** aby otworzyć dziennik zdarzeń aplikacji.
1. Wyszukaj błędy skojarzone z nieudaną aplikacją.

### <a name="run-the-app-at-a-command-prompt"></a>Uruchamianie aplikacji w wierszu polecenia

Wiele błędów uruchamiania nie generuje przydatnych informacji w dziennikach zdarzeń. Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingowym. Aby zarejestrować dodatkowe szczegóły z aplikacji, obniżyć [poziom dziennika](xref:fundamentals/logging/index#log-level) lub uruchomić aplikację w [środowisku deweloperskim](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Wyczyść pamięć podręczną pakietów

Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestawu .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w aplikacji. W niektórych przypadkach niespójne pakiety mogą spowodować przerwanie aplikacji podczas wykonywania głównych uaktualnień. Większość z tych problemów można rozwiązać, postępując zgodnie z następującymi instrukcjami:

1. Usuń *foldery bin* i *obj.*
1. Wyczyść bufory pakietu, wykonując [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z powłoki poleceń.

   Czyszczenie pamięci podręcznych pakietów można również wykonać za pomocą narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonać polecenie `nuget locals all -clear`. *nuget.exe* nie jest w pakiecie zainstalować z systemem operacyjnym Windows dla komputerów stacjonarnych i muszą być uzyskane oddzielnie od [strony internetowej NuGet](https://www.nuget.org/downloads).

1. Przywracanie i odbudowywać projekt.
1. Usuń wszystkie pliki w folderze wdrażania na serwerze przed ponowne wdrożeniem aplikacji.

### <a name="slow-or-hanging-app"></a>Powolna lub wisząca aplikacja

*Zrzut awaryjny* jest migawką pamięci systemu i może pomóc w określeniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikacja ulega awarii lub napotyka wyjątek

Uzyskiwanie i analizowanie zrzutu z [raportowania błędów systemu Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Utwórz folder do przechowywania `c:\dumps`plików zrzutu awaryjnego w pliku .
1. Uruchom [skrypt EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) o nazwie wykonywalnej aplikacji:

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Uruchom aplikację w warunkach, które powodują awarię.
1. Po wystąpieniu awarii uruchom [skrypt DisableDumps PowerShell:](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1)

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

Po awarii aplikacji i kolekcji zrzutu jest zakończona, aplikacja może zakończyć się normalnie. Skrypt programu PowerShell konfiguruje program WER do zbierania maksymalnie pięciu zrzutów na aplikację.

> [!WARNING]
> Zrzuty awaryjne mogą zająć dużą ilość miejsca na dysku (do kilku gigabajtów).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie

Gdy aplikacja *zawiesza* się (przestaje odpowiadać, ale nie ulega awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [Pliki zrzutu w trybie użytkownika: Wybieranie najlepszego narzędzia,](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) aby wybrać odpowiednie narzędzie do produkcji zrzutu.

#### <a name="analyze-the-dump"></a>Analizowanie zrzutu

Zrzut można analizować przy użyciu kilku metod. Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu w trybie użytkownika](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Konfiguracja punktu końcowego pustułka](xref:fundamentals/servers/kestrel#endpoint-configuration) (obejmuje konfigurację HTTPS i obsługę SNI)
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Aplikacja ASP.NET Core może być hostowana w systemie Windows jako [usługa systemu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez korzystania z usług IIS. Po uruchomieniu jako usługa systemu Windows aplikacja jest uruchamiana automatycznie po ponownym uruchomieniu serwera.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

* [ASP.NET Core SDK 2.1 lub nowszym](https://dotnet.microsoft.com/download)
* [Program PowerShell 6.2 lub nowszy](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Konfiguracja aplikacji

Aplikacja wymaga odwołań do pakietów dla [witryny Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) i [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Aby przetestować i debugować podczas uruchamiania poza usługą, dodaj kod, aby ustalić, czy aplikacja jest uruchomiona jako usługa lub aplikacja konsoli. Sprawdź, czy debuger jest `--console` dołączony lub przełącznik jest obecny. Jeśli którykolwiek z tych warunków jest spełniony (aplikacja <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>nie jest uruchamiana jako usługa), zadzwoń . Jeśli warunki są fałszywe (aplikacja jest uruchamiana jako usługa):

* Zadzwoń <xref:System.IO.Directory.SetCurrentDirectory*> i użyj ścieżki do opublikowanej lokalizacji aplikacji. Nie wywołaj, <xref:System.IO.Directory.GetCurrentDirectory*> aby uzyskać ścieżkę, ponieważ aplikacja usługi systemu Windows <xref:System.IO.Directory.GetCurrentDirectory*> zwraca folder *C:\\WINDOWS\\system32,* gdy jest wywoływana. Aby uzyskać więcej informacji, zobacz [sekcję Bieżący katalog i katalog zawartości.](#current-directory-and-content-root) Ten krok jest wykonywany przed skonfigurowaniem aplikacji w pliku `CreateWebHostBuilder`.
* Wywołanie, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> aby uruchomić aplikację jako usługę.

Ponieważ [dostawca konfiguracji wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) wymaga par nazwa-wartość dla `--console` argumentów wiersza <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> polecenia, przełącznik jest usuwany z argumentów przed odebraniem argumentów.

Aby zapisać w dzienniku zdarzeń systemu Windows, dodaj dostawcę EventLog do <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Ustaw poziom rejestrowania `Logging:LogLevel:Default` za pomocą klucza w *appsettings. Plik Production.json.*

W poniższym przykładzie z `RunAsCustomService` przykładowej aplikacji, jest wywoływana zamiast <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> w celu obsługi zdarzeń lifetime w aplikacji. Aby uzyskać więcej informacji, zobacz [Handle uruchamiania i zatrzymywania zdarzeń](#handle-starting-and-stopping-events) sekcji.

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Typ wdrożenia

Aby uzyskać informacje i porady dotyczące scenariuszy wdrażania, zobacz [wdrożenie aplikacji .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>SDK

W przypadku usługi opartej na aplikacjach sieci web, która używa stron Razor Pages lub struktur MVC, określ składnik Web SDK w pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Jeśli usługa wykonuje tylko zadania w tle (na przykład [usługi hostowane),](xref:fundamentals/host/hosted-services)określ sdk roboczy w pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Wdrożenie zależne od struktury (FDD)

Wdrożenie zależne od struktury (FDD) opiera się na obecności udostępnionej wersji systemu .NET Core w systemie docelowym. Gdy scenariusz FDD zostanie przyjęty zgodnie ze wskazówkami zawartymi w tym artykule, SDK tworzy plik wykonywalny (*.exe*), nazywany *wykonywalnym zależnym od struktury*.

Identyfikator środowiska wykonawczego systemu Windows [(RID)](/dotnet/core/rid-catalog) [\<(RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) zawiera platformę docelową. W poniższym przykładzie identyfikator `win7-x64`RID jest ustawiony na . Właściwość `<SelfContained>` jest `false`ustawiona na . Te właściwości instruują SDK do generowania pliku wykonywalnego *(exe)* dla systemu Windows i aplikacji, która zależy od udostępnionej struktury .NET Core.

Plik *web.config,* który jest zwykle produkowany podczas publikowania aplikacji ASP.NET Core, nie jest potrzebny dla aplikacji Usług systemu Windows. Aby wyłączyć tworzenie pliku *web.config,* `<IsTransformWebConfigDisabled>` dodaj właściwość ustawioną na `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Samodzielne wdrażanie (SCD)

Samodzielne wdrożenie (SCD) nie opiera się na obecności udostępnionej struktury w systemie hosta. Środowisko wykonawcze i zależności aplikacji są wdrażane za pomocą aplikacji.

Identyfikator środowiska wykonawczego systemu Windows [(RID)](/dotnet/core/rid-catalog) `<PropertyGroup>` znajduje się w platformie zawierającej platformę docelową:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Aby opublikować wiele identyfikatorów RID:

* Podaj identyfikatory RID na liście rozdzielanych średnikami.
* Nazwa właściwości [ \<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (liczba mnoga).

Aby uzyskać więcej informacji, zobacz [.NET Core RID Catalog](/dotnet/core/rid-catalog).

Właściwość `<SelfContained>` jest `true`ustawiona na:

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Konto użytkownika usługi

Aby utworzyć konto użytkownika dla usługi, użyj polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z administracyjnej powłoki poleceń programu PowerShell 6.

W systemie Windows 10 October 2018 Update (wersja 1809/kompilacja 10.0.17763) lub nowszej:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

W systemie operacyjnym Windows wcześniej niż Windows 10 October 2018 Update (wersja 1809/build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Po wyświetleniu monitu podaj [silne hasło.](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)

O `-AccountExpires` ile parametr nie zostanie dostarczony do polecenia cmdlet <xref:System.DateTime> [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z wygaśnięciem, konto nie wygaśnie.

Aby uzyskać więcej informacji, zobacz [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) i [Konta użytkowników usług](/windows/desktop/services/service-user-accounts).

Alternatywnym podejściem do zarządzania użytkownikami podczas korzystania z usługi Active Directory jest użycie kont usług zarządzanych. Aby uzyskać więcej informacji, zobacz [Omówienie grupowych kont usług zarządzanych](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Zaloguj się jako prawa do usługi

Aby *ustanowić logowanie jako* prawa do usługi dla konta użytkownika usługi:

1. Otwórz edytor lokalnych zasad zabezpieczeń, uruchamiając *plik secpol.msc*.
1. Rozwiń węzeł **Zasady lokalne** i wybierz pozycję **Przypisanie praw użytkownika**.
1. Otwórz **zasadę Logowanie jako usługi.**
1. Wybierz **pozycję Dodaj użytkownika lub grupę**.
1. Podaj nazwę obiektu (konto użytkownika) przy użyciu jednej z następujących metod:
   1. Wpisz konto użytkownika`{DOMAIN OR COMPUTER NAME\USER}`( ) w polu nazwa obiektu i wybierz **przycisk OK,** aby dodać użytkownika do zasad.
   1. Wybierz **pozycję Zaawansowane**. Wybierz **pozycję Znajdź teraz**. Wybierz konto użytkownika z listy. Kliknij przycisk **OK**. Wybierz **przycisk OK** ponownie, aby dodać użytkownika do zasad.
1. Wybierz **przycisk OK** lub **Zastosuj,** aby zaakceptować zmiany.

## <a name="create-and-manage-the-windows-service"></a>Tworzenie usługi systemu Windows i zarządzanie nią

### <a name="create-a-service"></a>Tworzenie usługi

Użyj poleceń programu PowerShell, aby zarejestrować usługę. Z administracyjnej powłoki poleceń programu PowerShell 6 wykonaj następujące polecenia:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`&ndash; Ścieżka do folderu aplikacji na hoście `d:\myservice`(na przykład ). Nie dołączaj pliku wykonywalnego aplikacji do ścieżki. Ukośnik na końcu nie jest wymagany.
* `{DOMAIN OR COMPUTER NAME\USER}`&ndash; Konto użytkownika usługi (na przykład `Contoso\ServiceUser`).
* `{SERVICE NAME}`&ndash; Nazwa usługi (na `MyService`przykład ).
* `{EXE FILE PATH}`&ndash; Ścieżka wykonywalna aplikacji `d:\myservice\myservice.exe`(na przykład ). Dołącz nazwę pliku wykonywalnego z rozszerzeniem.
* `{DESCRIPTION}`&ndash; Opis usługi (na `My sample service`przykład ).
* `{DISPLAY NAME}`&ndash; Nazwa wyświetlana usługi `My Service`(na przykład ).

### <a name="start-a-service"></a>Uruchamianie usługi

Uruchom usługę za pomocą następującego polecenia programu PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Polecenie trwa kilka sekund, aby uruchomić usługę.

### <a name="determine-a-services-status"></a>Określanie stanu usługi

Aby sprawdzić stan usługi, użyj następującego polecenia programu PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

Stan jest zgłaszany jako jedna z następujących wartości:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Zatrzymywanie usługi

Zatrzymaj usługę za pomocą następującego polecenia programu Powershell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Usuwanie usługi

Po krótkim opóźnieniu zatrzymania usługi usuń usługę za pomocą następującego polecenia Programu Powershell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Obsługa zdarzeń uruchamiania i zatrzymywania

Aby <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>obsłużyć , <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>i <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> zdarzenia:

1. Utwórz klasę, która <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> wywodzi się z `OnStarting`, `OnStarted`i `OnStopping` metody:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Utwórz metodę <xref:Microsoft.AspNetCore.Hosting.IWebHost> rozszerzenia, `CustomWebHostService` która <xref:System.ServiceProcess.ServiceBase.Run*>przekazuje do :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. W `Program.Main`, `RunAsCustomService` wywołać metodę <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>rozszerzenia zamiast:

   ```csharp
   host.RunAsCustomService();
   ```

   Aby wyświetlić <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> lokalizację `Program.Main`w , zapoznaj się z przykładowym kodem pokazanym w sekcji [Typ wdrożenia.](#deployment-type)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

Usługi, które wchodzą w interakcję z żądaniami z Internetu lub sieci firmowej i znajdują się za serwerem proxy lub modułem równoważenia obciążenia, mogą wymagać dodatkowej konfiguracji. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Konfigurowanie punktów końcowych

Domyślnie ASP.NET Core wiąże się `http://localhost:5000`z programem . Skonfiguruj adres URL `ASPNETCORE_URLS` i port, ustawiając zmienną środowiskową.

Aby uzyskać dodatkowe podejścia do konfiguracji adresów URL i portów, zobacz odpowiedni artykuł na temat serwera:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Powyższe wskazówki obejmują obsługę punktów końcowych HTTPS. Na przykład skonfiguruj aplikację dla protokołu HTTPS, gdy uwierzytelnianie jest używane z usługą systemu Windows.

> [!NOTE]
> Użycie ASP.NET certyfikatu dewelopera HTTPS w celu zabezpieczenia punktu końcowego usługi nie jest obsługiwane.

## <a name="current-directory-and-content-root"></a>Bieżący katalog i katalog główny zawartości

Bieżący katalog roboczy <xref:System.IO.Directory.GetCurrentDirectory*> zwracany przez wywołanie usługi systemu Windows to folder *C:\\WINDOWS\\system32.* Folder *system32* nie jest odpowiednią lokalizacją do przechowywania plików usługi (na przykład plików ustawień). Użyj jednego z następujących podejść do obsługi i uzyskiwania dostępu do zasobów i plików ustawień usługi.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Ustawianie ścieżki głównej zawartości do folderu aplikacji

Jest <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> to ta sama `binPath` ścieżka podana do argumentu podczas tworzenia usługi. Zamiast dzwonić, `GetCurrentDirectory` aby utworzyć ścieżki do <xref:System.IO.Directory.SetCurrentDirectory*> plików ustawień, zadzwoń ze ścieżką do [katalogu głównego zawartości](xref:fundamentals/index#content-root)aplikacji .

W `Program.Main`obszarze , określ ścieżkę do folderu pliku wykonywalnego usługi i użyj ścieżki do ustanowienia katalogu głównego zawartości aplikacji:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Przechowywanie plików usługi w odpowiednim miejscu na dysku

Określ ścieżkę <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> bezwzględną <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> podczas korzystania z folderu zawierającego pliki.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby rozwiązać problem z <xref:test/troubleshoot>aplikacją Usługi systemu Windows, zobacz .

### <a name="common-errors"></a>Typowe błędy

* Używana jest stara lub wersja wstępna programu PowerShell.
* Zarejestrowana usługa nie używa **opublikowanych** danych wyjściowych aplikacji z polecenia [publikowania dotnet.](/dotnet/core/tools/dotnet-publish) Dane wyjściowe polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) nie jest obsługiwany dla wdrażania aplikacji. Opublikowane zasoby znajdują się w jednym z następujących folderów w zależności od typu wdrożenia:
  * *bin/release/{TARGET FRAMEWORK}/publish* (FDD)
  * *bin/release/{TARGET FRAMEWORK}/{IDENTYFIKATOR ŚRODOWISKA URUCHOMIENIOWEGO}/publish* (SCD)
* Usługa nie jest w stanie RUNNING.
* Ścieżki do zasobów używanych przez aplikację (na przykład certyfikaty) są niepoprawne. Podstawową ścieżką usługi systemu Windows jest *c:\\Windows\\System32*.
* Użytkownik nie ma *logowania jako* praw do usługi.
* Hasło użytkownika wygasło lub zostało niepoprawnie przekazane `New-Service` podczas wykonywania polecenia programu PowerShell.
* Aplikacja wymaga ASP.NET uwierzytelniania core, ale nie jest skonfigurowana do bezpiecznych połączeń (HTTPS).
* Port adresu URL żądania jest niepoprawny lub niepoprawnie skonfigurowany w aplikacji.

### <a name="system-and-application-event-logs"></a>Dzienniki zdarzeń systemu i aplikacji

Dostęp do dzienników zdarzeń systemu i aplikacji:

1. Otwórz menu Start, wyszukaj *podgląd zdarzeń*i wybierz aplikację **Podgląd zdarzeń.**
1. W **Podglądzie zdarzeń**otwórz węzeł **Dzienniki systemu Windows.**
1. Wybierz **system,** aby otworzyć dziennik zdarzeń systemu. Wybierz **opcję Aplikacja,** aby otworzyć dziennik zdarzeń aplikacji.
1. Wyszukaj błędy skojarzone z nieudaną aplikacją.

### <a name="run-the-app-at-a-command-prompt"></a>Uruchamianie aplikacji w wierszu polecenia

Wiele błędów uruchamiania nie generuje przydatnych informacji w dziennikach zdarzeń. Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingowym. Aby zarejestrować dodatkowe szczegóły z aplikacji, obniżyć [poziom dziennika](xref:fundamentals/logging/index#log-level) lub uruchomić aplikację w [środowisku deweloperskim](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Wyczyść pamięć podręczną pakietów

Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestawu .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w aplikacji. W niektórych przypadkach niespójne pakiety mogą spowodować przerwanie aplikacji podczas wykonywania głównych uaktualnień. Większość z tych problemów można rozwiązać, postępując zgodnie z następującymi instrukcjami:

1. Usuń *foldery bin* i *obj.*
1. Wyczyść bufory pakietu, wykonując [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z powłoki poleceń.

   Czyszczenie pamięci podręcznych pakietów można również wykonać za pomocą narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonać polecenie `nuget locals all -clear`. *nuget.exe* nie jest w pakiecie zainstalować z systemem operacyjnym Windows dla komputerów stacjonarnych i muszą być uzyskane oddzielnie od [strony internetowej NuGet](https://www.nuget.org/downloads).

1. Przywracanie i odbudowywać projekt.
1. Usuń wszystkie pliki w folderze wdrażania na serwerze przed ponowne wdrożeniem aplikacji.

### <a name="slow-or-hanging-app"></a>Powolna lub wisząca aplikacja

*Zrzut awaryjny* jest migawką pamięci systemu i może pomóc w określeniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikacja ulega awarii lub napotyka wyjątek

Uzyskiwanie i analizowanie zrzutu z [raportowania błędów systemu Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Utwórz folder do przechowywania `c:\dumps`plików zrzutu awaryjnego w pliku .
1. Uruchom [skrypt EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) o nazwie wykonywalnej aplikacji:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Uruchom aplikację w warunkach, które powodują awarię.
1. Po wystąpieniu awarii uruchom [skrypt DisableDumps PowerShell:](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Po awarii aplikacji i kolekcji zrzutu jest zakończona, aplikacja może zakończyć się normalnie. Skrypt programu PowerShell konfiguruje program WER do zbierania maksymalnie pięciu zrzutów na aplikację.

> [!WARNING]
> Zrzuty awaryjne mogą zająć dużą ilość miejsca na dysku (do kilku gigabajtów).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie

Gdy aplikacja *zawiesza* się (przestaje odpowiadać, ale nie ulega awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [Pliki zrzutu w trybie użytkownika: Wybieranie najlepszego narzędzia,](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) aby wybrać odpowiednie narzędzie do produkcji zrzutu.

#### <a name="analyze-the-dump"></a>Analizowanie zrzutu

Zrzut można analizować przy użyciu kilku metod. Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu w trybie użytkownika](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Konfiguracja punktu końcowego pustułka](xref:fundamentals/servers/kestrel#endpoint-configuration) (obejmuje konfigurację HTTPS i obsługę SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Aplikacja ASP.NET Core może być hostowana w systemie Windows jako [usługa systemu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez korzystania z usług IIS. Po uruchomieniu jako usługa systemu Windows aplikacja jest uruchamiana automatycznie po ponownym uruchomieniu serwera.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

* [ASP.NET Core SDK 2.1 lub nowszym](https://dotnet.microsoft.com/download)
* [Program PowerShell 6.2 lub nowszy](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Konfiguracja aplikacji

Aplikacja wymaga odwołań do pakietów dla [witryny Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) i [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Aby przetestować i debugować podczas uruchamiania poza usługą, dodaj kod, aby ustalić, czy aplikacja jest uruchomiona jako usługa lub aplikacja konsoli. Sprawdź, czy debuger jest `--console` dołączony lub przełącznik jest obecny. Jeśli którykolwiek z tych warunków jest spełniony (aplikacja <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>nie jest uruchamiana jako usługa), zadzwoń . Jeśli warunki są fałszywe (aplikacja jest uruchamiana jako usługa):

* Zadzwoń <xref:System.IO.Directory.SetCurrentDirectory*> i użyj ścieżki do opublikowanej lokalizacji aplikacji. Nie wywołaj, <xref:System.IO.Directory.GetCurrentDirectory*> aby uzyskać ścieżkę, ponieważ aplikacja usługi systemu Windows <xref:System.IO.Directory.GetCurrentDirectory*> zwraca folder *C:\\WINDOWS\\system32,* gdy jest wywoływana. Aby uzyskać więcej informacji, zobacz [sekcję Bieżący katalog i katalog zawartości.](#current-directory-and-content-root) Ten krok jest wykonywany przed skonfigurowaniem aplikacji w pliku `CreateWebHostBuilder`.
* Wywołanie, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> aby uruchomić aplikację jako usługę.

Ponieważ [dostawca konfiguracji wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) wymaga par nazwa-wartość dla `--console` argumentów wiersza <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> polecenia, przełącznik jest usuwany z argumentów przed odebraniem argumentów.

Aby zapisać w dzienniku zdarzeń systemu Windows, dodaj dostawcę EventLog do <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Ustaw poziom rejestrowania `Logging:LogLevel:Default` za pomocą klucza w *appsettings. Plik Production.json.*

W poniższym przykładzie z `RunAsCustomService` przykładowej aplikacji, jest wywoływana zamiast <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> w celu obsługi zdarzeń lifetime w aplikacji. Aby uzyskać więcej informacji, zobacz [Handle uruchamiania i zatrzymywania zdarzeń](#handle-starting-and-stopping-events) sekcji.

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Typ wdrożenia

Aby uzyskać informacje i porady dotyczące scenariuszy wdrażania, zobacz [wdrożenie aplikacji .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>SDK

W przypadku usługi opartej na aplikacjach sieci web, która używa stron Razor Pages lub struktur MVC, określ składnik Web SDK w pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Jeśli usługa wykonuje tylko zadania w tle (na przykład [usługi hostowane),](xref:fundamentals/host/hosted-services)określ sdk roboczy w pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Wdrożenie zależne od struktury (FDD)

Wdrożenie zależne od struktury (FDD) opiera się na obecności udostępnionej wersji systemu .NET Core w systemie docelowym. Gdy scenariusz FDD zostanie przyjęty zgodnie ze wskazówkami zawartymi w tym artykule, SDK tworzy plik wykonywalny (*.exe*), nazywany *wykonywalnym zależnym od struktury*.

Identyfikator środowiska wykonawczego systemu Windows [(RID)](/dotnet/core/rid-catalog) [\<(RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) zawiera platformę docelową. W poniższym przykładzie identyfikator `win7-x64`RID jest ustawiony na . Właściwość `<SelfContained>` jest `false`ustawiona na . Te właściwości instruują SDK do generowania pliku wykonywalnego *(exe)* dla systemu Windows i aplikacji, która zależy od udostępnionej struktury .NET Core.

Właściwość `<UseAppHost>` jest `true`ustawiona na . Ta właściwość zapewnia usługi ze ścieżką aktywacji (wykonywalny, *.exe)* dla FDD.

Plik *web.config,* który jest zwykle produkowany podczas publikowania aplikacji ASP.NET Core, nie jest potrzebny dla aplikacji Usług systemu Windows. Aby wyłączyć tworzenie pliku *web.config,* `<IsTransformWebConfigDisabled>` dodaj właściwość ustawioną na `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Samodzielne wdrażanie (SCD)

Samodzielne wdrożenie (SCD) nie opiera się na obecności udostępnionej struktury w systemie hosta. Środowisko wykonawcze i zależności aplikacji są wdrażane za pomocą aplikacji.

Identyfikator środowiska wykonawczego systemu Windows [(RID)](/dotnet/core/rid-catalog) `<PropertyGroup>` znajduje się w platformie zawierającej platformę docelową:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Aby opublikować wiele identyfikatorów RID:

* Podaj identyfikatory RID na liście rozdzielanych średnikami.
* Nazwa właściwości [ \<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (liczba mnoga).

Aby uzyskać więcej informacji, zobacz [.NET Core RID Catalog](/dotnet/core/rid-catalog).

Właściwość `<SelfContained>` jest `true`ustawiona na:

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Konto użytkownika usługi

Aby utworzyć konto użytkownika dla usługi, użyj polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z administracyjnej powłoki poleceń programu PowerShell 6.

W systemie Windows 10 October 2018 Update (wersja 1809/kompilacja 10.0.17763) lub nowszej:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

W systemie operacyjnym Windows wcześniej niż Windows 10 October 2018 Update (wersja 1809/build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Po wyświetleniu monitu podaj [silne hasło.](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)

O `-AccountExpires` ile parametr nie zostanie dostarczony do polecenia cmdlet <xref:System.DateTime> [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z wygaśnięciem, konto nie wygaśnie.

Aby uzyskać więcej informacji, zobacz [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) i [Konta użytkowników usług](/windows/desktop/services/service-user-accounts).

Alternatywnym podejściem do zarządzania użytkownikami podczas korzystania z usługi Active Directory jest użycie kont usług zarządzanych. Aby uzyskać więcej informacji, zobacz [Omówienie grupowych kont usług zarządzanych](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Zaloguj się jako prawa do usługi

Aby *ustanowić logowanie jako* prawa do usługi dla konta użytkownika usługi:

1. Otwórz edytor lokalnych zasad zabezpieczeń, uruchamiając *plik secpol.msc*.
1. Rozwiń węzeł **Zasady lokalne** i wybierz pozycję **Przypisanie praw użytkownika**.
1. Otwórz **zasadę Logowanie jako usługi.**
1. Wybierz **pozycję Dodaj użytkownika lub grupę**.
1. Podaj nazwę obiektu (konto użytkownika) przy użyciu jednej z następujących metod:
   1. Wpisz konto użytkownika`{DOMAIN OR COMPUTER NAME\USER}`( ) w polu nazwa obiektu i wybierz **przycisk OK,** aby dodać użytkownika do zasad.
   1. Wybierz **pozycję Zaawansowane**. Wybierz **pozycję Znajdź teraz**. Wybierz konto użytkownika z listy. Kliknij przycisk **OK**. Wybierz **przycisk OK** ponownie, aby dodać użytkownika do zasad.
1. Wybierz **przycisk OK** lub **Zastosuj,** aby zaakceptować zmiany.

## <a name="create-and-manage-the-windows-service"></a>Tworzenie usługi systemu Windows i zarządzanie nią

### <a name="create-a-service"></a>Tworzenie usługi

Użyj poleceń programu PowerShell, aby zarejestrować usługę. Z administracyjnej powłoki poleceń programu PowerShell 6 wykonaj następujące polecenia:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`&ndash; Ścieżka do folderu aplikacji na hoście `d:\myservice`(na przykład ). Nie dołączaj pliku wykonywalnego aplikacji do ścieżki. Ukośnik na końcu nie jest wymagany.
* `{DOMAIN OR COMPUTER NAME\USER}`&ndash; Konto użytkownika usługi (na przykład `Contoso\ServiceUser`).
* `{SERVICE NAME}`&ndash; Nazwa usługi (na `MyService`przykład ).
* `{EXE FILE PATH}`&ndash; Ścieżka wykonywalna aplikacji `d:\myservice\myservice.exe`(na przykład ). Dołącz nazwę pliku wykonywalnego z rozszerzeniem.
* `{DESCRIPTION}`&ndash; Opis usługi (na `My sample service`przykład ).
* `{DISPLAY NAME}`&ndash; Nazwa wyświetlana usługi `My Service`(na przykład ).

### <a name="start-a-service"></a>Uruchamianie usługi

Uruchom usługę za pomocą następującego polecenia programu PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Polecenie trwa kilka sekund, aby uruchomić usługę.

### <a name="determine-a-services-status"></a>Określanie stanu usługi

Aby sprawdzić stan usługi, użyj następującego polecenia programu PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

Stan jest zgłaszany jako jedna z następujących wartości:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Zatrzymywanie usługi

Zatrzymaj usługę za pomocą następującego polecenia programu Powershell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Usuwanie usługi

Po krótkim opóźnieniu zatrzymania usługi usuń usługę za pomocą następującego polecenia Programu Powershell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Obsługa zdarzeń uruchamiania i zatrzymywania

Aby <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>obsłużyć , <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>i <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> zdarzenia:

1. Utwórz klasę, która <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> wywodzi się z `OnStarting`, `OnStarted`i `OnStopping` metody:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Utwórz metodę <xref:Microsoft.AspNetCore.Hosting.IWebHost> rozszerzenia, `CustomWebHostService` która <xref:System.ServiceProcess.ServiceBase.Run*>przekazuje do :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. W `Program.Main`, `RunAsCustomService` wywołać metodę <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>rozszerzenia zamiast:

   ```csharp
   host.RunAsCustomService();
   ```

   Aby wyświetlić <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> lokalizację `Program.Main`w , zapoznaj się z przykładowym kodem pokazanym w sekcji [Typ wdrożenia.](#deployment-type)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

Usługi, które wchodzą w interakcję z żądaniami z Internetu lub sieci firmowej i znajdują się za serwerem proxy lub modułem równoważenia obciążenia, mogą wymagać dodatkowej konfiguracji. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Konfigurowanie punktów końcowych

Domyślnie ASP.NET Core wiąże się `http://localhost:5000`z programem . Skonfiguruj adres URL `ASPNETCORE_URLS` i port, ustawiając zmienną środowiskową.

Aby uzyskać dodatkowe podejścia do konfiguracji adresów URL i portów, zobacz odpowiedni artykuł na temat serwera:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Powyższe wskazówki obejmują obsługę punktów końcowych HTTPS. Na przykład skonfiguruj aplikację dla protokołu HTTPS, gdy uwierzytelnianie jest używane z usługą systemu Windows.

> [!NOTE]
> Użycie ASP.NET certyfikatu dewelopera HTTPS w celu zabezpieczenia punktu końcowego usługi nie jest obsługiwane.

## <a name="current-directory-and-content-root"></a>Bieżący katalog i katalog główny zawartości

Bieżący katalog roboczy <xref:System.IO.Directory.GetCurrentDirectory*> zwracany przez wywołanie usługi systemu Windows to folder *C:\\WINDOWS\\system32.* Folder *system32* nie jest odpowiednią lokalizacją do przechowywania plików usługi (na przykład plików ustawień). Użyj jednego z następujących podejść do obsługi i uzyskiwania dostępu do zasobów i plików ustawień usługi.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Ustawianie ścieżki głównej zawartości do folderu aplikacji

Jest <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> to ta sama `binPath` ścieżka podana do argumentu podczas tworzenia usługi. Zamiast dzwonić, `GetCurrentDirectory` aby utworzyć ścieżki do <xref:System.IO.Directory.SetCurrentDirectory*> plików ustawień, zadzwoń ze ścieżką do [katalogu głównego zawartości](xref:fundamentals/index#content-root)aplikacji .

W `Program.Main`obszarze , określ ścieżkę do folderu pliku wykonywalnego usługi i użyj ścieżki do ustanowienia katalogu głównego zawartości aplikacji:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Przechowywanie plików usługi w odpowiednim miejscu na dysku

Określ ścieżkę <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> bezwzględną <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> podczas korzystania z folderu zawierającego pliki.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby rozwiązać problem z <xref:test/troubleshoot>aplikacją Usługi systemu Windows, zobacz .

### <a name="common-errors"></a>Typowe błędy

* Używana jest stara lub wersja wstępna programu PowerShell.
* Zarejestrowana usługa nie używa **opublikowanych** danych wyjściowych aplikacji z polecenia [publikowania dotnet.](/dotnet/core/tools/dotnet-publish) Dane wyjściowe polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) nie jest obsługiwany dla wdrażania aplikacji. Opublikowane zasoby znajdują się w jednym z następujących folderów w zależności od typu wdrożenia:
  * *bin/release/{TARGET FRAMEWORK}/publish* (FDD)
  * *bin/release/{TARGET FRAMEWORK}/{IDENTYFIKATOR ŚRODOWISKA URUCHOMIENIOWEGO}/publish* (SCD)
* Usługa nie jest w stanie RUNNING.
* Ścieżki do zasobów używanych przez aplikację (na przykład certyfikaty) są niepoprawne. Podstawową ścieżką usługi systemu Windows jest *c:\\Windows\\System32*.
* Użytkownik nie ma *logowania jako* praw do usługi.
* Hasło użytkownika wygasło lub zostało niepoprawnie przekazane `New-Service` podczas wykonywania polecenia programu PowerShell.
* Aplikacja wymaga ASP.NET uwierzytelniania core, ale nie jest skonfigurowana do bezpiecznych połączeń (HTTPS).
* Port adresu URL żądania jest niepoprawny lub niepoprawnie skonfigurowany w aplikacji.

### <a name="system-and-application-event-logs"></a>Dzienniki zdarzeń systemu i aplikacji

Dostęp do dzienników zdarzeń systemu i aplikacji:

1. Otwórz menu Start, wyszukaj *podgląd zdarzeń*i wybierz aplikację **Podgląd zdarzeń.**
1. W **Podglądzie zdarzeń**otwórz węzeł **Dzienniki systemu Windows.**
1. Wybierz **system,** aby otworzyć dziennik zdarzeń systemu. Wybierz **opcję Aplikacja,** aby otworzyć dziennik zdarzeń aplikacji.
1. Wyszukaj błędy skojarzone z nieudaną aplikacją.

### <a name="run-the-app-at-a-command-prompt"></a>Uruchamianie aplikacji w wierszu polecenia

Wiele błędów uruchamiania nie generuje przydatnych informacji w dziennikach zdarzeń. Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingowym. Aby zarejestrować dodatkowe szczegóły z aplikacji, obniżyć [poziom dziennika](xref:fundamentals/logging/index#log-level) lub uruchomić aplikację w [środowisku deweloperskim](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Wyczyść pamięć podręczną pakietów

Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestawu .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w aplikacji. W niektórych przypadkach niespójne pakiety mogą spowodować przerwanie aplikacji podczas wykonywania głównych uaktualnień. Większość z tych problemów można rozwiązać, postępując zgodnie z następującymi instrukcjami:

1. Usuń *foldery bin* i *obj.*
1. Wyczyść bufory pakietu, wykonując [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) z powłoki poleceń.

   Czyszczenie pamięci podręcznych pakietów można również wykonać za pomocą narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonać polecenie `nuget locals all -clear`. *nuget.exe* nie jest w pakiecie zainstalować z systemem operacyjnym Windows dla komputerów stacjonarnych i muszą być uzyskane oddzielnie od [strony internetowej NuGet](https://www.nuget.org/downloads).

1. Przywracanie i odbudowywać projekt.
1. Usuń wszystkie pliki w folderze wdrażania na serwerze przed ponowne wdrożeniem aplikacji.

### <a name="slow-or-hanging-app"></a>Powolna lub wisząca aplikacja

*Zrzut awaryjny* jest migawką pamięci systemu i może pomóc w określeniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.

#### <a name="app-crashes-or-encounters-an-exception"></a>Aplikacja ulega awarii lub napotyka wyjątek

Uzyskiwanie i analizowanie zrzutu z [raportowania błędów systemu Windows (WER)](/windows/desktop/wer/windows-error-reporting):

1. Utwórz folder do przechowywania `c:\dumps`plików zrzutu awaryjnego w pliku .
1. Uruchom [skrypt EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) o nazwie wykonywalnej aplikacji:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Uruchom aplikację w warunkach, które powodują awarię.
1. Po wystąpieniu awarii uruchom [skrypt DisableDumps PowerShell:](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Po awarii aplikacji i kolekcji zrzutu jest zakończona, aplikacja może zakończyć się normalnie. Skrypt programu PowerShell konfiguruje program WER do zbierania maksymalnie pięciu zrzutów na aplikację.

> [!WARNING]
> Zrzuty awaryjne mogą zająć dużą ilość miejsca na dysku (do kilku gigabajtów).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie

Gdy aplikacja *zawiesza* się (przestaje odpowiadać, ale nie ulega awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [Pliki zrzutu w trybie użytkownika: Wybieranie najlepszego narzędzia,](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) aby wybrać odpowiednie narzędzie do produkcji zrzutu.

#### <a name="analyze-the-dump"></a>Analizowanie zrzutu

Zrzut można analizować przy użyciu kilku metod. Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu w trybie użytkownika](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Konfiguracja punktu końcowego pustułka](xref:fundamentals/servers/kestrel#endpoint-configuration) (obejmuje konfigurację HTTPS i obsługę SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
