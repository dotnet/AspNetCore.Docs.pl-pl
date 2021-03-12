---
title: ASP.NET Core hosta w usłudze systemu Windows
author: rick-anderson
description: Dowiedz się, jak hostować aplikację ASP.NET Core w usłudze systemu Windows.
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: e5c7dd0e52f1246d3ac6ad9622573db4c276654b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588818"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>ASP.NET Core hosta w usłudze systemu Windows

::: moniker range=">= aspnetcore-3.0"

Aplikacja ASP.NET Core może być hostowana w systemie Windows jako [Usługa systemu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez korzystania z usług IIS. Gdy usługa jest hostowana w systemie Windows, aplikacja jest uruchamiana automatycznie po ponownym uruchomieniu serwera.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

* [ASP.NET Core zestaw SDK 2,1 lub nowszy](https://dotnet.microsoft.com/download)
* [Program PowerShell 6,2 lub nowszy](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a>Szablon usługi procesu roboczego

Szablon usługi ASP.NET Core Worker zapewnia punkt początkowy do pisania długotrwałych aplikacji usługi. Aby użyć szablonu jako podstawy dla aplikacji usługi systemu Windows:

1. Utwórz aplikację usługi procesu roboczego na podstawie szablonu .NET Core.
1. Postępuj zgodnie ze wskazówkami w sekcji [Konfiguracja aplikacji](#app-configuration) , aby zaktualizować aplikację usługi procesu roboczego tak, aby mogła ona działać jako usługa systemu Windows.

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a>Konfiguracja aplikacji

Aplikacja wymaga odwołania do pakietu dla elementu [Microsoft. Extensions. hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).

`IHostBuilder.UseWindowsService` jest wywoływana podczas kompilowania hosta. Jeśli aplikacja działa jako usługa systemu Windows, Metoda:

* Ustawia okres istnienia hosta na `WindowsServiceLifetime` .
* Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory). Aby uzyskać więcej informacji, zapoznaj się z sekcją [Current Directory i root Content](#current-directory-and-content-root) .
* Włącza rejestrowanie w dzienniku zdarzeń:
  * Nazwa aplikacji jest używana jako domyślna nazwa źródła.
  * Domyślny poziom rejestrowania jest *ostrzegawczy* lub wyższy dla aplikacji opartej na szablonie ASP.NET Core, który wywołuje, `CreateDefaultBuilder` Aby skompilować hosta.
  * Zastąp domyślny poziom dziennika `Logging:EventLog:LogLevel:Default` kluczem w *appsettings.json* / *appSettings. { Environment}. JSON* lub inny dostawca konfiguracji.
  * Tylko Administratorzy mogą tworzyć nowe źródła zdarzeń. Gdy nie można utworzyć źródła zdarzeń przy użyciu nazwy aplikacji, w źródle *aplikacji* jest rejestrowane ostrzeżenie, a dzienniki zdarzeń są wyłączone.

W `CreateHostBuilder` programie *program.cs*:

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

Następujące przykładowe aplikacje zostały dołączone do tego tematu:

* Przykład usługi procesu roboczego w tle: przykład aplikacji innej niż Internet oparta na [szablonie usługi procesu roboczego](#worker-service-template) , który korzysta z [usług hostowanych](xref:fundamentals/host/hosted-services) na potrzeby zadań w tle.
* Przykład App Service sieci Web: Razor Przykładowa aplikacja sieci Web, która działa jako usługa systemu Windows z [usługami hostowanymi](xref:fundamentals/host/hosted-services) dla zadań w tle.

Wskazówki dotyczące MVC znajdują się w artykułach <xref:mvc/overview> i <xref:migration/22-to-30> .

## <a name="deployment-type"></a>Typ wdrożenia

Aby uzyskać informacje i porady dotyczące scenariuszy wdrażania, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>SDK

W przypadku usługi opartej na aplikacji sieci Web korzystającej ze Razor stron lub platform MVC należy określić zestaw SDK sieci Web w pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Jeśli usługa wykonuje tylko zadania w tle (na przykład [usługi hostowane](xref:fundamentals/host/hosted-services)), określ zestaw SDK procesu roboczego w pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Wdrożenie zależne od platformy (FDD)

Wdrożenie zależne od platformy (FDD) zależy od obecności udostępnionej systemowej wersji platformy .NET Core w systemie docelowym. Po przyjęciu scenariusza FDD zgodnie ze wskazówkami zawartymi w tym artykule zestaw SDK tworzy plik wykonywalny (*. exe*), nazywany *plik wykonywalny zależny od platformy*.

Jeśli używasz [zestawu Web SDK](#sdk), plik *web.config* , który jest zwykle tworzony podczas publikowania aplikacji ASP.NET Core, nie jest konieczny dla aplikacji usług systemu Windows. Aby wyłączyć tworzenie pliku *web.config* , należy dodać `<IsTransformWebConfigDisabled>` Właściwość ustawioną na `true` .

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Wdrażanie samodzielne (SCD)

Wdrożenie samodzielne (SCD) nie polega na obecności struktury udostępnionej w systemie hosta. Środowisko uruchomieniowe i zależności aplikacji są wdrażane przy użyciu aplikacji.

[Identyfikator środowiska uruchomieniowego systemu Windows (RID)](/dotnet/core/rid-catalog) znajduje się w, `<PropertyGroup>` który zawiera platformę docelową:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Aby opublikować dla wielu identyfikatorów RID:

* Podaj identyfikatory RID na liście rozdzielanej średnikami.
* Użyj nazwy właściwości [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).

Aby uzyskać więcej informacji, zobacz [katalog .NET Core RID Catalog](/dotnet/core/rid-catalog).

## <a name="service-user-account"></a>Konto użytkownika usługi

Aby utworzyć konto użytkownika dla usługi, należy użyć polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) w powłoce poleceń administracyjnych programu PowerShell 6.

W systemie Windows 10 października 2018 Update (wersja 1809/Kompilacja 10.0.17763) lub nowsza:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

W systemie operacyjnym Windows w wersji starszej niż aktualizacja 2018 systemu Windows 10 października (wersja 1809/Kompilacja 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Podaj [silne hasło](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) po wyświetleniu monitu.

Jeśli `-AccountExpires` parametr nie zostanie dostarczony do polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z wygaśnięciem <xref:System.DateTime> , konto nie wygaśnie.

Aby uzyskać więcej informacji, zobacz [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) i [konta użytkowników usług](/windows/desktop/services/service-user-accounts).

Alternatywna metoda zarządzania użytkownikami podczas korzystania z Active Directory polega na użyciu zarządzanych kont usług. Aby uzyskać więcej informacji, zobacz [Omówienie kont usług zarządzanych przez grupę](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Logowanie jako prawa usługi

Aby nawiązać *Logowanie jako prawa usługi* dla konta użytkownika usługi:

1. Otwórz Edytor lokalnych zasad zabezpieczeń, uruchamiając program *secpol. msc*.
1. Rozwiń węzeł **Zasady lokalne** , a następnie wybierz pozycję **Przypisywanie praw użytkownika**.
1. Otwórz okno **Logowanie jako usługa** .
1. Wybierz pozycję **Dodaj użytkownika lub grupę**.
1. Podaj nazwę obiektu (konto użytkownika) przy użyciu jednej z następujących metod:
   1. Wpisz konto użytkownika ( `{DOMAIN OR COMPUTER NAME\USER}` ) w polu Nazwa obiektu, a następnie wybierz **przycisk OK** , aby dodać użytkownika do zasad.
   1. Wybierz pozycję **Zaawansowane**. Wybierz pozycję **Znajdź teraz**. Wybierz z listy konto użytkownika. Wybierz przycisk **OK**. Ponownie wybierz **przycisk OK** , aby dodać użytkownika do zasad.
1. Wybierz **przycisk OK** lub **Zastosuj** , aby zaakceptować zmiany.

## <a name="create-and-manage-the-windows-service"></a>Tworzenie usługi systemu Windows i zarządzanie nią

### <a name="create-a-service"></a>Tworzenie usługi

Zarejestrowanie usługi za pomocą poleceń programu PowerShell. Z poziomu powłoki poleceń administracyjnych programu PowerShell 6 wykonaj następujące polecenia:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`: Ścieżka do folderu aplikacji na hoście (na przykład `d:\myservice` ). Nie dołączaj pliku wykonywalnego aplikacji do ścieżki. Końcowy ukośnik nie jest wymagany.
* `{DOMAIN OR COMPUTER NAME\USER}`: Konto użytkownika usługi (na przykład `Contoso\ServiceUser` ).
* `{SERVICE NAME}`: Nazwa usługi (na przykład `MyService` ).
* `{EXE FILE PATH}`: Ścieżka pliku wykonywalnego aplikacji (na przykład `d:\myservice\myservice.exe` ). Uwzględnij nazwę pliku wykonywalnego z rozszerzeniem.
* `{DESCRIPTION}`: Opis usługi (na przykład `My sample service` ).
* `{DISPLAY NAME}`: Nazwa wyświetlana usługi (na przykład `My Service` ).

### <a name="start-a-service"></a>Uruchamianie usługi

Uruchom usługę za pomocą następującego polecenia programu PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Uruchomienie usługi może potrwać kilka sekund.

### <a name="determine-a-services-status"></a>Określanie stanu usługi

Aby sprawdzić stan usługi, użyj następującego polecenia programu PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

Stan jest raportowany jako jedna z następujących wartości:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Zatrzymywanie usługi

Zatrzymaj usługę za pomocą następującego polecenia programu PowerShell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Usuwanie usługi

Po krótkim opóźnieniu na zatrzymanie usługi Usuń usługę za pomocą następującego polecenia programu PowerShell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

Usługi, które współdziałają z żądaniami z Internetu lub sieci firmowej i znajdują się za serwerem proxy lub modułem równoważenia obciążenia, mogą wymagać dodatkowej konfiguracji. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Konfigurowanie punktów końcowych

Domyślnie ASP.NET Core wiąże się z `http://localhost:5000` . Skonfiguruj adres URL i port przez ustawienie `ASPNETCORE_URLS` zmiennej środowiskowej.

Aby uzyskać dodatkowe podejścia do konfiguracji adresów URL i portów, zobacz artykuł dotyczący odpowiedniego serwera:

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <xref:fundamentals/servers/kestrel/endpoints>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

* <xref:fundamentals/servers/kestrel#endpoint-configuration>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <xref:fundamentals/servers/httpsys#configure-windows-server>

Powyższe wskazówki obejmują obsługę punktów końcowych HTTPS. Na przykład skonfiguruj aplikację do obsługi protokołu HTTPS, gdy uwierzytelnianie jest używane z usługą systemu Windows.

> [!NOTE]
> Korzystanie z ASP.NET Core certyfikatu deweloperskiego HTTPS w celu zabezpieczenia punktu końcowego usługi nie jest obsługiwane.

## <a name="current-directory-and-content-root"></a>Bieżący katalog i główna Zawartość

Bieżącym katalogiem roboczym zwróconym przez wywołanie <xref:System.IO.Directory.GetCurrentDirectory%2A> dla usługi systemu Windows jest folder *C: \\ Windows \\ system32* . Folder *system32* nie jest odpowiednią lokalizacją do przechowywania plików usługi (na przykład plików ustawień). Użyj jednego z poniższych metod, aby zachować i uzyskać dostęp do plików ustawień i zasobów usługi.

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>Użyj ContentRootPath lub ContentRootFileProvider

Użyj [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) lub <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> do lokalizowania zasobów aplikacji.

Gdy aplikacja działa jako usługa, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> ustawia wartość <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> na [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).

Domyślne pliki ustawień aplikacji *appsettings.json* i *appSettings. { Environment}. JSON* jest ładowany z katalogu głównego zawartości aplikacji przez wywołanie [CreateDefaultBuilder podczas konstruowania hosta](xref:fundamentals/host/generic-host#set-up-a-host).

W przypadku innych plików ustawień ładowanych przez kod dewelopera w <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> , nie ma potrzeby wywoływania <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> . W poniższym przykładzie *custom_settings.jsw* pliku istnieje w katalogu głównym zawartości aplikacji i jest ładowany bez jawnego ustawiania ścieżki podstawowej:

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

Nie należy próbować użyć <xref:System.IO.Directory.GetCurrentDirectory%2A> programu w celu uzyskania ścieżki zasobu, ponieważ aplikacja usługi systemu Windows zwraca folder *C: \\ Windows \\ system32* jako bieżący katalog.

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Przechowywanie plików usługi w odpowiedniej lokalizacji na dysku

Określ ścieżkę bezwzględną przy <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> użyciu <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do folderu zawierającego pliki.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby rozwiązać problem z aplikacją usługi systemu Windows, zobacz <xref:test/troubleshoot> .

### <a name="common-errors"></a>Typowe błędy

* Starsza lub wstępnie wydana wersja programu PowerShell jest używana.
* Zarejestrowana usługa nie używa **opublikowanych** danych wyjściowych aplikacji z [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenia. Dane wyjściowe polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) nie są obsługiwane w przypadku wdrażania aplikacji. Opublikowane zasoby znajdują się w dowolnym z następujących folderów w zależności od typu wdrożenia:
  * *bin/Release/{Target Framework}/Publish* (FDD)
  * *bin/Release/{Target Framework}/{Runtime identyfikator}/Publish* (SCD)
* Usługa nie jest w stanie uruchomienia.
* Ścieżki do zasobów używanych przez aplikację (na przykład certyfikaty) są nieprawidłowe. Ścieżką podstawową usługi systemu Windows jest *c: \\ Windows \\ system32*.
* Użytkownik nie ma uprawnień do *logowania się jako usługa* .
* Hasło użytkownika wygasło lub zostało nieprawidłowo przesłane podczas wykonywania `New-Service` polecenia programu PowerShell.
* Aplikacja wymaga uwierzytelniania ASP.NET Core, ale nie jest skonfigurowana dla połączeń Secure (HTTPS).
* Port adresu URL żądania jest niepoprawny lub niepoprawnie skonfigurowany w aplikacji.

### <a name="system-and-application-event-logs"></a>Dzienniki zdarzeń systemu i aplikacji

Uzyskaj dostęp do dzienników zdarzeń systemu i aplikacji:

1. Otwórz menu Start, wyszukaj ciąg *Podgląd zdarzeń* i wybierz aplikację **Podgląd zdarzeń** .
1. W **Podgląd zdarzeń** Otwórz węzeł **Dzienniki systemu Windows** .
1. Wybierz pozycję **system** , aby otworzyć dziennik zdarzeń systemu. Wybierz pozycję **aplikacja** , aby otworzyć dziennik zdarzeń aplikacji.
1. Wyszukaj błędy związane z niepowodzeniem aplikacji.

### <a name="run-the-app-at-a-command-prompt"></a>Uruchamianie aplikacji w wierszu polecenia

Wiele błędów uruchamiania nie produkuje użytecznych informacji w dziennikach zdarzeń. Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingu. Aby rejestrować dodatkowe szczegóły aplikacji, Obniż [poziom rejestrowania](xref:fundamentals/logging/index#log-level) lub Uruchom aplikację w [środowisku deweloperskim](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Wyczyść pamięć podręczną pakietów

Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji. W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych. Większość tych problemów można naprawić, wykonując następujące instrukcje:

1. Usuń foldery *bin* i *obj* .
1. Wyczyść pamięć podręczną pakietów, wykonując [wszystkie elementy lokalne usługi NuGet programu dotnet--Wyczyść](/dotnet/core/tools/dotnet-nuget-locals) z poziomu powłoki poleceń.

   Czyszczenie pamięci podręcznych pakietów można także wykonać przy użyciu narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonując polecenie `nuget locals all -clear` . *nuget.exe* nie jest instalacją w pakiecie z systemem operacyjnym Windows dla komputerów stacjonarnych i należy ją pobrać niezależnie od [witryny sieci Web programu NuGet](https://www.nuget.org/downloads).

1. Przywróć i skompiluj projekt.
1. Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.

### <a name="slow-or-hanging-app"></a>Aplikacja wolna lub wysunięta

*Zrzut awaryjny* to migawka pamięci systemu, która może pomóc w ustaleniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.

#### <a name="app-crashes-or-encounters-an-exception"></a>Awaria aplikacji lub napotka wyjątek

Uzyskaj i Analizuj Zrzut z [raportowanie błędów systemu Windows (raportowanie błędów systemu Windows)](/windows/desktop/wer/windows-error-reporting):

1. Utwórz folder do przechowywania plików zrzutu awaryjnego w `c:\dumps` .
1. Uruchom [skrypt programu PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) w programie EnableDumps z nazwą pliku wykonywalnego aplikacji:

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Uruchom aplikację w warunkach, które powodują awarię.
1. Po wystąpieniu awarii Uruchom [skrypt programu DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

Po awarii aplikacji i zakończeniu zbierania zrzutów aplikacja może zakończyć normalne działanie. Skrypt programu PowerShell konfiguruje raportowanie błędów systemu Windows w celu zebrania do pięciu zrzutów na aplikację.

> [!WARNING]
> Zrzuty awaryjne mogą wymagać dużej ilości miejsca na dysku (do kilku gigabajtów).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie

Gdy aplikacja *zawiesza* się (bez awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [pliki zrzutu w trybie użytkownika: wybór najlepszego narzędzia](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) do wygenerowania zrzutu.

#### <a name="analyze-the-dump"></a>Analizowanie zrzutu

Zrzut można analizować przy użyciu kilku metod. Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu User-Mode](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Dodatkowe zasoby

::: moniker-end

::: moniker range=">= aspnetcore-5.0"
* [Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel/endpoints) (w tym Konfiguracja protokołu HTTPS i obsługa SNI)
::: moniker-end
::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"
* [Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (w tym Konfiguracja protokołu HTTPS i obsługa SNI)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Aplikacja ASP.NET Core może być hostowana w systemie Windows jako [Usługa systemu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez korzystania z usług IIS. Gdy usługa jest hostowana w systemie Windows, aplikacja jest uruchamiana automatycznie po ponownym uruchomieniu serwera.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

* [ASP.NET Core zestaw SDK 2,1 lub nowszy](https://dotnet.microsoft.com/download)
* [Program PowerShell 6,2 lub nowszy](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Konfiguracja aplikacji

Aplikacja wymaga odwołania do pakietów dla elementu [Microsoft. AspNetCore. hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) i [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Aby przetestować i debugować w przypadku uruchamiania poza usługą, należy dodać kod, aby określić, czy aplikacja działa jako usługa, czy Aplikacja konsolowa. Sprawdź, czy debuger jest dołączony lub czy `--console` jest obecny przełącznik. Jeśli którykolwiek z warunków jest spełniony (aplikacja nie jest uruchomiona jako usługa), wywołaj metodę <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> . Jeśli warunki są fałszywe (aplikacja jest uruchamiana jako usługa):

* Wywołaj <xref:System.IO.Directory.SetCurrentDirectory*> i użyj ścieżki do opublikowanej lokalizacji aplikacji. Nie wywołuj <xref:System.IO.Directory.GetCurrentDirectory*> , aby uzyskać ścieżkę, ponieważ aplikacja usługi systemu Windows zwraca folder *C: \\ Windows \\ system32* , gdy <xref:System.IO.Directory.GetCurrentDirectory*> jest wywoływana. Aby uzyskać więcej informacji, zapoznaj się z sekcją [Current Directory i root Content](#current-directory-and-content-root) . Ten krok jest wykonywany przed skonfigurowaniem aplikacji w programie `CreateWebHostBuilder` .
* Wywołaj <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> , aby uruchomić aplikację jako usługę.

Ponieważ [dostawca konfiguracji wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) wymaga par nazwa-wartość dla argumentów wiersza polecenia, `--console` przełącznik jest usuwany z argumentów przed <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> odebraniem argumentów.

Aby zapisać w dzienniku zdarzeń systemu Windows, Dodaj dostawcę EventLog do programu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> . Ustaw poziom rejestrowania z `Logging:LogLevel:Default` kluczem w *appsettings.Production.js* pliku.

W poniższym przykładzie z przykładowej aplikacji `RunAsCustomService` jest wywoływana zamiast <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> w celu obsługi zdarzeń okresu istnienia w aplikacji. Aby uzyskać więcej informacji, zobacz sekcję [Obsługa zdarzeń dotyczących uruchamiania i zatrzymywania](#handle-starting-and-stopping-events) .

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Typ wdrożenia

Aby uzyskać informacje i porady dotyczące scenariuszy wdrażania, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>SDK

W przypadku usługi opartej na aplikacji sieci Web korzystającej ze Razor stron lub platform MVC należy określić zestaw SDK sieci Web w pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Jeśli usługa wykonuje tylko zadania w tle (na przykład [usługi hostowane](xref:fundamentals/host/hosted-services)), określ zestaw SDK procesu roboczego w pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Wdrożenie zależne od platformy (FDD)

Wdrożenie zależne od platformy (FDD) zależy od obecności udostępnionej systemowej wersji platformy .NET Core w systemie docelowym. Po przyjęciu scenariusza FDD zgodnie ze wskazówkami zawartymi w tym artykule zestaw SDK tworzy plik wykonywalny (*. exe*), nazywany *plik wykonywalny zależny od platformy*.

[Identyfikator środowiska uruchomieniowego systemu Windows (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) zawiera platformę docelową. W poniższym przykładzie identyfikator RID jest ustawiony na `win7-x64` . Właściwość `<SelfContained>` ma ustawioną wartość `false`. Te właściwości instruują zestaw SDK, aby wygenerował plik wykonywalny (*exe*) dla systemu Windows i aplikację, która zależy od współużytkowanej platformy .NET Core.

Plik *web.config* , który jest zwykle tworzony podczas publikowania aplikacji ASP.NET Core, nie jest konieczny dla aplikacji usług systemu Windows. Aby wyłączyć tworzenie pliku *web.config* , należy dodać `<IsTransformWebConfigDisabled>` Właściwość ustawioną na `true` .

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Wdrażanie samodzielne (SCD)

Wdrożenie samodzielne (SCD) nie polega na obecności struktury udostępnionej w systemie hosta. Środowisko uruchomieniowe i zależności aplikacji są wdrażane przy użyciu aplikacji.

[Identyfikator środowiska uruchomieniowego systemu Windows (RID)](/dotnet/core/rid-catalog) znajduje się w, `<PropertyGroup>` który zawiera platformę docelową:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Aby opublikować dla wielu identyfikatorów RID:

* Podaj identyfikatory RID na liście rozdzielanej średnikami.
* Użyj nazwy właściwości [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).

Aby uzyskać więcej informacji, zobacz [katalog .NET Core RID Catalog](/dotnet/core/rid-catalog).

`<SelfContained>`Właściwość jest ustawiona na `true` :

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Konto użytkownika usługi

Aby utworzyć konto użytkownika dla usługi, należy użyć polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) w powłoce poleceń administracyjnych programu PowerShell 6.

W systemie Windows 10 października 2018 Update (wersja 1809/Kompilacja 10.0.17763) lub nowsza:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

W systemie operacyjnym Windows w wersji starszej niż aktualizacja 2018 systemu Windows 10 października (wersja 1809/Kompilacja 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Podaj [silne hasło](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) po wyświetleniu monitu.

Jeśli `-AccountExpires` parametr nie zostanie dostarczony do polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z wygaśnięciem <xref:System.DateTime> , konto nie wygaśnie.

Aby uzyskać więcej informacji, zobacz [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) i [konta użytkowników usług](/windows/desktop/services/service-user-accounts).

Alternatywna metoda zarządzania użytkownikami podczas korzystania z Active Directory polega na użyciu zarządzanych kont usług. Aby uzyskać więcej informacji, zobacz [Omówienie kont usług zarządzanych przez grupę](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Logowanie jako prawa usługi

Aby nawiązać *Logowanie jako prawa usługi* dla konta użytkownika usługi:

1. Otwórz Edytor lokalnych zasad zabezpieczeń, uruchamiając program *secpol. msc*.
1. Rozwiń węzeł **Zasady lokalne** , a następnie wybierz pozycję **Przypisywanie praw użytkownika**.
1. Otwórz okno **Logowanie jako usługa** .
1. Wybierz pozycję **Dodaj użytkownika lub grupę**.
1. Podaj nazwę obiektu (konto użytkownika) przy użyciu jednej z następujących metod:
   1. Wpisz konto użytkownika ( `{DOMAIN OR COMPUTER NAME\USER}` ) w polu Nazwa obiektu, a następnie wybierz **przycisk OK** , aby dodać użytkownika do zasad.
   1. Wybierz pozycję **Zaawansowane**. Wybierz pozycję **Znajdź teraz**. Wybierz z listy konto użytkownika. Wybierz przycisk **OK**. Ponownie wybierz **przycisk OK** , aby dodać użytkownika do zasad.
1. Wybierz **przycisk OK** lub **Zastosuj** , aby zaakceptować zmiany.

## <a name="create-and-manage-the-windows-service"></a>Tworzenie usługi systemu Windows i zarządzanie nią

### <a name="create-a-service"></a>Tworzenie usługi

Zarejestrowanie usługi za pomocą poleceń programu PowerShell. Z poziomu powłoki poleceń administracyjnych programu PowerShell 6 wykonaj następujące polecenia:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`: Ścieżka do folderu aplikacji na hoście (na przykład `d:\myservice` ). Nie dołączaj pliku wykonywalnego aplikacji do ścieżki. Końcowy ukośnik nie jest wymagany.
* `{DOMAIN OR COMPUTER NAME\USER}`: Konto użytkownika usługi (na przykład `Contoso\ServiceUser` ).
* `{SERVICE NAME}`: Nazwa usługi (na przykład `MyService` ).
* `{EXE FILE PATH}`: Ścieżka pliku wykonywalnego aplikacji (na przykład `d:\myservice\myservice.exe` ). Uwzględnij nazwę pliku wykonywalnego z rozszerzeniem.
* `{DESCRIPTION}`: Opis usługi (na przykład `My sample service` ).
* `{DISPLAY NAME}`: Nazwa wyświetlana usługi (na przykład `My Service` ).

### <a name="start-a-service"></a>Uruchamianie usługi

Uruchom usługę za pomocą następującego polecenia programu PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Uruchomienie usługi może potrwać kilka sekund.

### <a name="determine-a-services-status"></a>Określanie stanu usługi

Aby sprawdzić stan usługi, użyj następującego polecenia programu PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

Stan jest raportowany jako jedna z następujących wartości:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Zatrzymywanie usługi

Zatrzymaj usługę za pomocą następującego polecenia programu PowerShell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Usuwanie usługi

Po krótkim opóźnieniu na zatrzymanie usługi Usuń usługę za pomocą następującego polecenia programu PowerShell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Obsługa zdarzeń uruchamiania i zatrzymywania

Aby obsłużyć <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> zdarzenia, i <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :

1. Utwórz klasę, która dziedziczy z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> `OnStarting` `OnStarted` metody,, i `OnStopping` :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Utwórz metodę rozszerzenia dla <xref:Microsoft.AspNetCore.Hosting.IWebHost> , która przekazuje `CustomWebHostService` do <xref:System.ServiceProcess.ServiceBase.Run*> :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. W `Program.Main` , wywołaj `RunAsCustomService` metodę rozszerzenia zamiast <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :

   ```csharp
   host.RunAsCustomService();
   ```

   Aby zobaczyć lokalizację programu <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> w programie `Program.Main` , zapoznaj się z przykładem kodu pokazanym w sekcji [typ wdrożenia](#deployment-type) .

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

Usługi, które współdziałają z żądaniami z Internetu lub sieci firmowej i znajdują się za serwerem proxy lub modułem równoważenia obciążenia, mogą wymagać dodatkowej konfiguracji. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Konfigurowanie punktów końcowych

Domyślnie ASP.NET Core wiąże się z `http://localhost:5000` . Skonfiguruj adres URL i port przez ustawienie `ASPNETCORE_URLS` zmiennej środowiskowej.

Aby uzyskać dodatkowe podejścia do konfiguracji adresów URL i portów, zobacz artykuł dotyczący odpowiedniego serwera:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Powyższe wskazówki obejmują obsługę punktów końcowych HTTPS. Na przykład skonfiguruj aplikację do obsługi protokołu HTTPS, gdy uwierzytelnianie jest używane z usługą systemu Windows.

> [!NOTE]
> Korzystanie z ASP.NET Core certyfikatu deweloperskiego HTTPS w celu zabezpieczenia punktu końcowego usługi nie jest obsługiwane.

## <a name="current-directory-and-content-root"></a>Bieżący katalog i główna Zawartość

Bieżącym katalogiem roboczym zwróconym przez wywołanie <xref:System.IO.Directory.GetCurrentDirectory*> dla usługi systemu Windows jest folder *C: \\ Windows \\ system32* . Folder *system32* nie jest odpowiednią lokalizacją do przechowywania plików usługi (na przykład plików ustawień). Użyj jednego z poniższych metod, aby zachować i uzyskać dostęp do plików ustawień i zasobów usługi.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Ustawianie ścieżki katalogu głównego zawartości do folderu aplikacji

Jest to ta <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> sama ścieżka do `binPath` argumentu podczas tworzenia usługi. Zamiast wywoływania funkcji `GetCurrentDirectory` tworzenia ścieżek do plików ustawień, należy wywołać <xref:System.IO.Directory.SetCurrentDirectory*> ścieżkę do [katalogu głównego zawartości](xref:fundamentals/index#content-root)aplikacji.

W programie `Program.Main` Określ ścieżkę do folderu wykonywalnego usługi i użyj ścieżki, aby określić katalog główny zawartości aplikacji:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Przechowywanie plików usługi w odpowiedniej lokalizacji na dysku

Określ ścieżkę bezwzględną przy <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> użyciu <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do folderu zawierającego pliki.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby rozwiązać problem z aplikacją usługi systemu Windows, zobacz <xref:test/troubleshoot> .

### <a name="common-errors"></a>Typowe błędy

* Starsza lub wstępnie wydana wersja programu PowerShell jest używana.
* Zarejestrowana usługa nie używa **opublikowanych** danych wyjściowych aplikacji z [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenia. Dane wyjściowe polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) nie są obsługiwane w przypadku wdrażania aplikacji. Opublikowane zasoby znajdują się w dowolnym z następujących folderów w zależności od typu wdrożenia:
  * *bin/Release/{Target Framework}/Publish* (FDD)
  * *bin/Release/{Target Framework}/{Runtime identyfikator}/Publish* (SCD)
* Usługa nie jest w stanie uruchomienia.
* Ścieżki do zasobów używanych przez aplikację (na przykład certyfikaty) są nieprawidłowe. Ścieżką podstawową usługi systemu Windows jest *c: \\ Windows \\ system32*.
* Użytkownik nie ma uprawnień do *logowania się jako usługa* .
* Hasło użytkownika wygasło lub zostało nieprawidłowo przesłane podczas wykonywania `New-Service` polecenia programu PowerShell.
* Aplikacja wymaga uwierzytelniania ASP.NET Core, ale nie jest skonfigurowana dla połączeń Secure (HTTPS).
* Port adresu URL żądania jest niepoprawny lub niepoprawnie skonfigurowany w aplikacji.

### <a name="system-and-application-event-logs"></a>Dzienniki zdarzeń systemu i aplikacji

Uzyskaj dostęp do dzienników zdarzeń systemu i aplikacji:

1. Otwórz menu Start, wyszukaj ciąg *Podgląd zdarzeń* i wybierz aplikację **Podgląd zdarzeń** .
1. W **Podgląd zdarzeń** Otwórz węzeł **Dzienniki systemu Windows** .
1. Wybierz pozycję **system** , aby otworzyć dziennik zdarzeń systemu. Wybierz pozycję **aplikacja** , aby otworzyć dziennik zdarzeń aplikacji.
1. Wyszukaj błędy związane z niepowodzeniem aplikacji.

### <a name="run-the-app-at-a-command-prompt"></a>Uruchamianie aplikacji w wierszu polecenia

Wiele błędów uruchamiania nie produkuje użytecznych informacji w dziennikach zdarzeń. Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingu. Aby rejestrować dodatkowe szczegóły aplikacji, Obniż [poziom rejestrowania](xref:fundamentals/logging/index#log-level) lub Uruchom aplikację w [środowisku deweloperskim](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Wyczyść pamięć podręczną pakietów

Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji. W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych. Większość tych problemów można naprawić, wykonując następujące instrukcje:

1. Usuń foldery *bin* i *obj* .
1. Wyczyść pamięć podręczną pakietów, wykonując [wszystkie elementy lokalne usługi NuGet programu dotnet--Wyczyść](/dotnet/core/tools/dotnet-nuget-locals) z poziomu powłoki poleceń.

   Czyszczenie pamięci podręcznych pakietów można także wykonać przy użyciu narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonując polecenie `nuget locals all -clear` . *nuget.exe* nie jest instalacją w pakiecie z systemem operacyjnym Windows dla komputerów stacjonarnych i należy ją pobrać niezależnie od [witryny sieci Web programu NuGet](https://www.nuget.org/downloads).

1. Przywróć i skompiluj projekt.
1. Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.

### <a name="slow-or-hanging-app"></a>Aplikacja wolna lub wysunięta

*Zrzut awaryjny* to migawka pamięci systemu, która może pomóc w ustaleniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.

#### <a name="app-crashes-or-encounters-an-exception"></a>Awaria aplikacji lub napotka wyjątek

Uzyskaj i Analizuj Zrzut z [raportowanie błędów systemu Windows (raportowanie błędów systemu Windows)](/windows/desktop/wer/windows-error-reporting):

1. Utwórz folder do przechowywania plików zrzutu awaryjnego w `c:\dumps` .
1. Uruchom [skrypt programu PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) w programie EnableDumps z nazwą pliku wykonywalnego aplikacji:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Uruchom aplikację w warunkach, które powodują awarię.
1. Po wystąpieniu awarii Uruchom [skrypt programu DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Po awarii aplikacji i zakończeniu zbierania zrzutów aplikacja może zakończyć normalne działanie. Skrypt programu PowerShell konfiguruje raportowanie błędów systemu Windows w celu zebrania do pięciu zrzutów na aplikację.

> [!WARNING]
> Zrzuty awaryjne mogą wymagać dużej ilości miejsca na dysku (do kilku gigabajtów).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie

Gdy aplikacja *zawiesza* się (bez awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [pliki zrzutu w trybie użytkownika: wybór najlepszego narzędzia](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) do wygenerowania zrzutu.

#### <a name="analyze-the-dump"></a>Analizowanie zrzutu

Zrzut można analizować przy użyciu kilku metod. Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu User-Mode](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (w tym Konfiguracja protokołu HTTPS i obsługa SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Aplikacja ASP.NET Core może być hostowana w systemie Windows jako [Usługa systemu Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) bez korzystania z usług IIS. Gdy usługa jest hostowana w systemie Windows, aplikacja jest uruchamiana automatycznie po ponownym uruchomieniu serwera.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

* [ASP.NET Core zestaw SDK 2,1 lub nowszy](https://dotnet.microsoft.com/download)
* [Program PowerShell 6,2 lub nowszy](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Konfiguracja aplikacji

Aplikacja wymaga odwołania do pakietów dla elementu [Microsoft. AspNetCore. hosting. WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) i [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Aby przetestować i debugować w przypadku uruchamiania poza usługą, należy dodać kod, aby określić, czy aplikacja działa jako usługa, czy Aplikacja konsolowa. Sprawdź, czy debuger jest dołączony lub czy `--console` jest obecny przełącznik. Jeśli którykolwiek z warunków jest spełniony (aplikacja nie jest uruchomiona jako usługa), wywołaj metodę <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> . Jeśli warunki są fałszywe (aplikacja jest uruchamiana jako usługa):

* Wywołaj <xref:System.IO.Directory.SetCurrentDirectory*> i użyj ścieżki do opublikowanej lokalizacji aplikacji. Nie wywołuj <xref:System.IO.Directory.GetCurrentDirectory*> , aby uzyskać ścieżkę, ponieważ aplikacja usługi systemu Windows zwraca folder *C: \\ Windows \\ system32* , gdy <xref:System.IO.Directory.GetCurrentDirectory*> jest wywoływana. Aby uzyskać więcej informacji, zapoznaj się z sekcją [Current Directory i root Content](#current-directory-and-content-root) . Ten krok jest wykonywany przed skonfigurowaniem aplikacji w programie `CreateWebHostBuilder` .
* Wywołaj <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> , aby uruchomić aplikację jako usługę.

Ponieważ [dostawca konfiguracji wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) wymaga par nazwa-wartość dla argumentów wiersza polecenia, `--console` przełącznik jest usuwany z argumentów przed <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> odebraniem argumentów.

Aby zapisać w dzienniku zdarzeń systemu Windows, Dodaj dostawcę EventLog do programu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> . Ustaw poziom rejestrowania z `Logging:LogLevel:Default` kluczem w *appsettings.Production.js* pliku.

W poniższym przykładzie z przykładowej aplikacji `RunAsCustomService` jest wywoływana zamiast <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> w celu obsługi zdarzeń okresu istnienia w aplikacji. Aby uzyskać więcej informacji, zobacz sekcję [Obsługa zdarzeń dotyczących uruchamiania i zatrzymywania](#handle-starting-and-stopping-events) .

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Typ wdrożenia

Aby uzyskać informacje i porady dotyczące scenariuszy wdrażania, zobacz [wdrażanie aplikacji .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>SDK

W przypadku usługi opartej na aplikacji sieci Web korzystającej ze Razor stron lub platform MVC należy określić zestaw SDK sieci Web w pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Jeśli usługa wykonuje tylko zadania w tle (na przykład [usługi hostowane](xref:fundamentals/host/hosted-services)), określ zestaw SDK procesu roboczego w pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Wdrożenie zależne od platformy (FDD)

Wdrożenie zależne od platformy (FDD) zależy od obecności udostępnionej systemowej wersji platformy .NET Core w systemie docelowym. Po przyjęciu scenariusza FDD zgodnie ze wskazówkami zawartymi w tym artykule zestaw SDK tworzy plik wykonywalny (*. exe*), nazywany *plik wykonywalny zależny od platformy*.

[Identyfikator środowiska uruchomieniowego systemu Windows (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) zawiera platformę docelową. W poniższym przykładzie identyfikator RID jest ustawiony na `win7-x64` . Właściwość `<SelfContained>` ma ustawioną wartość `false`. Te właściwości instruują zestaw SDK, aby wygenerował plik wykonywalny (*exe*) dla systemu Windows i aplikację, która zależy od współużytkowanej platformy .NET Core.

Właściwość `<UseAppHost>` ma ustawioną wartość `true`. Ta właściwość zapewnia usługę ze ścieżką aktywacji (plik wykonywalny, *exe*) dla FDD.

Plik *web.config* , który jest zwykle tworzony podczas publikowania aplikacji ASP.NET Core, nie jest konieczny dla aplikacji usług systemu Windows. Aby wyłączyć tworzenie pliku *web.config* , należy dodać `<IsTransformWebConfigDisabled>` Właściwość ustawioną na `true` .

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Wdrażanie samodzielne (SCD)

Wdrożenie samodzielne (SCD) nie polega na obecności struktury udostępnionej w systemie hosta. Środowisko uruchomieniowe i zależności aplikacji są wdrażane przy użyciu aplikacji.

[Identyfikator środowiska uruchomieniowego systemu Windows (RID)](/dotnet/core/rid-catalog) znajduje się w, `<PropertyGroup>` który zawiera platformę docelową:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Aby opublikować dla wielu identyfikatorów RID:

* Podaj identyfikatory RID na liście rozdzielanej średnikami.
* Użyj nazwy właściwości [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).

Aby uzyskać więcej informacji, zobacz [katalog .NET Core RID Catalog](/dotnet/core/rid-catalog).

`<SelfContained>`Właściwość jest ustawiona na `true` :

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Konto użytkownika usługi

Aby utworzyć konto użytkownika dla usługi, należy użyć polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) w powłoce poleceń administracyjnych programu PowerShell 6.

W systemie Windows 10 października 2018 Update (wersja 1809/Kompilacja 10.0.17763) lub nowsza:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

W systemie operacyjnym Windows w wersji starszej niż aktualizacja 2018 systemu Windows 10 października (wersja 1809/Kompilacja 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Podaj [silne hasło](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) po wyświetleniu monitu.

Jeśli `-AccountExpires` parametr nie zostanie dostarczony do polecenia cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) z wygaśnięciem <xref:System.DateTime> , konto nie wygaśnie.

Aby uzyskać więcej informacji, zobacz [Microsoft. PowerShell. LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) i [konta użytkowników usług](/windows/desktop/services/service-user-accounts).

Alternatywna metoda zarządzania użytkownikami podczas korzystania z Active Directory polega na użyciu zarządzanych kont usług. Aby uzyskać więcej informacji, zobacz [Omówienie kont usług zarządzanych przez grupę](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Logowanie jako prawa usługi

Aby nawiązać *Logowanie jako prawa usługi* dla konta użytkownika usługi:

1. Otwórz Edytor lokalnych zasad zabezpieczeń, uruchamiając program *secpol. msc*.
1. Rozwiń węzeł **Zasady lokalne** , a następnie wybierz pozycję **Przypisywanie praw użytkownika**.
1. Otwórz okno **Logowanie jako usługa** .
1. Wybierz pozycję **Dodaj użytkownika lub grupę**.
1. Podaj nazwę obiektu (konto użytkownika) przy użyciu jednej z następujących metod:
   1. Wpisz konto użytkownika ( `{DOMAIN OR COMPUTER NAME\USER}` ) w polu Nazwa obiektu, a następnie wybierz **przycisk OK** , aby dodać użytkownika do zasad.
   1. Wybierz pozycję **Zaawansowane**. Wybierz pozycję **Znajdź teraz**. Wybierz z listy konto użytkownika. Wybierz przycisk **OK**. Ponownie wybierz **przycisk OK** , aby dodać użytkownika do zasad.
1. Wybierz **przycisk OK** lub **Zastosuj** , aby zaakceptować zmiany.

## <a name="create-and-manage-the-windows-service"></a>Tworzenie usługi systemu Windows i zarządzanie nią

### <a name="create-a-service"></a>Tworzenie usługi

Zarejestrowanie usługi za pomocą poleceń programu PowerShell. Z poziomu powłoki poleceń administracyjnych programu PowerShell 6 wykonaj następujące polecenia:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`: Ścieżka do folderu aplikacji na hoście (na przykład `d:\myservice` ). Nie dołączaj pliku wykonywalnego aplikacji do ścieżki. Końcowy ukośnik nie jest wymagany.
* `{DOMAIN OR COMPUTER NAME\USER}`: Konto użytkownika usługi (na przykład `Contoso\ServiceUser` ).
* `{SERVICE NAME}`: Nazwa usługi (na przykład `MyService` ).
* `{EXE FILE PATH}`: Ścieżka pliku wykonywalnego aplikacji (na przykład `d:\myservice\myservice.exe` ). Uwzględnij nazwę pliku wykonywalnego z rozszerzeniem.
* `{DESCRIPTION}`: Opis usługi (na przykład `My sample service` ).
* `{DISPLAY NAME}`: Nazwa wyświetlana usługi (na przykład `My Service` ).

### <a name="start-a-service"></a>Uruchamianie usługi

Uruchom usługę za pomocą następującego polecenia programu PowerShell 6:

```powershell
Start-Service -Name {SERVICE NAME}
```

Uruchomienie usługi może potrwać kilka sekund.

### <a name="determine-a-services-status"></a>Określanie stanu usługi

Aby sprawdzić stan usługi, użyj następującego polecenia programu PowerShell 6:

```powershell
Get-Service -Name {SERVICE NAME}
```

Stan jest raportowany jako jedna z następujących wartości:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Zatrzymywanie usługi

Zatrzymaj usługę za pomocą następującego polecenia programu PowerShell 6:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Usuwanie usługi

Po krótkim opóźnieniu na zatrzymanie usługi Usuń usługę za pomocą następującego polecenia programu PowerShell 6:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Obsługa zdarzeń uruchamiania i zatrzymywania

Aby obsłużyć <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> zdarzenia, i <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> :

1. Utwórz klasę, która dziedziczy z <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> `OnStarting` `OnStarted` metody,, i `OnStopping` :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Utwórz metodę rozszerzenia dla <xref:Microsoft.AspNetCore.Hosting.IWebHost> , która przekazuje `CustomWebHostService` do <xref:System.ServiceProcess.ServiceBase.Run*> :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. W `Program.Main` , wywołaj `RunAsCustomService` metodę rozszerzenia zamiast <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :

   ```csharp
   host.RunAsCustomService();
   ```

   Aby zobaczyć lokalizację programu <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> w programie `Program.Main` , zapoznaj się z przykładem kodu pokazanym w sekcji [typ wdrożenia](#deployment-type) .

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

Usługi, które współdziałają z żądaniami z Internetu lub sieci firmowej i znajdują się za serwerem proxy lub modułem równoważenia obciążenia, mogą wymagać dodatkowej konfiguracji. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Konfigurowanie punktów końcowych

Domyślnie ASP.NET Core wiąże się z `http://localhost:5000` . Skonfiguruj adres URL i port przez ustawienie `ASPNETCORE_URLS` zmiennej środowiskowej.

Aby uzyskać dodatkowe podejścia do konfiguracji adresów URL i portów, zobacz artykuł dotyczący odpowiedniego serwera:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Powyższe wskazówki obejmują obsługę punktów końcowych HTTPS. Na przykład skonfiguruj aplikację do obsługi protokołu HTTPS, gdy uwierzytelnianie jest używane z usługą systemu Windows.

> [!NOTE]
> Korzystanie z ASP.NET Core certyfikatu deweloperskiego HTTPS w celu zabezpieczenia punktu końcowego usługi nie jest obsługiwane.

## <a name="current-directory-and-content-root"></a>Bieżący katalog i główna Zawartość

Bieżącym katalogiem roboczym zwróconym przez wywołanie <xref:System.IO.Directory.GetCurrentDirectory*> dla usługi systemu Windows jest folder *C: \\ Windows \\ system32* . Folder *system32* nie jest odpowiednią lokalizacją do przechowywania plików usługi (na przykład plików ustawień). Użyj jednego z poniższych metod, aby zachować i uzyskać dostęp do plików ustawień i zasobów usługi.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Ustawianie ścieżki katalogu głównego zawartości do folderu aplikacji

Jest to ta <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> sama ścieżka do `binPath` argumentu podczas tworzenia usługi. Zamiast wywoływania funkcji `GetCurrentDirectory` tworzenia ścieżek do plików ustawień, należy wywołać <xref:System.IO.Directory.SetCurrentDirectory*> ścieżkę do [katalogu głównego zawartości](xref:fundamentals/index#content-root)aplikacji.

W programie `Program.Main` Określ ścieżkę do folderu wykonywalnego usługi i użyj ścieżki, aby określić katalog główny zawartości aplikacji:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Przechowywanie plików usługi w odpowiedniej lokalizacji na dysku

Określ ścieżkę bezwzględną przy <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> użyciu <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> do folderu zawierającego pliki.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby rozwiązać problem z aplikacją usługi systemu Windows, zobacz <xref:test/troubleshoot> .

### <a name="common-errors"></a>Typowe błędy

* Starsza lub wstępnie wydana wersja programu PowerShell jest używana.
* Zarejestrowana usługa nie używa **opublikowanych** danych wyjściowych aplikacji z [dotnet Publish](/dotnet/core/tools/dotnet-publish) polecenia. Dane wyjściowe polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) nie są obsługiwane w przypadku wdrażania aplikacji. Opublikowane zasoby znajdują się w dowolnym z następujących folderów w zależności od typu wdrożenia:
  * *bin/Release/{Target Framework}/Publish* (FDD)
  * *bin/Release/{Target Framework}/{Runtime identyfikator}/Publish* (SCD)
* Usługa nie jest w stanie uruchomienia.
* Ścieżki do zasobów używanych przez aplikację (na przykład certyfikaty) są nieprawidłowe. Ścieżką podstawową usługi systemu Windows jest *c: \\ Windows \\ system32*.
* Użytkownik nie ma uprawnień do *logowania się jako usługa* .
* Hasło użytkownika wygasło lub zostało nieprawidłowo przesłane podczas wykonywania `New-Service` polecenia programu PowerShell.
* Aplikacja wymaga uwierzytelniania ASP.NET Core, ale nie jest skonfigurowana dla połączeń Secure (HTTPS).
* Port adresu URL żądania jest niepoprawny lub niepoprawnie skonfigurowany w aplikacji.

### <a name="system-and-application-event-logs"></a>Dzienniki zdarzeń systemu i aplikacji

Uzyskaj dostęp do dzienników zdarzeń systemu i aplikacji:

1. Otwórz menu Start, wyszukaj ciąg *Podgląd zdarzeń* i wybierz aplikację **Podgląd zdarzeń** .
1. W **Podgląd zdarzeń** Otwórz węzeł **Dzienniki systemu Windows** .
1. Wybierz pozycję **system** , aby otworzyć dziennik zdarzeń systemu. Wybierz pozycję **aplikacja** , aby otworzyć dziennik zdarzeń aplikacji.
1. Wyszukaj błędy związane z niepowodzeniem aplikacji.

### <a name="run-the-app-at-a-command-prompt"></a>Uruchamianie aplikacji w wierszu polecenia

Wiele błędów uruchamiania nie produkuje użytecznych informacji w dziennikach zdarzeń. Przyczynę niektórych błędów można znaleźć, uruchamiając aplikację w wierszu polecenia w systemie hostingu. Aby rejestrować dodatkowe szczegóły aplikacji, Obniż [poziom rejestrowania](xref:fundamentals/logging/index#log-level) lub Uruchom aplikację w [środowisku deweloperskim](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Wyczyść pamięć podręczną pakietów

Działająca aplikacja może zakończyć się niepowodzeniem natychmiast po uaktualnieniu zestaw .NET Core SDK na komputerze deweloperskim lub zmianie wersji pakietu w ramach aplikacji. W niektórych przypadkach niespójne pakiety mogą przerwać aplikację podczas przeprowadzania uaktualnień głównych. Większość tych problemów można naprawić, wykonując następujące instrukcje:

1. Usuń foldery *bin* i *obj* .
1. Wyczyść pamięć podręczną pakietów, wykonując [wszystkie elementy lokalne usługi NuGet programu dotnet--Wyczyść](/dotnet/core/tools/dotnet-nuget-locals) z poziomu powłoki poleceń.

   Czyszczenie pamięci podręcznych pakietów można także wykonać przy użyciu narzędzia [nuget.exe](https://www.nuget.org/downloads) i wykonując polecenie `nuget locals all -clear` . *nuget.exe* nie jest instalacją w pakiecie z systemem operacyjnym Windows dla komputerów stacjonarnych i należy ją pobrać niezależnie od [witryny sieci Web programu NuGet](https://www.nuget.org/downloads).

1. Przywróć i skompiluj projekt.
1. Usuń wszystkie pliki z folderu wdrożenia na serwerze przed ponownym wdrożeniem aplikacji.

### <a name="slow-or-hanging-app"></a>Aplikacja wolna lub wysunięta

*Zrzut awaryjny* to migawka pamięci systemu, która może pomóc w ustaleniu przyczyny awarii aplikacji, awarii uruchamiania lub powolnej aplikacji.

#### <a name="app-crashes-or-encounters-an-exception"></a>Awaria aplikacji lub napotka wyjątek

Uzyskaj i Analizuj Zrzut z [raportowanie błędów systemu Windows (raportowanie błędów systemu Windows)](/windows/desktop/wer/windows-error-reporting):

1. Utwórz folder do przechowywania plików zrzutu awaryjnego w `c:\dumps` .
1. Uruchom [skrypt programu PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) w programie EnableDumps z nazwą pliku wykonywalnego aplikacji:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Uruchom aplikację w warunkach, które powodują awarię.
1. Po wystąpieniu awarii Uruchom [skrypt programu DisableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Po awarii aplikacji i zakończeniu zbierania zrzutów aplikacja może zakończyć normalne działanie. Skrypt programu PowerShell konfiguruje raportowanie błędów systemu Windows w celu zebrania do pięciu zrzutów na aplikację.

> [!WARNING]
> Zrzuty awaryjne mogą wymagać dużej ilości miejsca na dysku (do kilku gigabajtów).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Aplikacja zawiesza się, kończy się niepowodzeniem podczas uruchamiania lub działa normalnie

Gdy aplikacja *zawiesza* się (bez awarii), kończy się niepowodzeniem podczas uruchamiania lub działa normalnie, zobacz [pliki zrzutu w trybie użytkownika: wybór najlepszego narzędzia](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) do wygenerowania zrzutu.

#### <a name="analyze-the-dump"></a>Analizowanie zrzutu

Zrzut można analizować przy użyciu kilku metod. Aby uzyskać więcej informacji, zobacz [Analizowanie pliku zrzutu User-Mode](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Konfiguracja punktu końcowego Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (w tym Konfiguracja protokołu HTTPS i obsługa SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
