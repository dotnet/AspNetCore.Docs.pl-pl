---
title: Host ASP.NET Core w systemie Windows z iis
author: rick-anderson
description: Dowiedz się, jak hostować aplikacje ASP.NET Core w internetowych usługach informacyjnych systemu Windows Server (IIS).
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/iis/index
ms.openlocfilehash: 819c53f945c1e5bb2cedcef8fc39d4c8761e4549
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977031"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>Host ASP.NET Core w systemie Windows z iis

::: moniker range=">= aspnetcore-3.0"

Aby zapoznać się z samouczkiem dotyczących publikowania aplikacji core <xref:tutorials/publish-to-iis>ASP.NET na serwerze usług IIS, zobacz .

[Zainstaluj pakiet hostingowy .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Obsługiwane są następujące systemy operacyjne:

* Windows 7 lub nowsze
* Windows Server 2012 R2 lub nowszy

[Serwer HTTP.sys](xref:fundamentals/servers/httpsys) (dawniej nazywany WebListener) nie działa w odwrotnej konfiguracji serwera proxy z usługami IIS. Użyj [serwera Kestrel](xref:fundamentals/servers/kestrel).

Aby uzyskać informacje na <xref:host-and-deploy/azure-apps/index>temat hostingu na platformie Azure, zobacz .

Aby uzyskać wskazówki <xref:test/troubleshoot>dotyczące rozwiązywania problemów, zobacz .

## <a name="supported-platforms"></a>Obsługiwane platformy

Obsługiwane są aplikacje opublikowane dla wdrożenia 32-bitowego (x86) lub 64-bitowego (x64). Wdrażanie 32-bitowej aplikacji z 32-bitowym (x86) .NET Core SDK, chyba że aplikacja:

* Wymaga większej przestrzeni adresowej pamięci wirtualnej dostępnej dla aplikacji 64-bitowej.
* Wymaga większego rozmiaru stosu usługi IIS.
* Ma 64-bitowe natywne zależności.

Użyj 64-bitowego (x64) .NET Core SDK, aby opublikować aplikację 64-bitową. 64-bitowy środowiska uruchomieniowego musi być obecny w systemie hosta.

## <a name="hosting-models"></a>Modele hostingu

### <a name="in-process-hosting-model"></a>Model hostingu w trakcie

Korzystając z hostingu w trakcie, aplikacja ASP.NET Core działa w tym samym procesie co proces roboczy IIS. Hosting w trakcie zapewnia lepszą wydajność w zakresie hostingu poza procesem, ponieważ żądania nie są przerzaszane przez kartę sprzężenia zwrotnego, interfejs sieciowy, który zwraca wychodzący ruch sieciowy z powrotem na ten sam komputer. Usługi IIS obsługują zarządzanie procesami za pomocą [usługi aktywacji procesów systemu Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Moduł [podstawowy ASP.NET:](xref:host-and-deploy/aspnet-core-module)

* Wykonuje inicjowanie aplikacji.
  * Ładuje [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Połączenia `Program.Main`.
* Obsługuje okres istnienia żądania macierzystego IIS.

Model hostingu w trakcie nie jest obsługiwany dla aplikacji ASP.NET Core, które są przeznaczone dla platformy .NET Framework.

Na poniższym diagramie przedstawiono relację między iis, ASP.NET Core Module i aplikacji hostowane w procesie:

![ASP.NET moduł podstawowy w scenariuszu hostingu w trakcie](index/_static/ancm-inprocess.png)

Żądanie jest nadchodają z sieci Web do sterownika HTTP.sys w trybie jądra. Sterownik kieruje żądanie macierzyste do usługi IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS). Moduł ASP.NET Core odbiera żądanie macierzyste i przekazuje je do`IISHttpServer`serwera HTTP usług IIS ( ). Serwer HTTP usług IIS to implementacja serwera w trakcie usług IIS, która konwertuje żądanie z macierzystego na zarządzane.

Po iis serwer HTTP przetwarza żądanie, żądanie jest wypychane do potoku oprogramowania pośredniczącego ASP.NET Core. Potok oprogramowania pośredniczącego obsługuje żądanie i `HttpContext` przekazuje go jako wystąpienie do logiki aplikacji. Odpowiedź aplikacji jest przekazywana z powrotem do usług IIS za pośrednictwem serwera HTTP usług IIS. Usługi IIS wysyłają odpowiedź do klienta, który zainicjował żądanie.

Hosting w trakcie jest opt-in dla istniejących aplikacji, ale [dotnet nowe](/dotnet/core/tools/dotnet-new) szablony domyślnie w trakcie modelu hostingu dla wszystkich scenariuszy usług IIS i IIS Express.

`CreateDefaultBuilder`dodaje <xref:Microsoft.AspNetCore.Hosting.Server.IServer> wystąpienie, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> wywołując metodę uruchamiania [CoreCLR](/dotnet/standard/glossary#coreclr) i hostowania aplikacji wewnątrz procesu roboczego IIS (*w3wp.exe* lub *iisexpress.exe*). Testy wydajności wskazują, że hostowanie aplikacji .NET Core w procesie zapewnia znacznie wyższą przepływność żądania w porównaniu do hostowania aplikacji poza procesem i proxy do serwera [Kestrel.](xref:fundamentals/servers/kestrel)

> [!NOTE]
> Aplikacje opublikowane jako plik wykonywalny pojedynczego pliku nie mogą być ładowane przez model hostingu w trakcie.

### <a name="out-of-process-hosting-model"></a>Model hostingu poza procesem

Ponieważ aplikacje core ASP.NET są uruchamiane w procesie odrębnym od procesu roboczego usług IIS, moduł ASP.NET Core obsługuje zarządzanie procesami. Moduł uruchamia proces aplikacji ASP.NET Core po nadejściu pierwszego żądania i uruchamia ponownie aplikację, jeśli zostanie zamknięta lub ulegnie awarii. Jest to zasadniczo to samo zachowanie, co w aplikacjach uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (WAS).](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)

Na poniższym diagramie przedstawiono relację między iis, ASP.NET Core Module i aplikacji hostowane poza procesem:

![ASP.NET modułu podstawowego w scenariuszu hostingu poza procesem](index/_static/ancm-outofprocess.png)

Żądania przychodzą z sieci Web do sterownika HTTP.sys w trybie jądra. Sterownik kieruje żądania do iIS w skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS). Moduł przekazuje żądania do Kestrel na losowym porcie dla aplikacji, który nie jest portem 80 lub 443.

Moduł określa port za pośrednictwem zmiennej środowiskowej <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> podczas uruchamiania, a `http://localhost:{PORT}`rozszerzenie konfiguruje serwer do nasłuchiwać na . Dodatkowe kontrole są wykonywane, a żądania, które nie pochodzą z modułu są odrzucane. Moduł nie obsługuje przekazywania protokołu HTTPS, więc żądania są przekazywane za pośrednictwem protokołu HTTP, nawet jeśli są odbierane przez usługi IIS za pośrednictwem protokołu HTTPS.

Po Kestrel odbiera żądanie z modułu, żądanie jest wypychane do potoku oprogramowania pośredniczącego ASP.NET Core. Potok oprogramowania pośredniczącego obsługuje żądanie i `HttpContext` przekazuje go jako wystąpienie do logiki aplikacji. Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i bazę ścieżek w celu przekazania żądania do Kestrel. Odpowiedź aplikacji jest przekazywana z powrotem do iIS, co wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.

Aby uzyskać ASP.NET wskazówki dotyczące <xref:host-and-deploy/aspnet-core-module>konfiguracji modułu rdzenia, zobacz .

Aby uzyskać więcej informacji na temat hostingu, zobacz [Host w ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Konfiguracja aplikacji

### <a name="enable-the-iisintegration-components"></a>Włączanie składników integracji iis

Podczas tworzenia hosta w `CreateHostBuilder` *(Program.cs*), wywołanie, <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> aby włączyć integrację usługi IIS:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

Aby uzyskać `CreateDefaultBuilder`więcej <xref:fundamentals/host/generic-host#default-builder-settings>informacji na temat , zobacz .

### <a name="iis-options"></a>Opcje iis

**Model hostingu w trakcie**

Aby skonfigurować opcje serwera usług IIS, <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>należy dołączyć konfigurację usługi w <xref:Microsoft.AspNetCore.Builder.IISServerOptions> programie . Poniższy przykład wyłącza AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Opcja                         | Domyślne | Ustawienie |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Jeśli `true`program IIS `HttpContext.User` Server ustawi uwierzytelnione [przez uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth). Jeśli `false`serwer zapewnia tożsamość `HttpContext.User` i odpowiada na wyzwania tylko wtedy, `AuthenticationScheme`gdy jest to wyraźnie wymagane przez plik . Uwierzytelnianie systemu Windows musi być `AutomaticAuthentication` włączone w iis, aby działały. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Ustawia wyświetlaną nazwę wyświetlaną użytkownikom na stronach logowania. |
| `AllowSynchronousIO`           | `false` | Czy synchroniczne we/wy jest `HttpContext.Request` dozwolone `HttpContext.Response`dla i . |
| `MaxRequestBodySize`           | `30000000`  | Pobiera lub ustawia maksymalny rozmiar `HttpRequest`treści żądania dla . Należy zauważyć, że same `maxAllowedContentLength` programy IIS mają `MaxRequestBodySize` limit, `IISServerOptions`który będzie przetwarzany przed zestawem w pliku . Zmiana `MaxRequestBodySize` nie wpłynie na `maxAllowedContentLength`. Aby `maxAllowedContentLength`zwiększyć, dodaj wpis w *pliku web.config,* aby ustawić `maxAllowedContentLength` wyższą wartość. Aby uzyskać więcej informacji, zobacz [Konfiguracja](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

**Model hostingu poza procesem**

Aby skonfigurować opcje usług IIS, <xref:Microsoft.AspNetCore.Builder.IISOptions> <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>należy dołączyć konfigurację usługi w programie . Poniższy przykład zapobiega zapełnianiu `HttpContext.Connection.ClientCertificate`aplikacji:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Opcja                         | Domyślne | Ustawienie |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Jeśli `true`oprogramowanie [pośredniczące integracji iIS](#enable-the-iisintegration-components) ustawia `HttpContext.User` uwierzytelnione przez [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth). Jeśli `false`oprogramowanie pośredniczące zapewnia `HttpContext.User` tylko tożsamość i odpowiada na wyzwania, gdy jest to wyraźnie wymagane przez `AuthenticationScheme`. Uwierzytelnianie systemu Windows musi być `AutomaticAuthentication` włączone w iis, aby działały. Aby uzyskać więcej informacji, zobacz temat [Uwierzytelniania systemu Windows.](xref:security/authentication/windowsauth) |
| `AuthenticationDisplayName`    | `null`  | Ustawia wyświetlaną nazwę wyświetlaną użytkownikom na stronach logowania. |
| `ForwardClientCertificate`     | `true`  | Jeśli `true` nagłówek `MS-ASPNETCORE-CLIENTCERT` żądania jest obecny, jest wypełniany. `HttpContext.Connection.ClientCertificate` |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

[Oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components), które konfiguruje oprogramowanie pośredniczące nagłówków przesyłanych dalej, oraz ASP.NET Core Module są skonfigurowane do przesyłania dalej schematu (HTTP/HTTPS) i zdalnego adresu IP, z którego pochodzi żądanie. Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych za dodatkowymi serwerami proxy i modułami równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>plik web.config

Plik *web.config* konfiguruje [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module). Tworzenie, przekształcanie i publikowanie pliku *web.config* jest obsługiwane przez obiekt`_TransformWebConfig`docelowy MSBuild ( ) po opublikowaniu projektu. Ten cel jest obecny w celach`Microsoft.NET.Sdk.Web`SDK sieci Web ( ). Zestaw SDK znajduje się w górnej części pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Jeśli pliku *web.config* nie ma w projekcie, plik jest tworzony z poprawnym *processPath* i *argumentami* w celu skonfigurowania ASP.NET modułu rdzenia i przeniesiony do [opublikowanego wyjścia](xref:host-and-deploy/directory-structure).

Jeśli plik *web.config* jest obecny w projekcie, plik jest przekształcany za pomocą poprawnego *processPath* i *argumentów,* aby skonfigurować ASP.NET Core Module i przeniesiony do opublikowanych danych wyjściowych. Transformacja nie modyfikuje ustawień konfiguracji usług IIS w pliku.

Plik *web.config* może zapewniać dodatkowe ustawienia konfiguracji usług IIS, które sterują aktywnymi modułami usług IIS. Aby uzyskać informacje na temat modułów usług IIS, które mogą przetwarzać żądania za pomocą aplikacji ASP.NET Core, zobacz temat [modułów usług IIS.](xref:host-and-deploy/iis/modules)

Aby zapobiec przekształcaniu pliku *web.config przez web.config,* należy użyć właściwości ** \<IsTransformWebConfigDisabled>** w pliku projektu:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Podczas wyłączania zestawu Web SDK z przekształcania pliku, *processPath* i *argumenty* powinny być ręcznie ustawione przez dewelopera. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>lokalizacja pliku web.config

Aby poprawnie skonfigurować [moduł ASP.NET Core,](xref:host-and-deploy/aspnet-core-module) plik *web.config* musi znajdować się przy ścieżce [głównej zawartości](xref:fundamentals/index#content-root) (zazwyczaj ścieżki podstawowej aplikacji) wdrożonej aplikacji. Jest to ta sama lokalizacja, co ścieżka fizyczna witryny sieci Web udostępniana usługom IIS. Plik *web.config* jest wymagany w katalogu głównym aplikacji, aby umożliwić publikowanie wielu aplikacji przy użyciu funkcji Web Deploy.

Poufne pliki istnieją na ścieżce fizycznej aplikacji, takie jak * \<zestaw>.runtimeconfig.json*, * \<zestaw>.xml* (komentarze dokumentacji XML) i * \<zestaw>.deps.json*. Gdy plik *web.config* jest obecny, a witryna uruchamia się normalnie, usługi IIS nie obsługują tych poufnych plików, jeśli są wymagane. Jeśli brakuje pliku *web.config,* niepoprawnie nazwany lub nie można skonfigurować witryny do normalnego uruchamiania, usługi IIS mogą obsługiwać poufne pliki publicznie.

**Plik *web.config* musi być obecny we wdrożeniu przez cały czas, poprawnie nazwany i może skonfigurować witrynę do normalnego uruchamiania. Nigdy nie usuwaj pliku *web.config* z wdrożenia produkcyjnego.**

### <a name="transform-webconfig"></a>Przekształcanie pliku web.config

Jeśli chcesz przekształcić *web.config* przy publikowaniu (na przykład ustawić zmienne środowiskowe na <xref:host-and-deploy/iis/transform-webconfig>podstawie konfiguracji, profilu lub środowiska), zobacz .

## <a name="iis-configuration"></a>Konfiguracja usług IIS

**Systemy operacyjne Windows Server**

Włącz rolę serwera **sieci Web (IIS)** i ustal usługi ról.

1. Użyj Kreatora **Dodawania ról i funkcji** z menu **Zarządzanie** lub łącza w **Menedżerze serwera**. W kroku **Role serwera** zaznacz pole wyboru Dla serwera sieci **Web (IIS)**.

   ![Rola usług IIS serwera sieci Web jest zaznaczona w kroku Wybierz role serwera.](index/_static/server-roles-ws2016.png)

1. Po kroku **Funkcje** krok **usług roli** ładuje się dla serwera sieci Web (IIS). Wybierz żądane usługi ról usług IIS lub zaakceptuj domyślne usługi ról.

   ![Domyślne usługi ról są zaznaczone w kroku Wybierz usługi roli.](index/_static/role-services-ws2016.png)

   **Uwierzytelnianie systemu Windows (opcjonalnie)**  
   Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły:**Zabezpieczenia** **serwera** > sieci Web . Wybierz funkcję **uwierzytelniania systemu Windows.** Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie \<systemu Windows>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

   **WebSockets (opcjonalnie)**  
   WebSockets jest obsługiwany z ASP.NET Core 1.1 lub nowszym. Aby włączyć websockets, rozwiń następujące węzły:**Tworzenie aplikacji** **serwera** > sieci Web . Wybierz funkcję **Protokołu WebSocket.** Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).

1. Przejdź przez krok **potwierdzenia,** aby zainstalować rolę i usługi serwera sieci web. Ponowne uruchomienie serwera/usług IIS nie jest wymagane po zainstalowaniu roli **serwera sieci Web (IIS).**

**Systemy operacyjne Windows dla komputerów stacjonarnych**

Włącz **konsolę zarządzania usługami IIS** i **usługi sieci World Wide Web**.

1. Przejdź do pozycji **Programy i funkcje** > **Panelu** > **sterowania** > **Włączanie lub wyłączanie funkcji systemu Windows** (po lewej stronie ekranu).

1. Otwórz węzeł **Internetowe Usługi informacyjne.** Otwórz węzeł **Narzędzia do zarządzania siecią Web.**

1. Zaznacz pole wyboru **Konsola zarządzania usługami IIS**.

1. Zaznacz pole wyboru **Usługi sieci World Wide Web**.

1. Zaakceptuj domyślne funkcje **usług sieci World Wide Web** lub dostosuj funkcje usług IIS.

   **Uwierzytelnianie systemu Windows (opcjonalnie)**  
   Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **Zabezpieczenia usług** > sieci World Wide Web **.** Wybierz funkcję **uwierzytelniania systemu Windows.** Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie \<systemu Windows>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

   **WebSockets (opcjonalnie)**  
   WebSockets jest obsługiwany z ASP.NET Core 1.1 lub nowszym. Aby włączyć websockets, rozwiń następujące węzły:**Funkcje tworzenia aplikacji** **world wide web services** > . Wybierz funkcję **Protokołu WebSocket.** Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).

1. Jeśli instalacja usług IIS wymaga ponownego uruchomienia, uruchom ponownie system.

![W programie Funkcje systemu Windows wybrano konsolę zarządzania usługami IIS i usługi sieci Web World Wide Web.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Zainstaluj pakiet hostingowy .NET Core

Zainstaluj *pakiet hostingowy .NET Core* w systemie hostingowym. Pakiet instaluje środowisko uruchomieniowe .NET Core, bibliotekę .NET Core library i [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module). Moduł umożliwia ASP.NET aplikacje Core działać za usługami IIS.

> [!IMPORTANT]
> Jeśli pakiet hostingowy jest zainstalowany przed usługami IIS, instalacja pakietu musi zostać naprawiona. Uruchom instalator pakietu hostingowego ponownie po zainstalowaniu usługi IIS.
>
> Jeśli pakiet hostingowy jest zainstalowany po zainstalowaniu 64-bitowej (x64) wersji .NET Core, może wydawać się brak zestawów SDK[(nie wykryto żadnych zestawów SDK .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Aby rozwiązać ten <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>problem, zobacz .

### <a name="direct-download-current-version"></a>Pobieranie bezpośrednie (aktualna wersja)

Pobierz instalatora za pomocą następującego linku:

[Aktualny instalator pakietu hostingu .NET Core (pobieranie bezpośrednie)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Starsze wersje instalatora

Aby uzyskać wcześniejszą wersję instalatora:

1. Przejdź do strony [Pobierz .NET Core.](https://dotnet.microsoft.com/download/dotnet-core)
1. Kliknij żądaną wersję .NET Core.
1. W kolumnie **Uruchom aplikacje — środowisko uruchomieniowe** znajdź wiersz żądanej wersji środowiska uruchomieniowego .NET Core.
1. Pobierz instalatora za pomocą łącza **Runtime & Hosting Bundle.**

> [!WARNING]
> Niektóre instalatory zawierają wersje wersji, które osiągnęły swój koniec życia (EOL) i nie są już obsługiwane przez firmę Microsoft. Aby uzyskać więcej informacji, zobacz [zasady pomocy technicznej](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Zainstaluj pakiet hostingowy

1. Uruchom instalator na serwerze. Następujące parametry są dostępne podczas uruchamiania instalatora z powłoki poleceń administratora:

   * `OPT_NO_ANCM=1`&ndash; Pomiń instalację modułu ASP.NET Core.
   * `OPT_NO_RUNTIME=1`&ndash; Pomiń instalowanie środowiska uruchomieniowego .NET Core. Używane, gdy serwer obsługuje tylko [samodzielne wdrożenia (SCD).](/dotnet/core/deploying/#self-contained-deployments-scd)
   * `OPT_NO_SHAREDFX=1`&ndash; Pomiń instalowanie ASP.NET udostępnionej struktury (ASP.NET środowiska uruchomieniowego). Używane, gdy serwer obsługuje tylko [samodzielne wdrożenia (SCD).](/dotnet/core/deploying/#self-contained-deployments-scd)
   * `OPT_NO_X86=1`&ndash; Pomiń instalowanie środowiska wykonawczego x86. Użyj tego parametru, gdy wiesz, że nie będziesz hostować aplikacji 32-bitowych. Jeśli istnieje jakakolwiek szansa, że będziesz hostować zarówno aplikacje 32-bitowe, jak i 64-bitowe w przyszłości, nie używaj tego parametru i instaluj oba środowiska wykonawcze.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`&ndash; Wyłącz sprawdzanie przy użyciu konfiguracji udostępnionej usług IIS, gdy konfiguracja udostępniona *(applicationHost.config)* znajduje się na tym samym komputerze co instalacja usług IIS. *Dostępne tylko dla instalatorów ASP.NET Core 2.2 lub nowszych Hosting Bundler.* Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Uruchom ponownie system lub wykonaj następujące polecenia w powłoce poleceń:

   ```console
   net stop was /y
   net start w3svc
   ```
   Ponowne uruchomienie usługi IIS odbiera zmianę w systemie PATH, która jest zmienną środowiskową, dokonaną przez instalatora.

ASP.NET Core nie przyjmuje zachowania do przekazywania w celu wydania poprawek udostępnionych pakietów ramowych. Po uaktualnieniu udostępnionej struktury przez zainstalowanie nowego pakietu hostingowego, uruchom ponownie system lub wykonaj następujące polecenia w powłoce poleceń:

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> Aby uzyskać informacje na temat konfiguracji udostępnionej usług IIS, zobacz [ASP.NET modułu rdzenia z konfiguracją współużytku iis](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Instalowanie wdrażania w sieci Web podczas publikowania w programie Visual Studio

Podczas wdrażania aplikacji na serwerach z [programem Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)należy zainstalować najnowszą wersję programu Web Deploy na serwerze. Aby zainstalować program Web Deploy, użyj [instalatora platformy sieci Web (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) lub uzyskaj instalator bezpośrednio z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=43717). Preferowaną metodą jest użycie interfejsu WebPI. WebPI oferuje autonomiczną konfigurację i konfigurację dla dostawców hostingu.

## <a name="create-the-iis-site"></a>Tworzenie witryny usługi IIS

1. W systemie hostingowym utwórz folder zawierający opublikowane foldery i pliki aplikacji. W następnym kroku ścieżka folderu jest dostarczana do iIS jako ścieżka fizyczna do aplikacji. Aby uzyskać więcej informacji na temat folderu <xref:host-and-deploy/directory-structure>wdrażania aplikacji i układu plików, zobacz .

1. W Menedżerze usług IIS otwórz węzeł serwera w panelu **Połączenia.** Kliknij prawym przyciskiem myszy folder **Witryny.** Z menu kontekstowego **wybierz polecenie Dodaj witrynę sieci Web.**

1. Podaj **nazwę witryny** i ustaw **ścieżkę fizyczną** do folderu wdrażania aplikacji. Podaj konfigurację **powiązania** i utwórz witrynę sieci Web, wybierając **przycisk OK:**

   ![Podaj nazwę witryny, ścieżkę fizyczną i nazwę hosta w kroku Dodaj witrynę sieci Web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Wiązania wieloznaczne najwyższego`http://*:80/` `http://+:80`poziomu ( i ) **nie** powinny być używane. Powiązania symboli wieloznacznych najwyższego poziomu mogą otworzyć aplikację na luki w zabezpieczeniach. Dotyczy to zarówno silnych, jak i słabych symboli wieloznacznych. Użyj jawnych nazw hostów, a nie symboli wieloznacznych. Powiązanie symboli wieloznacznych `*.mysub.com`poddomeny (na przykład) nie ma tego zagrożenia bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do `*.com`, która jest zagrożona). Zobacz [rfc7230 section-5.4,](https://tools.ietf.org/html/rfc7230#section-5.4) aby uzyskać więcej informacji.

1. W obszarze węzła serwera wybierz pozycję **Pule aplikacji**.

1. Kliknij prawym przyciskiem myszy pulę aplikacji witryny i wybierz polecenie **Ustawienia podstawowe** z menu kontekstowego.

1. W oknie **Edytuj pulę aplikacji** ustaw **wersję .NET CLR** na **Brak kodu zarządzanego:**

   ![Ustaw brak kodu zarządzanego dla wersji CLR .NET.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core działa w oddzielnym procesie i zarządza środowiska wykonawczego. ASP.NET Core nie polega na załadowaniu pulpitu CLR (.NET CLR)&mdash;Core Common Language Runtime (CoreCLR) dla platformy .NET Core jest uruchamiany w celu hostowania aplikacji w procesie roboczym. Ustawienie **wersji .NET CLR** na **Brak kodu zarządzanego** jest opcjonalne, ale zalecane.

1. *ASP.NET Core 2.2 lub nowszym:* W przypadku [samodzielnego wdrożenia](/dotnet/core/deploying/#self-contained-deployments-scd) 64-bitowego (x64), które korzysta z [modelu hostingu w trakcie,](#in-process-hosting-model)wyłącz pulę aplikacji dla procesów 32-bitowych (x86).

   Na pasku bocznym **Akcje** Menedżera usług IIS > **pul aplikacji**wybierz pozycję **Ustaw ustawienia domyślne puli aplikacji** lub Ustawienia **zaawansowane**. Zlokalizuj **włącz aplikacje 32-bitowe** i ustaw wartość na `False`. To ustawienie nie ma wpływu na aplikacje wdrożone do [hostingu poza procesem.](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)

1. Upewnij się, że tożsamość modelu procesu ma odpowiednie uprawnienia.

   Jeśli domyślna tożsamość puli aplikacji > **(Tożsamość****modelu procesu)** zostanie zmieniona z **ApplicationPoolIdentity** na inną tożsamość, sprawdź, czy nowa tożsamość ma wymagane uprawnienia dostępu do folderu, bazy danych i innych wymaganych zasobów aplikacji. Na przykład pula aplikacji wymaga dostępu do odczytu i zapisu do folderów, w których aplikacja odczytuje i zapisuje pliki.

**Konfiguracja uwierzytelniania systemu Windows (opcjonalnie)**  
Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

Wdrażanie aplikacji w folderze **ścieżki fizycznej** iIS, który został ustanowiony w sekcji [Tworzenie witryny IIS.](#create-the-iis-site) [Wdrażanie w sieci Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) jest zalecanym mechanizmem wdrażania, ale istnieje kilka opcji przenoszenia aplikacji z folderu *publikowania* projektu do folderu wdrażania systemu hostingowego.

### <a name="web-deploy-with-visual-studio"></a>Wdrażanie w sieci Web za pomocą programu Visual Studio

Zobacz profile publikowania w [programie Visual Studio, aby ASP.NET temat wdrażania aplikacji Core,](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) aby dowiedzieć się, jak utworzyć profil publikowania do użytku z wdrażaniem w sieci Web. Jeśli dostawca hostingu udostępnia profil publikowania lub obsługę tworzenia, pobierz jego profil i zaimportuj go za pomocą okna dialogowego **Publikowania** programu Visual Studio:

![Strona okna dialogowego Publikowania](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Wdrażanie w sieci Web poza programem Visual Studio

[Wdrażanie w sieci Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) może być również używane poza programem Visual Studio z wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Narzędzie wdrażania sieci Web](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternatywy dla wdrażania w sieci Web

Użyj dowolnej z kilku metod, aby przenieść aplikację do systemu hostingowego, takich jak [ręczne kopiowanie, Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)lub [PowerShell](/powershell/).

Aby uzyskać więcej informacji na temat wdrożenia ASP.NET core w usługach IIS, zobacz sekcję [Zasoby wdrażania dla administratorów usług IIS.](#deployment-resources-for-iis-administrators)

## <a name="browse-the-website"></a>Przeglądanie witryny sieci Web

Po wdrożeniu aplikacji w systemie hostingowym należy wysłać żądanie do jednego z publicznych punktów końcowych aplikacji.

W poniższym przykładzie witryna jest powiązana z `www.mysite.com` **nazwą hosta** IIS w **porcie** `80`. Wniosek składa się `http://www.mysite.com`na:

![Przeglądarka Microsoft Edge załadowała stronę startową usługi IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Zablokowane pliki wdrażania

Pliki w folderze wdrażania są zablokowane, gdy aplikacja jest uruchomiona. Zablokowanych plików nie można nadpisywane podczas wdrażania. Aby zwolnić zablokowane pliki we wdrożeniu, zatrzymaj pulę aplikacji przy użyciu **jednego** z następujących metod:

* Użyj wdrażania w `Microsoft.NET.Sdk.Web` sieci Web i odwołania w pliku projektu. Plik *app_offline.htm* jest umieszczany w katalogu głównym katalogu aplikacji sieci web. Gdy plik jest obecny, ASP.NET Moduł podstawowy bezpiecznie zamyka aplikację i obsługuje *plik app_offline.htm* podczas wdrażania. Aby uzyskać więcej informacji, zobacz [ASP.NET odwołanie do konfiguracji modułu rdzenia](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Ręcznie zatrzymaj pulę aplikacji w Menedżerze usług IIS na serwerze.
* Użyj programu PowerShell, aby upuścić *app_offline.htm* (wymaga programu PowerShell 5 lub nowszego):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Ochrona danych

[Stos ASP.NET Core Data Protection](xref:security/data-protection/introduction) jest używany przez kilka ASP.NET core [oprogramowania pośredniczącego,](xref:fundamentals/middleware/index)w tym oprogramowania pośredniczącego używanego w uwierzytelniania. Nawet jeśli interfejsy API ochrony danych nie są wywoływane przez kod użytkownika, ochrona danych powinna być skonfigurowana za pomocą skryptu wdrażania lub kodu użytkownika w celu utworzenia trwałego [magazynu kluczy](xref:security/data-protection/implementation/key-management)kryptograficznych . Jeśli ochrona danych nie jest skonfigurowana, klucze są przechowywane w pamięci i odrzucane po ponownym uruchomieniu aplikacji.

Jeśli pierścień klucza jest przechowywany w pamięci po ponownym uruchomieniu aplikacji:

* Wszystkie tokeny uwierzytelniania oparte na plikach cookie są unieważnione. 
* Użytkownicy muszą zalogować się ponownie przy następnym żądaniu. 
* Nie można już odszyfrować żadnych danych chronionych za pomocą pierścienia kluczy. Może to obejmować [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) i [ASP.NET podstawowe pliki cookie MVC TempData](xref:fundamentals/app-state#tempdata).

Aby skonfigurować ochronę danych w ramach usługi IIS do utrwalania pierścienia kluczy, należy użyć **jednego** z następujących metod:

* **Tworzenie kluczy rejestru ochrony danych**

  Klucze ochrony danych używane przez aplikacje ASP.NET Core są przechowywane w rejestrze zewnętrznym dla aplikacji. Aby utrwalić klucze dla danej aplikacji, utwórz klucze rejestru dla puli aplikacji.

  W przypadku autonomicznych instalacji usług IIS innych niż webfarm [skrypt PowerShell zapewnia ochronę danych](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) może być używany dla każdej puli aplikacji używanej z aplikacją ASP.NET Core. Ten skrypt tworzy klucz rejestru w rejestrze HKLM, który jest dostępny tylko dla konta procesu roboczego puli aplikacji. Klucze są szyfrowane w spoczynku przy użyciu DPAPI z kluczem dla całego komputera.

  W scenariuszach farmy sieci web można skonfigurować aplikację do używania ścieżki UNC do przechowywania pierścienia kluczy ochrony danych. Domyślnie klucze ochrony danych nie są szyfrowane. Upewnij się, że uprawnienia do plików dla udziału sieciowego są ograniczone do konta systemu Windows, pod które działa aplikacja. Certyfikat X509 może służyć do ochrony kluczy w spoczynku. Należy wziąć pod uwagę mechanizm umożliwiający użytkownikom przekazywanie certyfikatów: Umieść certyfikaty w zaufanym magazynie certyfikatów użytkownika i upewnij się, że są one dostępne na wszystkich komputerach, na których działa aplikacja użytkownika. Szczegółowe informacje można [znaleźć w temacie Konfigurowanie ASP.NET podstawowej ochrony danych.](xref:security/data-protection/configuration/overview)

* **Konfigurowanie puli aplikacji usług IIS w celu załadowania profilu użytkownika**

  To ustawienie znajduje się w sekcji **Model procesu** w obszarze **Ustawienia zaawansowane** dla puli aplikacji. Ustaw **załaduj profil użytkownika** na `True`. Po ustawieniu `True`klawiszy są przechowywane w katalogu profilu użytkownika i chronione przy użyciu dpapi z kluczem specyficznym dla konta użytkownika. Klucze są zachowywane w folderze *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys.*

  Należy również [włączyć atrybut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) puli aplikacji. Wartością `setProfileEnvironment` domyślną `true`jest . W niektórych scenariuszach (na przykład `setProfileEnvironment` system `false`operacyjny Windows) jest ustawiona na . Jeśli klucze nie są przechowywane w katalogu profilu użytkownika zgodnie z oczekiwaniami:

  1. Przejdź do folderu *%windir%/system32/inetsrv/config.*
  1. Otwórz plik *applicationHost.config.*
  1. Zlokalizuj `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.
  1. Upewnij się, że `setProfileEnvironment` atrybut nie jest obecny, `true`co domyślnie wartość lub jawnie `true`ustawić wartość atrybutu .

* **Używanie systemu plików jako magazynu dzwonków z kluczami**

  Dostosuj kod aplikacji, aby [używać systemu plików jako magazynu kluczy](xref:security/data-protection/configuration/overview). Użyj certyfikatu X509, aby chronić pierścień kluczy i upewnić się, że certyfikat jest zaufanym certyfikatem. Jeśli certyfikat jest podpisany samodzielnie, umieść go w magazynie Zaufanych Root.

  W przypadku korzystania z usług IIS w farmie internetowej:

  * Użyj udziału plików, do którego mają dostęp wszystkie maszyny.
  * Wdrażanie certyfikatu X509 na każdym komputerze. Skonfiguruj [ochronę danych w kodzie](xref:security/data-protection/configuration/overview).

* **Ustawianie zasad ochrony danych dla całej maszyny**

  System ochrony danych ma ograniczoną obsługę ustawiania domyślnych [zasad dla wszystkich](xref:security/data-protection/configuration/machine-wide-policy) aplikacji, które korzystają z interfejsów API ochrony danych. Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Katalogi wirtualne

[Katalogi wirtualne usług IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) nie są obsługiwane w aplikacjach ASP.NET Core. Aplikacja może być hostowana jako [podnauka](#sub-applications).

## <a name="sub-applications"></a>Podprocesy

Aplikacja ASP.NET Core może być hostowana jako [podnauka usług IIS (podnauka).](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) Ścieżka aplikacji podrzędnej staje się częścią adresu URL aplikacji głównej.

Statyczne łącza zasobów w aplikacji podrzędnej powinny`~/`używać notacji tilde-slash ( ). Notacja o rozszyciu tyldy wyzwala [Pomocnika tagów,](xref:mvc/views/tag-helpers/intro) aby dołączyć do bazy ścieżek aplikacji podrzędnej do renderowanego łącza względnego. W przypadku poddolizmy w `/subapp_path` `src="~/image.png"` punkcie , `src="/subapp_path/image.png"`obraz połączony z jest renderowany jako . Oprogramowanie pośredniczące w pliku statycznym aplikacji głównej nie przetwarza żądania pliku statycznego. Żądanie jest przetwarzane przez oprogramowanie pośredniczące statycznego pliku aplikacji sub-app.

Jeśli atrybut zasobu `src` statycznego jest ustawiony na ścieżkę `src="/image.png"`bezwzględną (na przykład), łącze jest renderowane bez bazy ścieżek aplikacji podrzędnej. Oprogramowanie pośredniczące w aplikacji głównej Statyczne pliki próbuje obsługiwać zasób z katalogu głównego aplikacji [głównej,](xref:fundamentals/index#web-root)co skutkuje *odpowiedzią 404 — nie znaleziono,* chyba że zasób statyczny jest dostępny z aplikacji głównej.

Aby hostować aplikację core ASP.NET jako poddołkę w innej aplikacji ASP.NET Core:

1. Ustanowienie puli aplikacji dla podkoszuli. Ustaw **wersję CLR .NET** na **Brak kodu zarządzanego,** ponieważ podstawowy wspólny język runtime (CoreCLR) dla .NET Core jest uruchamiany do obsługi aplikacji w procesie roboczym, a nie na pulpicie CLR (.NET CLR).

1. Dodaj witrynę główną w Menedżerze usług IIS z aplikacją podrzędną w folderze pod witryną główną.

1. Kliknij prawym przyciskiem myszy folder aplikacji podrzędnej w Menedżerze usług IIS i wybierz polecenie **Konwertuj na aplikację**.

1. W oknie dialogowym **Dodawanie aplikacji** użyj przycisku **Wybierz** dla **puli aplikacji,** aby przypisać pulę aplikacji utworzoną dla aplikacji podrzędnej. Kliknij przycisk **OK**.

Przypisanie oddzielnej puli aplikacji do podkoszuli jest wymagane podczas korzystania z modelu hostingu w trakcie.

Aby uzyskać więcej informacji na temat modelu hostingu w trakcie procesu <xref:host-and-deploy/aspnet-core-module>i konfigurowania ASP.NET Core Module, zobacz .

## <a name="configuration-of-iis-with-webconfig"></a>Konfiguracja usług IIS za pomocą pliku web.config

Na konfigurację usług IIS ma wpływ `<system.webServer>` sekcja *web.config* dla scenariuszy usług IIS, które działają dla aplikacji ASP.NET Core z modułem ASP.NET Core. Na przykład konfiguracja usług IIS działa w przypadku kompresji dynamicznej. Jeśli usługi IIS są skonfigurowane na poziomie `<urlCompression>` serwera do używania kompresji dynamicznej, element w pliku *web.config* aplikacji może go wyłączyć dla aplikacji ASP.NET Core.

Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Odwołanie do \<konfiguracji dla>system.webServer](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Aby ustawić zmienne środowiskowe dla poszczególnych aplikacji działających w pulach aplikacji izolowanych (obsługiwanych dla usług IIS 10.0 lub nowszych), zobacz *sekcję polecenia AppCmd.exe* [środowiska zmiennych środowiska Zmienne \<środowiskoweWarzyny>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) tematu w dokumentacji odwołania usług IIS.

## <a name="configuration-sections-of-webconfig"></a>Sekcje konfiguracji web.config

Sekcje konfiguracji aplikacji ASP.NET 4.x w *witrynie web.config* nie są używane przez aplikacje ASP.NET Core do konfiguracji:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

aplikacje ASP.NET Core są konfigurowane przy użyciu innych dostawców konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Pule aplikacji

Izolacja puli aplikacji jest określana przez model hostingu:

* W trakcie &ndash; hostingu Aplikacje są wymagane do uruchamiania w oddzielnych pulach aplikacji.
* Hosting &ndash; poza procesem Zaleca izolowanie aplikacji od siebie, uruchamiając każdą aplikację we własnej puli aplikacji.

Domyślne okno dialogowe **Dodaj witrynę sieci Web** dla aplikacji iis dla jednej puli aplikacji na aplikację. Po podaniu **nazwy witryny** tekst jest automatycznie przesyłany do pola tekstowego **Puli aplikacji.** Nowa pula aplikacji jest tworzona przy użyciu nazwy witryny po dodaniu witryny.

## <a name="application-pool-identity"></a>Tożsamość puli aplikacji

Konto tożsamości puli aplikacji umożliwia aplikacji uruchamianie na unikatowym koncie bez konieczności tworzenia domen lub kont lokalnych oraz zarządzania nimi. W serwisach IIS 8.0 lub nowszych proces administracyjnego procesu roboczego usług IIS (WAS) tworzy konto wirtualne o nazwie nowej puli aplikacji i domyślnie uruchamia procesy robocze puli aplikacji w ramach tego konta. W konsoli zarządzania usługami IIS w obszarze **Ustawienia zaawansowane** dla puli aplikacji upewnij się, że **tożsamość** jest ustawiona na używanie **applicationpoolidentity:**

![Okno dialogowe Ustawień zaawansowanych puli aplikacji](index/_static/apppool-identity.png)

Proces zarządzania usługami IIS tworzy bezpieczny identyfikator o nazwie puli aplikacji w systemie zabezpieczeń systemu Windows. Zasoby można zabezpieczyć przy użyciu tej tożsamości. Jednak ta tożsamość nie jest rzeczywistym kontem użytkownika i nie jest chylić się w Konsoli zarządzania użytkownikami systemu Windows.

Jeśli proces roboczy IIS wymaga podwyższonego dostępu do aplikacji, zmodyfikuj listę kontroli dostępu (ACL) dla katalogu zawierającego aplikację:

1. Otwórz Eksploratora Windows i przejdź do katalogu.

1. Kliknij prawym przyciskiem myszy katalog i wybierz polecenie **Właściwości**.

1. W obszarze karta **Zabezpieczenia** wybierz przycisk **Edytuj,** a następnie przycisk **Dodaj.**

1. Wybierz przycisk **Lokalizacje** i upewnij się, że system jest zaznaczony.

1. Wprowadź **\\<<app_pool_name<usług IIS>** w obszarze Wprowadź nazwy **obiektów, aby wybrać** obszar. Wybierz przycisk **Sprawdź nazwy.** W przypadku *pola DefaultAppPool* sprawdź nazwy przy użyciu usługi **IIS AppPool\DefaultAppPool**. Po wybraniu przycisku **Sprawdź nazwy** w obszarze nazwy obiektów jest wskazywana wartość **DefaultAppPool.** Nie można wprowadzić nazwy puli aplikacji bezpośrednio w obszarze nazw obiektów. Podczas sprawdzania nazwy obiektu należy używać **\\<<app_pool_name>.**

   ![Wybierz okno dialogowe Użytkownicy lub grupy dla folderu aplikacji: Nazwa puli aplikacji "DefaultAppPool" jest dołączana do "Puli aplikacji usług IIS\" w obszarze nazw obiektów przed wybraniem opcji "Sprawdź nazwy".](index/_static/select-users-or-groups-1.png)

1. Kliknij przycisk **OK**.

   ![Wybierz okno dialogowe Użytkownicy lub grupy dla folderu aplikacji: Po wybraniu opcji "Sprawdź nazwy" nazwa obiektu "DefaultAppPool" jest wyświetlana w obszarze nazw obiektów.](index/_static/select-users-or-groups-2.png)

1. Uprawnienia &amp; do wykonania odczytu powinny być przyznawane domyślnie. W razie potrzeby podaj dodatkowe uprawnienia.

Dostęp można również udzielić w wierszu polecenia za pomocą narzędzia **ICACLS.** Na przykład przy użyciu *domyślnej dupy jako* przykładu używane jest następujące polecenie:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Aby uzyskać więcej informacji, zobacz temat [icacls.](/windows-server/administration/windows-commands/icacls)

## <a name="http2-support"></a>Obsługa protokołu HTTP/2

[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwany przez ASP.NET Core w następujących scenariuszach wdrażania usług IIS:

* W trakcie procesu
  * Windows Server 2016/Windows 10 lub nowsza; IIS 10 lub nowsze
  * Połączenie TLS 1.2 lub nowsze
* Poza procesem
  * Windows Server 2016/Windows 10 lub nowsza; IIS 10 lub nowsze
  * Połączenia serwerów brzegowych wychodzących na publicznej stronie używają protokołu HTTP/2, ale odwrotne połączenie proxy z [serwerem Kestrel](xref:fundamentals/servers/kestrel) używa protokołu HTTP/1.1.
  * Połączenie TLS 1.2 lub nowsze

W przypadku wdrożenia w trakcie po ustanowieniu połączenia HTTP/2, `HTTP/2` [httprequest.protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) zgłasza . W przypadku wdrożenia poza procesem po nawiązaniu połączenia HTTP/2 raporty `HTTP/1.1` [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) .

Aby uzyskać więcej informacji na temat modeli hostingu w <xref:host-and-deploy/aspnet-core-module>trakcie i poza procesem, zobacz .

Protokół HTTP/2 jest domyślnie włączony. Połączenia są przywracane do protokołu HTTP/1.1, jeśli połączenie HTTP/2 nie zostanie nawiązane. Aby uzyskać więcej informacji na temat konfiguracji HTTP/2 z wdrożeniami usług IIS, zobacz [HTTP/2 na usługach IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Żądania inspekcji wstępnej CORS

*Ta sekcja dotyczy tylko aplikacji ASP.NET Core, które są przeznaczone dla programu .NET Framework.*

W przypadku aplikacji ASP.NET Core przeznaczonej dla programu .NET Framework żądania opcji nie są domyślnie przekazywane do aplikacji w uiświadach. Aby dowiedzieć się, jak skonfigurować programy obsługi usług IIS aplikacji w *witrynie web.config* do przekazywania żądań OPCJI, zobacz [Włączanie żądań między źródłami w ASP.NET interfejsu API sieci Web 2: Jak działa cors](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Moduł inicjowania aplikacji i limit czasu bezczynnia

Hostowane w serwisach IIS przez moduł ASP.NET Core w wersji 2:

* Moduł [inicjowania aplikacji](#application-initialization-module) obsługiwany [w procesie](#in-process-hosting-model) lub poza procesem można skonfigurować tak, aby uruchamiał się automatycznie po ponownym uruchomieniu procesu roboczego lub ponownym uruchomieniu serwera. [out-of-process](#out-of-process-hosting-model) &ndash;
* [Idle Timeout](#idle-timeout) &ndash; App hostowane [w procesie](#in-process-hosting-model) można skonfigurować, aby nie limit czasu w okresach braku aktywności.

### <a name="application-initialization-module"></a>Moduł inicjowania aplikacji

*Dotyczy aplikacji hostowanych w procesie i poza procesem.*

[Inicjowanie aplikacji usług IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) to funkcja usług IIS, która wysyła żądanie HTTP do aplikacji po uruchomieniu lub odtworzeniu puli aplikacji. Żądanie wyzwala aplikację do uruchomienia. Domyślnie usługi IIS wystawiają żądanie do`/`głównego adresu URL aplikacji ( ) w celu zainicjowania aplikacji (zobacz [dodatkowe zasoby,](#application-initialization-module-and-idle-timeout-additional-resources) aby uzyskać więcej informacji na temat konfiguracji).

Upewnij się, że funkcja roli inicjowania aplikacji usług IIS jest włączona:

W systemach Windows 7 lub nowszych w systemach stacjonarnych korzystających z usług IIS lokalnie:

1. Przejdź do pozycji **Programy i funkcje** > **Panelu** > **sterowania** > **Włączanie lub wyłączanie funkcji systemu Windows** (po lewej stronie ekranu).
1. Otwórz **funkcje tworzenia aplikacji**sieci Web **w sieci** > **World Web Services** > .
1. Zaznacz pole wyboru **Inicjowania aplikacji**.

W systemie Windows Server 2008 R2 lub nowszym:

1. Otwórz **Kreatora dodawania ról i funkcji**.
1. W panelu **Wybierz usługi roli** otwórz węzeł Tworzenie **aplikacji.**
1. Zaznacz pole wyboru **Inicjowania aplikacji**.

Użyj jednej z następujących metod, aby włączyć moduł inicjowania aplikacji dla lokacji:

* Korzystanie z Menedżera usług IIS:

  1. Wybierz **pule aplikacji** w panelu **Połączenia.**
  1. Kliknij prawym przyciskiem myszy pulę aplikacji na liście i wybierz pozycję **Ustawienia zaawansowane**.
  1. Domyślnym **trybem startowym** jest **OnDemand**. Ustaw **tryb startu** na **AlwaysRunning**. Kliknij przycisk **OK**.
  1. Otwórz węzeł **Lokacje** w panelu **Połączenia.**
  1. Kliknij prawym przyciskiem myszy aplikację i wybierz pozycję **Zarządzaj** > **ustawieniami zaawansowanymi witryny**.
  1. Domyślnym **ustawieniem włączone ładowanie wstępne** jest **Fałsz**. Ustaw **włączone napięcie wstępne na** **true**. Kliknij przycisk **OK**.

* Za pomocą *web.config*, `doAppInitAfterRestart` dodać `true` `<applicationInitialization>` element `<system.webServer>` z zestawem do elementów w pliku *web.config* aplikacji:

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

*Dotyczy tylko aplikacji hostowanych w toku.*

Aby zapobiec pracy na biegu jałowym aplikacji, ustaw limit czasu bezczynność puli aplikacji za pomocą Menedżera usług IIS:

1. Wybierz **pule aplikacji** w panelu **Połączenia.**
1. Kliknij prawym przyciskiem myszy pulę aplikacji na liście i wybierz pozycję **Ustawienia zaawansowane**.
1. Domyślny **limit czasu bezczynnego (minuty)** wynosi **20** minut. Ustaw **limit czasu bezczynnego (minuty)** na **0** (zero). Kliknij przycisk **OK**.
1. Recykling procesu roboczego.

Aby zapobiec przesunieniu limitu czasu przez aplikacje hostowane [poza procesem,](#out-of-process-hosting-model) użyj jednej z następujących metod:

* Ping aplikacji z usługi zewnętrznej w celu utrzymania go w pracy.
* Jeśli aplikacja obsługuje tylko usługi w tle, unikaj hostingu usług IIS i użyj [usługi systemu Windows do hostowania aplikacji ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Moduł inicjowania aplikacji i limit czasu bezczynnia dodatkowe zasoby

* [Inicjowanie aplikacji usług IIS 8.0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Aplikacja Inicjalizacja \<aplikacjiJanitializacja>](/iis/configuration/system.webserver/applicationinitialization/).
* [Ustawienia modelu procesu dla \<procesu puli aplikacjiModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="deployment-resources-for-iis-administrators"></a>Zasoby wdrażania dla administratorów usług IIS

* [Dokumentacja iis](/iis)
* [Wprowadzenie do menedżera usług IIS w usługach IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Wdrożenie aplikacji .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:test/troubleshoot>
* <xref:index>
* [Oficjalna witryna microsoft iis](https://www.iis.net/)
* [Biblioteka zawartości technicznej systemu Windows Server](/windows-server/windows-server)
* [HTTP/2 na usługi IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Aby zapoznać się z samouczkiem dotyczących publikowania aplikacji core <xref:tutorials/publish-to-iis>ASP.NET na serwerze usług IIS, zobacz .

[Zainstaluj pakiet hostingowy .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Obsługiwane są następujące systemy operacyjne:

* Windows 7 lub nowsze
* Windows Server 2008 R2 lub nowszy

[Serwer HTTP.sys](xref:fundamentals/servers/httpsys) (dawniej nazywany WebListener) nie działa w odwrotnej konfiguracji serwera proxy z usługami IIS. Użyj [serwera Kestrel](xref:fundamentals/servers/kestrel).

Aby uzyskać informacje na <xref:host-and-deploy/azure-apps/index>temat hostingu na platformie Azure, zobacz .

Aby uzyskać wskazówki <xref:test/troubleshoot>dotyczące rozwiązywania problemów, zobacz .

## <a name="supported-platforms"></a>Obsługiwane platformy

Obsługiwane są aplikacje opublikowane dla wdrożenia 32-bitowego (x86) lub 64-bitowego (x64). Wdrażanie 32-bitowej aplikacji z 32-bitowym (x86) .NET Core SDK, chyba że aplikacja:

* Wymaga większej przestrzeni adresowej pamięci wirtualnej dostępnej dla aplikacji 64-bitowej.
* Wymaga większego rozmiaru stosu usługi IIS.
* Ma 64-bitowe natywne zależności.

Użyj 64-bitowego (x64) .NET Core SDK, aby opublikować aplikację 64-bitową. 64-bitowy środowiska uruchomieniowego musi być obecny w systemie hosta.

## <a name="hosting-models"></a>Modele hostingu

### <a name="in-process-hosting-model"></a>Model hostingu w trakcie

Korzystając z hostingu w trakcie, aplikacja ASP.NET Core działa w tym samym procesie co proces roboczy IIS. Hosting w trakcie zapewnia lepszą wydajność w zakresie hostingu poza procesem, ponieważ żądania nie są przerzaszane przez kartę sprzężenia zwrotnego, interfejs sieciowy, który zwraca wychodzący ruch sieciowy z powrotem na ten sam komputer. Usługi IIS obsługują zarządzanie procesami za pomocą [usługi aktywacji procesów systemu Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Moduł [podstawowy ASP.NET:](xref:host-and-deploy/aspnet-core-module)

* Wykonuje inicjowanie aplikacji.
  * Ładuje [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Połączenia `Program.Main`.
* Obsługuje okres istnienia żądania macierzystego IIS.

Model hostingu w trakcie nie jest obsługiwany dla aplikacji ASP.NET Core, które są przeznaczone dla platformy .NET Framework.

Na poniższym diagramie przedstawiono relację między iis, ASP.NET Core Module i aplikacji hostowane w procesie:

![ASP.NET moduł podstawowy w scenariuszu hostingu w trakcie](index/_static/ancm-inprocess.png)

Żądanie jest nadchodają z sieci Web do sterownika HTTP.sys w trybie jądra. Sterownik kieruje żądanie macierzyste do usługi IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS). Moduł ASP.NET Core odbiera żądanie macierzyste i przekazuje je do`IISHttpServer`serwera HTTP usług IIS ( ). Serwer HTTP usług IIS to implementacja serwera w trakcie usług IIS, która konwertuje żądanie z macierzystego na zarządzane.

Po iis serwer HTTP przetwarza żądanie, żądanie jest wypychane do potoku oprogramowania pośredniczącego ASP.NET Core. Potok oprogramowania pośredniczącego obsługuje żądanie i `HttpContext` przekazuje go jako wystąpienie do logiki aplikacji. Odpowiedź aplikacji jest przekazywana z powrotem do usług IIS za pośrednictwem serwera HTTP usług IIS. Usługi IIS wysyłają odpowiedź do klienta, który zainicjował żądanie.

Hosting w trakcie jest opt-in dla istniejących aplikacji, ale [dotnet nowe](/dotnet/core/tools/dotnet-new) szablony domyślnie w trakcie modelu hostingu dla wszystkich scenariuszy usług IIS i IIS Express.

`CreateDefaultBuilder`dodaje <xref:Microsoft.AspNetCore.Hosting.Server.IServer> wystąpienie, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> wywołując metodę uruchamiania [CoreCLR](/dotnet/standard/glossary#coreclr) i hostowania aplikacji wewnątrz procesu roboczego IIS (*w3wp.exe* lub *iisexpress.exe*). Testy wydajności wskazują, że hostowanie aplikacji .NET Core w procesie zapewnia znacznie wyższą przepływność żądania w porównaniu do hostowania aplikacji poza procesem i proxy do serwera [Kestrel.](xref:fundamentals/servers/kestrel)

### <a name="out-of-process-hosting-model"></a>Model hostingu poza procesem

Ponieważ aplikacje core ASP.NET są uruchamiane w procesie odrębnym od procesu roboczego usług IIS, moduł ASP.NET Core obsługuje zarządzanie procesami. Moduł uruchamia proces aplikacji ASP.NET Core po nadejściu pierwszego żądania i uruchamia ponownie aplikację, jeśli zostanie zamknięta lub ulegnie awarii. Jest to zasadniczo to samo zachowanie, co w aplikacjach uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (WAS).](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)

Na poniższym diagramie przedstawiono relację między iis, ASP.NET Core Module i aplikacji hostowane poza procesem:

![ASP.NET modułu podstawowego w scenariuszu hostingu poza procesem](index/_static/ancm-outofprocess.png)

Żądania przychodzą z sieci Web do sterownika HTTP.sys w trybie jądra. Sterownik kieruje żądania do iIS w skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS). Moduł przekazuje żądania do Kestrel na losowym porcie dla aplikacji, który nie jest portem 80 lub 443.

Moduł określa port za pośrednictwem zmiennej środowiskowej <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> podczas uruchamiania, a `http://localhost:{PORT}`rozszerzenie konfiguruje serwer do nasłuchiwać na . Dodatkowe kontrole są wykonywane, a żądania, które nie pochodzą z modułu są odrzucane. Moduł nie obsługuje przekazywania protokołu HTTPS, więc żądania są przekazywane za pośrednictwem protokołu HTTP, nawet jeśli są odbierane przez usługi IIS za pośrednictwem protokołu HTTPS.

Po Kestrel odbiera żądanie z modułu, żądanie jest wypychane do potoku oprogramowania pośredniczącego ASP.NET Core. Potok oprogramowania pośredniczącego obsługuje żądanie i `HttpContext` przekazuje go jako wystąpienie do logiki aplikacji. Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i bazę ścieżek w celu przekazania żądania do Kestrel. Odpowiedź aplikacji jest przekazywana z powrotem do iIS, co wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.

Aby uzyskać ASP.NET wskazówki dotyczące <xref:host-and-deploy/aspnet-core-module>konfiguracji modułu rdzenia, zobacz .

Aby uzyskać więcej informacji na temat hostingu, zobacz [Host w ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Konfiguracja aplikacji

### <a name="enable-the-iisintegration-components"></a>Włączanie składników integracji iis

Podczas tworzenia hosta w `CreateWebHostBuilder` *(Program.cs*), wywołanie, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> aby włączyć integrację usługi IIS:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Aby uzyskać `CreateDefaultBuilder`więcej <xref:fundamentals/host/web-host#set-up-a-host>informacji na temat , zobacz .

### <a name="iis-options"></a>Opcje iis

**Model hostingu w trakcie**

Aby skonfigurować opcje serwera usług IIS, <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>należy dołączyć konfigurację usługi w <xref:Microsoft.AspNetCore.Builder.IISServerOptions> programie . Poniższy przykład wyłącza AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Opcja                         | Domyślne | Ustawienie |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Jeśli `true`program IIS `HttpContext.User` Server ustawi uwierzytelnione [przez uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth). Jeśli `false`serwer zapewnia tożsamość `HttpContext.User` i odpowiada na wyzwania tylko wtedy, `AuthenticationScheme`gdy jest to wyraźnie wymagane przez plik . Uwierzytelnianie systemu Windows musi być `AutomaticAuthentication` włączone w iis, aby działały. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Ustawia wyświetlaną nazwę wyświetlaną użytkownikom na stronach logowania. |

**Model hostingu poza procesem**

Aby skonfigurować opcje usług IIS, <xref:Microsoft.AspNetCore.Builder.IISOptions> <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>należy dołączyć konfigurację usługi w programie . Poniższy przykład zapobiega zapełnianiu `HttpContext.Connection.ClientCertificate`aplikacji:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Opcja                         | Domyślne | Ustawienie |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Jeśli `true`oprogramowanie [pośredniczące integracji iIS](#enable-the-iisintegration-components) ustawia `HttpContext.User` uwierzytelnione przez [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth). Jeśli `false`oprogramowanie pośredniczące zapewnia `HttpContext.User` tylko tożsamość i odpowiada na wyzwania, gdy jest to wyraźnie wymagane przez `AuthenticationScheme`. Uwierzytelnianie systemu Windows musi być `AutomaticAuthentication` włączone w iis, aby działały. Aby uzyskać więcej informacji, zobacz temat [Uwierzytelniania systemu Windows.](xref:security/authentication/windowsauth) |
| `AuthenticationDisplayName`    | `null`  | Ustawia wyświetlaną nazwę wyświetlaną użytkownikom na stronach logowania. |
| `ForwardClientCertificate`     | `true`  | Jeśli `true` nagłówek `MS-ASPNETCORE-CLIENTCERT` żądania jest obecny, jest wypełniany. `HttpContext.Connection.ClientCertificate` |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

[Oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components), które konfiguruje oprogramowanie pośredniczące nagłówków przesyłanych dalej, oraz ASP.NET Core Module są skonfigurowane do przesyłania dalej schematu (HTTP/HTTPS) i zdalnego adresu IP, z którego pochodzi żądanie. Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych za dodatkowymi serwerami proxy i modułami równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>plik web.config

Plik *web.config* konfiguruje [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module). Tworzenie, przekształcanie i publikowanie pliku *web.config* jest obsługiwane przez obiekt`_TransformWebConfig`docelowy MSBuild ( ) po opublikowaniu projektu. Ten cel jest obecny w celach`Microsoft.NET.Sdk.Web`SDK sieci Web ( ). Zestaw SDK znajduje się w górnej części pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Jeśli pliku *web.config* nie ma w projekcie, plik jest tworzony z poprawnym *processPath* i *argumentami* w celu skonfigurowania ASP.NET modułu rdzenia i przeniesiony do [opublikowanego wyjścia](xref:host-and-deploy/directory-structure).

Jeśli plik *web.config* jest obecny w projekcie, plik jest przekształcany za pomocą poprawnego *processPath* i *argumentów,* aby skonfigurować ASP.NET Core Module i przeniesiony do opublikowanych danych wyjściowych. Transformacja nie modyfikuje ustawień konfiguracji usług IIS w pliku.

Plik *web.config* może zapewniać dodatkowe ustawienia konfiguracji usług IIS, które sterują aktywnymi modułami usług IIS. Aby uzyskać informacje na temat modułów usług IIS, które mogą przetwarzać żądania za pomocą aplikacji ASP.NET Core, zobacz temat [modułów usług IIS.](xref:host-and-deploy/iis/modules)

Aby zapobiec przekształcaniu pliku *web.config przez web.config,* należy użyć właściwości ** \<IsTransformWebConfigDisabled>** w pliku projektu:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Podczas wyłączania zestawu Web SDK z przekształcania pliku, *processPath* i *argumenty* powinny być ręcznie ustawione przez dewelopera. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>lokalizacja pliku web.config

Aby poprawnie skonfigurować [moduł ASP.NET Core,](xref:host-and-deploy/aspnet-core-module) plik *web.config* musi znajdować się przy ścieżce [głównej zawartości](xref:fundamentals/index#content-root) (zazwyczaj ścieżki podstawowej aplikacji) wdrożonej aplikacji. Jest to ta sama lokalizacja, co ścieżka fizyczna witryny sieci Web udostępniana usługom IIS. Plik *web.config* jest wymagany w katalogu głównym aplikacji, aby umożliwić publikowanie wielu aplikacji przy użyciu funkcji Web Deploy.

Poufne pliki istnieją na ścieżce fizycznej aplikacji, takie jak * \<zestaw>.runtimeconfig.json*, * \<zestaw>.xml* (komentarze dokumentacji XML) i * \<zestaw>.deps.json*. Gdy plik *web.config* jest obecny, a witryna uruchamia się normalnie, usługi IIS nie obsługują tych poufnych plików, jeśli są wymagane. Jeśli brakuje pliku *web.config,* niepoprawnie nazwany lub nie można skonfigurować witryny do normalnego uruchamiania, usługi IIS mogą obsługiwać poufne pliki publicznie.

**Plik *web.config* musi być obecny we wdrożeniu przez cały czas, poprawnie nazwany i może skonfigurować witrynę do normalnego uruchamiania. Nigdy nie usuwaj pliku *web.config* z wdrożenia produkcyjnego.**

### <a name="transform-webconfig"></a>Przekształcanie pliku web.config

Jeśli chcesz przekształcić *web.config* przy publikowaniu (na przykład ustawić zmienne środowiskowe na <xref:host-and-deploy/iis/transform-webconfig>podstawie konfiguracji, profilu lub środowiska), zobacz .

## <a name="iis-configuration"></a>Konfiguracja usług IIS

**Systemy operacyjne Windows Server**

Włącz rolę serwera **sieci Web (IIS)** i ustal usługi ról.

1. Użyj Kreatora **Dodawania ról i funkcji** z menu **Zarządzanie** lub łącza w **Menedżerze serwera**. W kroku **Role serwera** zaznacz pole wyboru Dla serwera sieci **Web (IIS)**.

   ![Rola usług IIS serwera sieci Web jest zaznaczona w kroku Wybierz role serwera.](index/_static/server-roles-ws2016.png)

1. Po kroku **Funkcje** krok **usług roli** ładuje się dla serwera sieci Web (IIS). Wybierz żądane usługi ról usług IIS lub zaakceptuj domyślne usługi ról.

   ![Domyślne usługi ról są zaznaczone w kroku Wybierz usługi roli.](index/_static/role-services-ws2016.png)

   **Uwierzytelnianie systemu Windows (opcjonalnie)**  
   Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły:**Zabezpieczenia** **serwera** > sieci Web . Wybierz funkcję **uwierzytelniania systemu Windows.** Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie \<systemu Windows>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

   **WebSockets (opcjonalnie)**  
   WebSockets jest obsługiwany z ASP.NET Core 1.1 lub nowszym. Aby włączyć websockets, rozwiń następujące węzły:**Tworzenie aplikacji** **serwera** > sieci Web . Wybierz funkcję **Protokołu WebSocket.** Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).

1. Przejdź przez krok **potwierdzenia,** aby zainstalować rolę i usługi serwera sieci web. Ponowne uruchomienie serwera/usług IIS nie jest wymagane po zainstalowaniu roli **serwera sieci Web (IIS).**

**Systemy operacyjne Windows dla komputerów stacjonarnych**

Włącz **konsolę zarządzania usługami IIS** i **usługi sieci World Wide Web**.

1. Przejdź do pozycji **Programy i funkcje** > **Panelu** > **sterowania** > **Włączanie lub wyłączanie funkcji systemu Windows** (po lewej stronie ekranu).

1. Otwórz węzeł **Internetowe Usługi informacyjne.** Otwórz węzeł **Narzędzia do zarządzania siecią Web.**

1. Zaznacz pole wyboru **Konsola zarządzania usługami IIS**.

1. Zaznacz pole wyboru **Usługi sieci World Wide Web**.

1. Zaakceptuj domyślne funkcje **usług sieci World Wide Web** lub dostosuj funkcje usług IIS.

   **Uwierzytelnianie systemu Windows (opcjonalnie)**  
   Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **Zabezpieczenia usług** > sieci World Wide Web **.** Wybierz funkcję **uwierzytelniania systemu Windows.** Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie \<systemu Windows>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

   **WebSockets (opcjonalnie)**  
   WebSockets jest obsługiwany z ASP.NET Core 1.1 lub nowszym. Aby włączyć websockets, rozwiń następujące węzły:**Funkcje tworzenia aplikacji** **world wide web services** > . Wybierz funkcję **Protokołu WebSocket.** Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).

1. Jeśli instalacja usług IIS wymaga ponownego uruchomienia, uruchom ponownie system.

![W programie Funkcje systemu Windows wybrano konsolę zarządzania usługami IIS i usługi sieci Web World Wide Web.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Zainstaluj pakiet hostingowy .NET Core

Zainstaluj *pakiet hostingowy .NET Core* w systemie hostingowym. Pakiet instaluje środowisko uruchomieniowe .NET Core, bibliotekę .NET Core library i [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module). Moduł umożliwia ASP.NET aplikacje Core działać za usługami IIS.

> [!IMPORTANT]
> Jeśli pakiet hostingowy jest zainstalowany przed usługami IIS, instalacja pakietu musi zostać naprawiona. Uruchom instalator pakietu hostingowego ponownie po zainstalowaniu usługi IIS.
>
> Jeśli pakiet hostingowy jest zainstalowany po zainstalowaniu 64-bitowej (x64) wersji .NET Core, może wydawać się brak zestawów SDK[(nie wykryto żadnych zestawów SDK .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Aby rozwiązać ten <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>problem, zobacz .

### <a name="direct-download-current-version"></a>Pobieranie bezpośrednie (aktualna wersja)

Pobierz instalatora za pomocą następującego linku:

[Aktualny instalator pakietu hostingu .NET Core (pobieranie bezpośrednie)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Starsze wersje instalatora

Aby uzyskać wcześniejszą wersję instalatora:

1. Przejdź do strony [Pobierz .NET Core.](https://dotnet.microsoft.com/download/dotnet-core)
1. Kliknij żądaną wersję .NET Core.
1. W kolumnie **Uruchom aplikacje — środowisko uruchomieniowe** znajdź wiersz żądanej wersji środowiska uruchomieniowego .NET Core.
1. Pobierz instalatora za pomocą łącza **Runtime & Hosting Bundle.**

> [!WARNING]
> Niektóre instalatory zawierają wersje wersji, które osiągnęły swój koniec życia (EOL) i nie są już obsługiwane przez firmę Microsoft. Aby uzyskać więcej informacji, zobacz [zasady pomocy technicznej](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Zainstaluj pakiet hostingowy

1. Uruchom instalator na serwerze. Następujące parametry są dostępne podczas uruchamiania instalatora z powłoki poleceń administratora:

   * `OPT_NO_ANCM=1`&ndash; Pomiń instalację modułu ASP.NET Core.
   * `OPT_NO_RUNTIME=1`&ndash; Pomiń instalowanie środowiska uruchomieniowego .NET Core. Używane, gdy serwer obsługuje tylko [samodzielne wdrożenia (SCD).](/dotnet/core/deploying/#self-contained-deployments-scd)
   * `OPT_NO_SHAREDFX=1`&ndash; Pomiń instalowanie ASP.NET udostępnionej struktury (ASP.NET środowiska uruchomieniowego). Używane, gdy serwer obsługuje tylko [samodzielne wdrożenia (SCD).](/dotnet/core/deploying/#self-contained-deployments-scd)
   * `OPT_NO_X86=1`&ndash; Pomiń instalowanie środowiska wykonawczego x86. Użyj tego parametru, gdy wiesz, że nie będziesz hostować aplikacji 32-bitowych. Jeśli istnieje jakakolwiek szansa, że będziesz hostować zarówno aplikacje 32-bitowe, jak i 64-bitowe w przyszłości, nie używaj tego parametru i instaluj oba środowiska wykonawcze.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`&ndash; Wyłącz sprawdzanie przy użyciu konfiguracji udostępnionej usług IIS, gdy konfiguracja udostępniona *(applicationHost.config)* znajduje się na tym samym komputerze co instalacja usług IIS. *Dostępne tylko dla instalatorów ASP.NET Core 2.2 lub nowszych Hosting Bundler.* Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Uruchom ponownie system lub wykonaj następujące polecenia w powłoce poleceń:

   ```console
   net stop was /y
   net start w3svc
   ```
   Ponowne uruchomienie usługi IIS odbiera zmianę w systemie PATH, która jest zmienną środowiskową, dokonaną przez instalatora.

Podczas instalowania pakietu hostingowego nie jest konieczne ręczne zatrzymywania poszczególnych witryn w serwisach IIS. Hostowane aplikacje (witryny usług IIS) są ponownie uruchamiane po ponownym uruchomieniu usług IIS. Aplikacje uruchamiają się ponownie po otrzymaniu pierwszego żądania, w tym z [modułu inicjowania aplikacji](#application-initialization-module-and-idle-timeout).

ASP.NET Core przyjmuje zachowanie do przekazywania dla wersji poprawek udostępnionych pakietów framework. Po ponownym uruchomieniu aplikacji obsługiwanych przez usługi IIS aplikacje ładują się z najnowszymi wersjami poprawek pakietów, do których istnieją odwołania, gdy otrzymają pierwsze żądanie. Jeśli usługi IIS nie zostaną ponownie uruchomione, aplikacje ponownie uruchomią się ponownie i wykazują zachowanie do przodu, gdy ich procesy robocze są poddane recyklingowi i otrzymują pierwsze żądanie.

> [!NOTE]
> Aby uzyskać informacje na temat konfiguracji udostępnionej usług IIS, zobacz [ASP.NET modułu rdzenia z konfiguracją współużytku iis](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Instalowanie wdrażania w sieci Web podczas publikowania w programie Visual Studio

Podczas wdrażania aplikacji na serwerach z [programem Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)należy zainstalować najnowszą wersję programu Web Deploy na serwerze. Aby zainstalować program Web Deploy, użyj [instalatora platformy sieci Web (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) lub uzyskaj instalator bezpośrednio z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=43717). Preferowaną metodą jest użycie interfejsu WebPI. WebPI oferuje autonomiczną konfigurację i konfigurację dla dostawców hostingu.

## <a name="create-the-iis-site"></a>Tworzenie witryny usługi IIS

1. W systemie hostingowym utwórz folder zawierający opublikowane foldery i pliki aplikacji. W następnym kroku ścieżka folderu jest dostarczana do iIS jako ścieżka fizyczna do aplikacji. Aby uzyskać więcej informacji na temat folderu <xref:host-and-deploy/directory-structure>wdrażania aplikacji i układu plików, zobacz .

1. W Menedżerze usług IIS otwórz węzeł serwera w panelu **Połączenia.** Kliknij prawym przyciskiem myszy folder **Witryny.** Z menu kontekstowego **wybierz polecenie Dodaj witrynę sieci Web.**

1. Podaj **nazwę witryny** i ustaw **ścieżkę fizyczną** do folderu wdrażania aplikacji. Podaj konfigurację **powiązania** i utwórz witrynę sieci Web, wybierając **przycisk OK:**

   ![Podaj nazwę witryny, ścieżkę fizyczną i nazwę hosta w kroku Dodaj witrynę sieci Web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Wiązania wieloznaczne najwyższego`http://*:80/` `http://+:80`poziomu ( i ) **nie** powinny być używane. Powiązania symboli wieloznacznych najwyższego poziomu mogą otworzyć aplikację na luki w zabezpieczeniach. Dotyczy to zarówno silnych, jak i słabych symboli wieloznacznych. Użyj jawnych nazw hostów, a nie symboli wieloznacznych. Powiązanie symboli wieloznacznych `*.mysub.com`poddomeny (na przykład) nie ma tego zagrożenia bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do `*.com`, która jest zagrożona). Zobacz [rfc7230 section-5.4,](https://tools.ietf.org/html/rfc7230#section-5.4) aby uzyskać więcej informacji.

1. W obszarze węzła serwera wybierz pozycję **Pule aplikacji**.

1. Kliknij prawym przyciskiem myszy pulę aplikacji witryny i wybierz polecenie **Ustawienia podstawowe** z menu kontekstowego.

1. W oknie **Edytuj pulę aplikacji** ustaw **wersję .NET CLR** na **Brak kodu zarządzanego:**

   ![Ustaw brak kodu zarządzanego dla wersji CLR .NET.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core działa w oddzielnym procesie i zarządza środowiska wykonawczego. ASP.NET Core nie polega na załadowaniu pulpitu CLR (.NET CLR)&mdash;Core Common Language Runtime (CoreCLR) dla platformy .NET Core jest uruchamiany w celu hostowania aplikacji w procesie roboczym. Ustawienie **wersji .NET CLR** na **Brak kodu zarządzanego** jest opcjonalne, ale zalecane.

1. *ASP.NET Core 2.2 lub nowszym:* W przypadku [samodzielnego wdrożenia](/dotnet/core/deploying/#self-contained-deployments-scd) 64-bitowego (x64), które korzysta z [modelu hostingu w trakcie,](#in-process-hosting-model)wyłącz pulę aplikacji dla procesów 32-bitowych (x86).

   Na pasku bocznym **Akcje** Menedżera usług IIS > **pul aplikacji**wybierz pozycję **Ustaw ustawienia domyślne puli aplikacji** lub Ustawienia **zaawansowane**. Zlokalizuj **włącz aplikacje 32-bitowe** i ustaw wartość na `False`. To ustawienie nie ma wpływu na aplikacje wdrożone do [hostingu poza procesem.](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)

1. Upewnij się, że tożsamość modelu procesu ma odpowiednie uprawnienia.

   Jeśli domyślna tożsamość puli aplikacji > **(Tożsamość****modelu procesu)** zostanie zmieniona z **ApplicationPoolIdentity** na inną tożsamość, sprawdź, czy nowa tożsamość ma wymagane uprawnienia dostępu do folderu, bazy danych i innych wymaganych zasobów aplikacji. Na przykład pula aplikacji wymaga dostępu do odczytu i zapisu do folderów, w których aplikacja odczytuje i zapisuje pliki.

**Konfiguracja uwierzytelniania systemu Windows (opcjonalnie)**  
Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

Wdrażanie aplikacji w folderze **ścieżki fizycznej** iIS, który został ustanowiony w sekcji [Tworzenie witryny IIS.](#create-the-iis-site) [Wdrażanie w sieci Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) jest zalecanym mechanizmem wdrażania, ale istnieje kilka opcji przenoszenia aplikacji z folderu *publikowania* projektu do folderu wdrażania systemu hostingowego.

### <a name="web-deploy-with-visual-studio"></a>Wdrażanie w sieci Web za pomocą programu Visual Studio

Zobacz profile publikowania w [programie Visual Studio, aby ASP.NET temat wdrażania aplikacji Core,](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) aby dowiedzieć się, jak utworzyć profil publikowania do użytku z wdrażaniem w sieci Web. Jeśli dostawca hostingu udostępnia profil publikowania lub obsługę tworzenia, pobierz jego profil i zaimportuj go za pomocą okna dialogowego **Publikowania** programu Visual Studio:

![Strona okna dialogowego Publikowania](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Wdrażanie w sieci Web poza programem Visual Studio

[Wdrażanie w sieci Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) może być również używane poza programem Visual Studio z wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Narzędzie wdrażania sieci Web](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternatywy dla wdrażania w sieci Web

Użyj dowolnej z kilku metod, aby przenieść aplikację do systemu hostingowego, takich jak [ręczne kopiowanie, Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)lub [PowerShell](/powershell/).

Aby uzyskać więcej informacji na temat wdrożenia ASP.NET core w usługach IIS, zobacz sekcję [Zasoby wdrażania dla administratorów usług IIS.](#deployment-resources-for-iis-administrators)

## <a name="browse-the-website"></a>Przeglądanie witryny sieci Web

Po wdrożeniu aplikacji w systemie hostingowym należy wysłać żądanie do jednego z publicznych punktów końcowych aplikacji.

W poniższym przykładzie witryna jest powiązana z `www.mysite.com` **nazwą hosta** IIS w **porcie** `80`. Wniosek składa się `http://www.mysite.com`na:

![Przeglądarka Microsoft Edge załadowała stronę startową usługi IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Zablokowane pliki wdrażania

Pliki w folderze wdrażania są zablokowane, gdy aplikacja jest uruchomiona. Zablokowanych plików nie można nadpisywane podczas wdrażania. Aby zwolnić zablokowane pliki we wdrożeniu, zatrzymaj pulę aplikacji przy użyciu **jednego** z następujących metod:

* Użyj wdrażania w `Microsoft.NET.Sdk.Web` sieci Web i odwołania w pliku projektu. Plik *app_offline.htm* jest umieszczany w katalogu głównym katalogu aplikacji sieci web. Gdy plik jest obecny, ASP.NET Moduł podstawowy bezpiecznie zamyka aplikację i obsługuje *plik app_offline.htm* podczas wdrażania. Aby uzyskać więcej informacji, zobacz [ASP.NET odwołanie do konfiguracji modułu rdzenia](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Ręcznie zatrzymaj pulę aplikacji w Menedżerze usług IIS na serwerze.
* Użyj programu PowerShell, aby upuścić *app_offline.htm* (wymaga programu PowerShell 5 lub nowszego):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Ochrona danych

[Stos ASP.NET Core Data Protection](xref:security/data-protection/introduction) jest używany przez kilka ASP.NET core [oprogramowania pośredniczącego,](xref:fundamentals/middleware/index)w tym oprogramowania pośredniczącego używanego w uwierzytelniania. Nawet jeśli interfejsy API ochrony danych nie są wywoływane przez kod użytkownika, ochrona danych powinna być skonfigurowana za pomocą skryptu wdrażania lub kodu użytkownika w celu utworzenia trwałego [magazynu kluczy](xref:security/data-protection/implementation/key-management)kryptograficznych . Jeśli ochrona danych nie jest skonfigurowana, klucze są przechowywane w pamięci i odrzucane po ponownym uruchomieniu aplikacji.

Jeśli pierścień klucza jest przechowywany w pamięci po ponownym uruchomieniu aplikacji:

* Wszystkie tokeny uwierzytelniania oparte na plikach cookie są unieważnione. 
* Użytkownicy muszą zalogować się ponownie przy następnym żądaniu. 
* Nie można już odszyfrować żadnych danych chronionych za pomocą pierścienia kluczy. Może to obejmować [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) i [ASP.NET podstawowe pliki cookie MVC TempData](xref:fundamentals/app-state#tempdata).

Aby skonfigurować ochronę danych w ramach usługi IIS do utrwalania pierścienia kluczy, należy użyć **jednego** z następujących metod:

* **Tworzenie kluczy rejestru ochrony danych**

  Klucze ochrony danych używane przez aplikacje ASP.NET Core są przechowywane w rejestrze zewnętrznym dla aplikacji. Aby utrwalić klucze dla danej aplikacji, utwórz klucze rejestru dla puli aplikacji.

  W przypadku autonomicznych instalacji usług IIS innych niż webfarm [skrypt PowerShell zapewnia ochronę danych](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) może być używany dla każdej puli aplikacji używanej z aplikacją ASP.NET Core. Ten skrypt tworzy klucz rejestru w rejestrze HKLM, który jest dostępny tylko dla konta procesu roboczego puli aplikacji. Klucze są szyfrowane w spoczynku przy użyciu DPAPI z kluczem dla całego komputera.

  W scenariuszach farmy sieci web można skonfigurować aplikację do używania ścieżki UNC do przechowywania pierścienia kluczy ochrony danych. Domyślnie klucze ochrony danych nie są szyfrowane. Upewnij się, że uprawnienia do plików dla udziału sieciowego są ograniczone do konta systemu Windows, pod które działa aplikacja. Certyfikat X509 może służyć do ochrony kluczy w spoczynku. Należy wziąć pod uwagę mechanizm umożliwiający użytkownikom przekazywanie certyfikatów: Umieść certyfikaty w zaufanym magazynie certyfikatów użytkownika i upewnij się, że są one dostępne na wszystkich komputerach, na których działa aplikacja użytkownika. Szczegółowe informacje można [znaleźć w temacie Konfigurowanie ASP.NET podstawowej ochrony danych.](xref:security/data-protection/configuration/overview)

* **Konfigurowanie puli aplikacji usług IIS w celu załadowania profilu użytkownika**

  To ustawienie znajduje się w sekcji **Model procesu** w obszarze **Ustawienia zaawansowane** dla puli aplikacji. Ustaw **załaduj profil użytkownika** na `True`. Po ustawieniu `True`klawiszy są przechowywane w katalogu profilu użytkownika i chronione przy użyciu dpapi z kluczem specyficznym dla konta użytkownika. Klucze są zachowywane w folderze *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys.*

  Należy również [włączyć atrybut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) puli aplikacji. Wartością `setProfileEnvironment` domyślną `true`jest . W niektórych scenariuszach (na przykład `setProfileEnvironment` system `false`operacyjny Windows) jest ustawiona na . Jeśli klucze nie są przechowywane w katalogu profilu użytkownika zgodnie z oczekiwaniami:

  1. Przejdź do folderu *%windir%/system32/inetsrv/config.*
  1. Otwórz plik *applicationHost.config.*
  1. Zlokalizuj `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.
  1. Upewnij się, że `setProfileEnvironment` atrybut nie jest obecny, `true`co domyślnie wartość lub jawnie `true`ustawić wartość atrybutu .

* **Używanie systemu plików jako magazynu dzwonków z kluczami**

  Dostosuj kod aplikacji, aby [używać systemu plików jako magazynu kluczy](xref:security/data-protection/configuration/overview). Użyj certyfikatu X509, aby chronić pierścień kluczy i upewnić się, że certyfikat jest zaufanym certyfikatem. Jeśli certyfikat jest podpisany samodzielnie, umieść go w magazynie Zaufanych Root.

  W przypadku korzystania z usług IIS w farmie internetowej:

  * Użyj udziału plików, do którego mają dostęp wszystkie maszyny.
  * Wdrażanie certyfikatu X509 na każdym komputerze. Skonfiguruj [ochronę danych w kodzie](xref:security/data-protection/configuration/overview).

* **Ustawianie zasad ochrony danych dla całej maszyny**

  System ochrony danych ma ograniczoną obsługę ustawiania domyślnych [zasad dla wszystkich](xref:security/data-protection/configuration/machine-wide-policy) aplikacji, które korzystają z interfejsów API ochrony danych. Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Katalogi wirtualne

[Katalogi wirtualne usług IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) nie są obsługiwane w aplikacjach ASP.NET Core. Aplikacja może być hostowana jako [podnauka](#sub-applications).

## <a name="sub-applications"></a>Podprocesy

Aplikacja ASP.NET Core może być hostowana jako [podnauka usług IIS (podnauka).](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) Ścieżka aplikacji podrzędnej staje się częścią adresu URL aplikacji głównej.

Statyczne łącza zasobów w aplikacji podrzędnej powinny`~/`używać notacji tilde-slash ( ). Notacja o rozszyciu tyldy wyzwala [Pomocnika tagów,](xref:mvc/views/tag-helpers/intro) aby dołączyć do bazy ścieżek aplikacji podrzędnej do renderowanego łącza względnego. W przypadku poddolizmy w `/subapp_path` `src="~/image.png"` punkcie , `src="/subapp_path/image.png"`obraz połączony z jest renderowany jako . Oprogramowanie pośredniczące w pliku statycznym aplikacji głównej nie przetwarza żądania pliku statycznego. Żądanie jest przetwarzane przez oprogramowanie pośredniczące statycznego pliku aplikacji sub-app.

Jeśli atrybut zasobu `src` statycznego jest ustawiony na ścieżkę `src="/image.png"`bezwzględną (na przykład), łącze jest renderowane bez bazy ścieżek aplikacji podrzędnej. Oprogramowanie pośredniczące w aplikacji głównej Statyczne pliki próbuje obsługiwać zasób z katalogu głównego aplikacji [głównej,](xref:fundamentals/index#web-root)co skutkuje *odpowiedzią 404 — nie znaleziono,* chyba że zasób statyczny jest dostępny z aplikacji głównej.

Aby hostować aplikację core ASP.NET jako poddołkę w innej aplikacji ASP.NET Core:

1. Ustanowienie puli aplikacji dla podkoszuli. Ustaw **wersję CLR .NET** na **Brak kodu zarządzanego,** ponieważ podstawowy wspólny język runtime (CoreCLR) dla .NET Core jest uruchamiany do obsługi aplikacji w procesie roboczym, a nie na pulpicie CLR (.NET CLR).

1. Dodaj witrynę główną w Menedżerze usług IIS z aplikacją podrzędną w folderze pod witryną główną.

1. Kliknij prawym przyciskiem myszy folder aplikacji podrzędnej w Menedżerze usług IIS i wybierz polecenie **Konwertuj na aplikację**.

1. W oknie dialogowym **Dodawanie aplikacji** użyj przycisku **Wybierz** dla **puli aplikacji,** aby przypisać pulę aplikacji utworzoną dla aplikacji podrzędnej. Kliknij przycisk **OK**.

Przypisanie oddzielnej puli aplikacji do podkoszuli jest wymagane podczas korzystania z modelu hostingu w trakcie.

Aby uzyskać więcej informacji na temat modelu hostingu w trakcie procesu <xref:host-and-deploy/aspnet-core-module>i konfigurowania ASP.NET Core Module, zobacz .

## <a name="configuration-of-iis-with-webconfig"></a>Konfiguracja usług IIS za pomocą pliku web.config

Na konfigurację usług IIS ma wpływ `<system.webServer>` sekcja *web.config* dla scenariuszy usług IIS, które działają dla aplikacji ASP.NET Core z modułem ASP.NET Core. Na przykład konfiguracja usług IIS działa w przypadku kompresji dynamicznej. Jeśli usługi IIS są skonfigurowane na poziomie `<urlCompression>` serwera do używania kompresji dynamicznej, element w pliku *web.config* aplikacji może go wyłączyć dla aplikacji ASP.NET Core.

Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Odwołanie do \<konfiguracji dla>system.webServer](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Aby ustawić zmienne środowiskowe dla poszczególnych aplikacji działających w pulach aplikacji izolowanych (obsługiwanych dla usług IIS 10.0 lub nowszych), zobacz *sekcję polecenia AppCmd.exe* [środowiska zmiennych środowiska Zmienne \<środowiskoweWarzyny>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) tematu w dokumentacji odwołania usług IIS.

## <a name="configuration-sections-of-webconfig"></a>Sekcje konfiguracji web.config

Sekcje konfiguracji aplikacji ASP.NET 4.x w *witrynie web.config* nie są używane przez aplikacje ASP.NET Core do konfiguracji:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

aplikacje ASP.NET Core są konfigurowane przy użyciu innych dostawców konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Pule aplikacji

Izolacja puli aplikacji jest określana przez model hostingu:

* W trakcie &ndash; hostingu Aplikacje są wymagane do uruchamiania w oddzielnych pulach aplikacji.
* Hosting &ndash; poza procesem Zaleca izolowanie aplikacji od siebie, uruchamiając każdą aplikację we własnej puli aplikacji.

Domyślne okno dialogowe **Dodaj witrynę sieci Web** dla aplikacji iis dla jednej puli aplikacji na aplikację. Po podaniu **nazwy witryny** tekst jest automatycznie przesyłany do pola tekstowego **Puli aplikacji.** Nowa pula aplikacji jest tworzona przy użyciu nazwy witryny po dodaniu witryny.

## <a name="application-pool-identity"></a>Tożsamość puli aplikacji

Konto tożsamości puli aplikacji umożliwia aplikacji uruchamianie na unikatowym koncie bez konieczności tworzenia domen lub kont lokalnych oraz zarządzania nimi. W serwisach IIS 8.0 lub nowszych proces administracyjnego procesu roboczego usług IIS (WAS) tworzy konto wirtualne o nazwie nowej puli aplikacji i domyślnie uruchamia procesy robocze puli aplikacji w ramach tego konta. W konsoli zarządzania usługami IIS w obszarze **Ustawienia zaawansowane** dla puli aplikacji upewnij się, że **tożsamość** jest ustawiona na używanie **applicationpoolidentity:**

![Okno dialogowe Ustawień zaawansowanych puli aplikacji](index/_static/apppool-identity.png)

Proces zarządzania usługami IIS tworzy bezpieczny identyfikator o nazwie puli aplikacji w systemie zabezpieczeń systemu Windows. Zasoby można zabezpieczyć przy użyciu tej tożsamości. Jednak ta tożsamość nie jest rzeczywistym kontem użytkownika i nie jest chylić się w Konsoli zarządzania użytkownikami systemu Windows.

Jeśli proces roboczy IIS wymaga podwyższonego dostępu do aplikacji, zmodyfikuj listę kontroli dostępu (ACL) dla katalogu zawierającego aplikację:

1. Otwórz Eksploratora Windows i przejdź do katalogu.

1. Kliknij prawym przyciskiem myszy katalog i wybierz polecenie **Właściwości**.

1. W obszarze karta **Zabezpieczenia** wybierz przycisk **Edytuj,** a następnie przycisk **Dodaj.**

1. Wybierz przycisk **Lokalizacje** i upewnij się, że system jest zaznaczony.

1. Wprowadź **\\<<app_pool_name<usług IIS>** w obszarze Wprowadź nazwy **obiektów, aby wybrać** obszar. Wybierz przycisk **Sprawdź nazwy.** W przypadku *pola DefaultAppPool* sprawdź nazwy przy użyciu usługi **IIS AppPool\DefaultAppPool**. Po wybraniu przycisku **Sprawdź nazwy** w obszarze nazwy obiektów jest wskazywana wartość **DefaultAppPool.** Nie można wprowadzić nazwy puli aplikacji bezpośrednio w obszarze nazw obiektów. Podczas sprawdzania nazwy obiektu należy używać **\\<<app_pool_name>.**

   ![Wybierz okno dialogowe Użytkownicy lub grupy dla folderu aplikacji: Nazwa puli aplikacji "DefaultAppPool" jest dołączana do "Puli aplikacji usług IIS\" w obszarze nazw obiektów przed wybraniem opcji "Sprawdź nazwy".](index/_static/select-users-or-groups-1.png)

1. Kliknij przycisk **OK**.

   ![Wybierz okno dialogowe Użytkownicy lub grupy dla folderu aplikacji: Po wybraniu opcji "Sprawdź nazwy" nazwa obiektu "DefaultAppPool" jest wyświetlana w obszarze nazw obiektów.](index/_static/select-users-or-groups-2.png)

1. Uprawnienia &amp; do wykonania odczytu powinny być przyznawane domyślnie. W razie potrzeby podaj dodatkowe uprawnienia.

Dostęp można również udzielić w wierszu polecenia za pomocą narzędzia **ICACLS.** Na przykład przy użyciu *domyślnej dupy jako* przykładu używane jest następujące polecenie:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Aby uzyskać więcej informacji, zobacz temat [icacls.](/windows-server/administration/windows-commands/icacls)

## <a name="http2-support"></a>Obsługa protokołu HTTP/2

[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwany przez ASP.NET Core w następujących scenariuszach wdrażania usług IIS:

* W trakcie procesu
  * Windows Server 2016/Windows 10 lub nowsza; IIS 10 lub nowsze
  * Połączenie TLS 1.2 lub nowsze
* Poza procesem
  * Windows Server 2016/Windows 10 lub nowsza; IIS 10 lub nowsze
  * Połączenia serwerów brzegowych wychodzących na publicznej stronie używają protokołu HTTP/2, ale odwrotne połączenie proxy z [serwerem Kestrel](xref:fundamentals/servers/kestrel) używa protokołu HTTP/1.1.
  * Połączenie TLS 1.2 lub nowsze

W przypadku wdrożenia w trakcie po ustanowieniu połączenia HTTP/2, `HTTP/2` [httprequest.protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) zgłasza . W przypadku wdrożenia poza procesem po nawiązaniu połączenia HTTP/2 raporty `HTTP/1.1` [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) .

Aby uzyskać więcej informacji na temat modeli hostingu w <xref:host-and-deploy/aspnet-core-module>trakcie i poza procesem, zobacz .

Protokół HTTP/2 jest domyślnie włączony. Połączenia są przywracane do protokołu HTTP/1.1, jeśli połączenie HTTP/2 nie zostanie nawiązane. Aby uzyskać więcej informacji na temat konfiguracji HTTP/2 z wdrożeniami usług IIS, zobacz [HTTP/2 na usługach IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Żądania inspekcji wstępnej CORS

*Ta sekcja dotyczy tylko aplikacji ASP.NET Core, które są przeznaczone dla programu .NET Framework.*

W przypadku aplikacji ASP.NET Core przeznaczonej dla programu .NET Framework żądania opcji nie są domyślnie przekazywane do aplikacji w uiświadach. Aby dowiedzieć się, jak skonfigurować programy obsługi usług IIS aplikacji w *witrynie web.config* do przekazywania żądań OPCJI, zobacz [Włączanie żądań między źródłami w ASP.NET interfejsu API sieci Web 2: Jak działa cors](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Moduł inicjowania aplikacji i limit czasu bezczynnia

Hostowane w serwisach IIS przez moduł ASP.NET Core w wersji 2:

* Moduł [inicjowania aplikacji](#application-initialization-module) obsługiwany [w procesie](#in-process-hosting-model) lub poza procesem można skonfigurować tak, aby uruchamiał się automatycznie po ponownym uruchomieniu procesu roboczego lub ponownym uruchomieniu serwera. [out-of-process](#out-of-process-hosting-model) &ndash;
* [Idle Timeout](#idle-timeout) &ndash; App hostowane [w procesie](#in-process-hosting-model) można skonfigurować, aby nie limit czasu w okresach braku aktywności.

### <a name="application-initialization-module"></a>Moduł inicjowania aplikacji

*Dotyczy aplikacji hostowanych w procesie i poza procesem.*

[Inicjowanie aplikacji usług IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) to funkcja usług IIS, która wysyła żądanie HTTP do aplikacji po uruchomieniu lub odtworzeniu puli aplikacji. Żądanie wyzwala aplikację do uruchomienia. Domyślnie usługi IIS wystawiają żądanie do`/`głównego adresu URL aplikacji ( ) w celu zainicjowania aplikacji (zobacz [dodatkowe zasoby,](#application-initialization-module-and-idle-timeout-additional-resources) aby uzyskać więcej informacji na temat konfiguracji).

Upewnij się, że funkcja roli inicjowania aplikacji usług IIS jest włączona:

W systemach Windows 7 lub nowszych w systemach stacjonarnych korzystających z usług IIS lokalnie:

1. Przejdź do pozycji **Programy i funkcje** > **Panelu** > **sterowania** > **Włączanie lub wyłączanie funkcji systemu Windows** (po lewej stronie ekranu).
1. Otwórz **funkcje tworzenia aplikacji**sieci Web **w sieci** > **World Web Services** > .
1. Zaznacz pole wyboru **Inicjowania aplikacji**.

W systemie Windows Server 2008 R2 lub nowszym:

1. Otwórz **Kreatora dodawania ról i funkcji**.
1. W panelu **Wybierz usługi roli** otwórz węzeł Tworzenie **aplikacji.**
1. Zaznacz pole wyboru **Inicjowania aplikacji**.

Użyj jednej z następujących metod, aby włączyć moduł inicjowania aplikacji dla lokacji:

* Korzystanie z Menedżera usług IIS:

  1. Wybierz **pule aplikacji** w panelu **Połączenia.**
  1. Kliknij prawym przyciskiem myszy pulę aplikacji na liście i wybierz pozycję **Ustawienia zaawansowane**.
  1. Domyślnym **trybem startowym** jest **OnDemand**. Ustaw **tryb startu** na **AlwaysRunning**. Kliknij przycisk **OK**.
  1. Otwórz węzeł **Lokacje** w panelu **Połączenia.**
  1. Kliknij prawym przyciskiem myszy aplikację i wybierz pozycję **Zarządzaj** > **ustawieniami zaawansowanymi witryny**.
  1. Domyślnym **ustawieniem włączone ładowanie wstępne** jest **Fałsz**. Ustaw **włączone napięcie wstępne na** **true**. Kliknij przycisk **OK**.

* Za pomocą *web.config*, `doAppInitAfterRestart` dodać `true` `<applicationInitialization>` element `<system.webServer>` z zestawem do elementów w pliku *web.config* aplikacji:

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

*Dotyczy tylko aplikacji hostowanych w toku.*

Aby zapobiec pracy na biegu jałowym aplikacji, ustaw limit czasu bezczynność puli aplikacji za pomocą Menedżera usług IIS:

1. Wybierz **pule aplikacji** w panelu **Połączenia.**
1. Kliknij prawym przyciskiem myszy pulę aplikacji na liście i wybierz pozycję **Ustawienia zaawansowane**.
1. Domyślny **limit czasu bezczynnego (minuty)** wynosi **20** minut. Ustaw **limit czasu bezczynnego (minuty)** na **0** (zero). Kliknij przycisk **OK**.
1. Recykling procesu roboczego.

Aby zapobiec przesunieniu limitu czasu przez aplikacje hostowane [poza procesem,](#out-of-process-hosting-model) użyj jednej z następujących metod:

* Ping aplikacji z usługi zewnętrznej w celu utrzymania go w pracy.
* Jeśli aplikacja obsługuje tylko usługi w tle, unikaj hostingu usług IIS i użyj [usługi systemu Windows do hostowania aplikacji ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Moduł inicjowania aplikacji i limit czasu bezczynnia dodatkowe zasoby

* [Inicjowanie aplikacji usług IIS 8.0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Aplikacja Inicjalizacja \<aplikacjiJanitializacja>](/iis/configuration/system.webserver/applicationinitialization/).
* [Ustawienia modelu procesu dla \<procesu puli aplikacjiModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="deployment-resources-for-iis-administrators"></a>Zasoby wdrażania dla administratorów usług IIS

* [Dokumentacja iis](/iis)
* [Wprowadzenie do menedżera usług IIS w usługach IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Wdrożenie aplikacji .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:test/troubleshoot>
* <xref:index>
* [Oficjalna witryna microsoft iis](https://www.iis.net/)
* [Biblioteka zawartości technicznej systemu Windows Server](/windows-server/windows-server)
* [HTTP/2 na usługi IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Aby zapoznać się z samouczkiem dotyczących publikowania aplikacji core <xref:tutorials/publish-to-iis>ASP.NET na serwerze usług IIS, zobacz .

[Zainstaluj pakiet hostingowy .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Obsługiwane są następujące systemy operacyjne:

* Windows 7 lub nowsze
* Windows Server 2008 R2 lub nowszy

[Serwer HTTP.sys](xref:fundamentals/servers/httpsys) (dawniej nazywany WebListener) nie działa w odwrotnej konfiguracji serwera proxy z usługami IIS. Użyj [serwera Kestrel](xref:fundamentals/servers/kestrel).

Aby uzyskać informacje na <xref:host-and-deploy/azure-apps/index>temat hostingu na platformie Azure, zobacz .

Aby uzyskać wskazówki <xref:test/troubleshoot>dotyczące rozwiązywania problemów, zobacz .

## <a name="supported-platforms"></a>Obsługiwane platformy

Obsługiwane są aplikacje opublikowane dla wdrożenia 32-bitowego (x86) lub 64-bitowego (x64). Wdrażanie 32-bitowej aplikacji z 32-bitowym (x86) .NET Core SDK, chyba że aplikacja:

* Wymaga większej przestrzeni adresowej pamięci wirtualnej dostępnej dla aplikacji 64-bitowej.
* Wymaga większego rozmiaru stosu usługi IIS.
* Ma 64-bitowe natywne zależności.

Użyj 64-bitowego (x64) .NET Core SDK, aby opublikować aplikację 64-bitową. 64-bitowy środowiska uruchomieniowego musi być obecny w systemie hosta.

ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), domyślnym, wieloplatformowym serwerem HTTP.

W przypadku korzystania z [usług IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) lub [usług IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikacja jest uruchamiana w procesie odrębnym od procesu roboczego usług IIS *(poza procesem)* z [serwerem Kestrel](xref:fundamentals/servers/index#kestrel).

Ponieważ ASP.NET aplikacje Core są uruchamiane w procesie odrębnym od procesu roboczego usług IIS, moduł obsługuje zarządzanie procesami. Moduł uruchamia proces aplikacji ASP.NET Core po nadejściu pierwszego żądania i uruchamia ponownie aplikację, jeśli zostanie zamknięta lub ulegnie awarii. Jest to zasadniczo to samo zachowanie, co w aplikacjach uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (WAS).](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)

Na poniższym diagramie przedstawiono relację między iis, ASP.NET Core Module i aplikacji hostowane poza procesem:

![Moduł ASP.NET Core](index/_static/ancm-outofprocess.png)

Żądania przychodzą z sieci Web do sterownika HTTP.sys w trybie jądra. Sterownik kieruje żądania do iIS w skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS). Moduł przekazuje żądania do Kestrel na losowym porcie dla aplikacji, który nie jest portem 80 lub 443.

Moduł określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania, a [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguruje serwer do nasłuchiwać na `http://localhost:{port}`. Dodatkowe kontrole są wykonywane, a żądania, które nie pochodzą z modułu są odrzucane. Moduł nie obsługuje przekazywania protokołu HTTPS, więc żądania są przekazywane za pośrednictwem protokołu HTTP, nawet jeśli są odbierane przez usługi IIS za pośrednictwem protokołu HTTPS.

Po Kestrel odbiera żądanie z modułu, żądanie jest wypychane do potoku oprogramowania pośredniczącego ASP.NET Core. Potok oprogramowania pośredniczącego obsługuje żądanie i `HttpContext` przekazuje go jako wystąpienie do logiki aplikacji. Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i bazę ścieżek w celu przekazania żądania do Kestrel. Odpowiedź aplikacji jest przekazywana z powrotem do iIS, co wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.

`CreateDefaultBuilder`konfiguruje serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer www i umożliwia integrację usług IIS, konfigurując ścieżkę bazową i port dla [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).

Moduł ASP.NET Core generuje dynamiczny port do przypisania do procesu wewnętrznej bazy danych. `CreateDefaultBuilder`wywołuje <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> tę metodę. `UseIISIntegration`konfiguruje Kestrel do nasłuchiwać na dynamicznym`127.0.0.1`porcie pod adresem IP hosta lokalnego ( ). Jeśli dynamiczny port wynosi 1234, Kestrel nasłuchuje w `127.0.0.1:1234`. Ta konfiguracja zastępuje inne konfiguracje adresów URL dostarczane przez:

* `UseUrls`
* [Kestrel's Listen API](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Konfiguracja](xref:fundamentals/configuration/index) (lub [opcja wiersza polecenia --urls)](xref:fundamentals/host/web-host#override-configuration)

Wywołania `UseUrls` lub Kestrel `Listen` interfejsu API nie są wymagane podczas korzystania z modułu. Jeśli `UseUrls` `Listen` lub jest wywoływana, Kestrel nasłuchuje na porcie określonym tylko podczas uruchamiania aplikacji bez iIS.

Aby uzyskać ASP.NET wskazówki dotyczące <xref:host-and-deploy/aspnet-core-module>konfiguracji modułu rdzenia, zobacz .

Aby uzyskać więcej informacji na temat hostingu, zobacz [Host w ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Konfiguracja aplikacji

### <a name="enable-the-iisintegration-components"></a>Włączanie składników integracji iis

Podczas tworzenia hosta w `CreateWebHostBuilder` *(Program.cs*), wywołanie, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> aby włączyć integrację usługi IIS:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Aby uzyskać `CreateDefaultBuilder`więcej <xref:fundamentals/host/web-host#set-up-a-host>informacji na temat , zobacz .

### <a name="iis-options"></a>Opcje iis

| Opcja                         | Domyślne | Ustawienie |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Jeśli `true`program IIS `HttpContext.User` Server ustawi uwierzytelnione [przez uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth). Jeśli `false`serwer zapewnia tożsamość `HttpContext.User` i odpowiada na wyzwania tylko wtedy, `AuthenticationScheme`gdy jest to wyraźnie wymagane przez plik . Uwierzytelnianie systemu Windows musi być `AutomaticAuthentication` włączone w iis, aby działały. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Ustawia wyświetlaną nazwę wyświetlaną użytkownikom na stronach logowania. |

Aby skonfigurować opcje usług IIS, <xref:Microsoft.AspNetCore.Builder.IISOptions> <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>należy dołączyć konfigurację usługi w programie . Poniższy przykład zapobiega zapełnianiu `HttpContext.Connection.ClientCertificate`aplikacji:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Opcja                         | Domyślne | Ustawienie |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Jeśli `true`oprogramowanie [pośredniczące integracji iIS](#enable-the-iisintegration-components) ustawia `HttpContext.User` uwierzytelnione przez [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth). Jeśli `false`oprogramowanie pośredniczące zapewnia `HttpContext.User` tylko tożsamość i odpowiada na wyzwania, gdy jest to wyraźnie wymagane przez `AuthenticationScheme`. Uwierzytelnianie systemu Windows musi być `AutomaticAuthentication` włączone w iis, aby działały. Aby uzyskać więcej informacji, zobacz temat [Uwierzytelniania systemu Windows.](xref:security/authentication/windowsauth) |
| `AuthenticationDisplayName`    | `null`  | Ustawia wyświetlaną nazwę wyświetlaną użytkownikom na stronach logowania. |
| `ForwardClientCertificate`     | `true`  | Jeśli `true` nagłówek `MS-ASPNETCORE-CLIENTCERT` żądania jest obecny, jest wypełniany. `HttpContext.Connection.ClientCertificate` |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

[Oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components), które konfiguruje oprogramowanie pośredniczące nagłówków przesyłanych dalej, oraz ASP.NET Core Module są skonfigurowane do przesyłania dalej schematu (HTTP/HTTPS) i zdalnego adresu IP, z którego pochodzi żądanie. Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych za dodatkowymi serwerami proxy i modułami równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>plik web.config

Plik *web.config* konfiguruje [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module). Tworzenie, przekształcanie i publikowanie pliku *web.config* jest obsługiwane przez obiekt`_TransformWebConfig`docelowy MSBuild ( ) po opublikowaniu projektu. Ten cel jest obecny w celach`Microsoft.NET.Sdk.Web`SDK sieci Web ( ). Zestaw SDK znajduje się w górnej części pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Jeśli pliku *web.config* nie ma w projekcie, plik jest tworzony z poprawnym *processPath* i *argumentami* w celu skonfigurowania ASP.NET modułu rdzenia i przeniesiony do [opublikowanego wyjścia](xref:host-and-deploy/directory-structure).

Jeśli plik *web.config* jest obecny w projekcie, plik jest przekształcany za pomocą poprawnego *processPath* i *argumentów,* aby skonfigurować ASP.NET Core Module i przeniesiony do opublikowanych danych wyjściowych. Transformacja nie modyfikuje ustawień konfiguracji usług IIS w pliku.

Plik *web.config* może zapewniać dodatkowe ustawienia konfiguracji usług IIS, które sterują aktywnymi modułami usług IIS. Aby uzyskać informacje na temat modułów usług IIS, które mogą przetwarzać żądania za pomocą aplikacji ASP.NET Core, zobacz temat [modułów usług IIS.](xref:host-and-deploy/iis/modules)

Aby zapobiec przekształcaniu pliku *web.config przez web.config,* należy użyć właściwości ** \<IsTransformWebConfigDisabled>** w pliku projektu:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Podczas wyłączania zestawu Web SDK z przekształcania pliku, *processPath* i *argumenty* powinny być ręcznie ustawione przez dewelopera. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>lokalizacja pliku web.config

Aby poprawnie skonfigurować [moduł ASP.NET Core,](xref:host-and-deploy/aspnet-core-module) plik *web.config* musi znajdować się przy ścieżce [głównej zawartości](xref:fundamentals/index#content-root) (zazwyczaj ścieżki podstawowej aplikacji) wdrożonej aplikacji. Jest to ta sama lokalizacja, co ścieżka fizyczna witryny sieci Web udostępniana usługom IIS. Plik *web.config* jest wymagany w katalogu głównym aplikacji, aby umożliwić publikowanie wielu aplikacji przy użyciu funkcji Web Deploy.

Poufne pliki istnieją na ścieżce fizycznej aplikacji, takie jak * \<zestaw>.runtimeconfig.json*, * \<zestaw>.xml* (komentarze dokumentacji XML) i * \<zestaw>.deps.json*. Gdy plik *web.config* jest obecny, a witryna uruchamia się normalnie, usługi IIS nie obsługują tych poufnych plików, jeśli są wymagane. Jeśli brakuje pliku *web.config,* niepoprawnie nazwany lub nie można skonfigurować witryny do normalnego uruchamiania, usługi IIS mogą obsługiwać poufne pliki publicznie.

**Plik *web.config* musi być obecny we wdrożeniu przez cały czas, poprawnie nazwany i może skonfigurować witrynę do normalnego uruchamiania. Nigdy nie usuwaj pliku *web.config* z wdrożenia produkcyjnego.**

### <a name="transform-webconfig"></a>Przekształcanie pliku web.config

Jeśli chcesz przekształcić *web.config* przy publikowaniu (na przykład ustawić zmienne środowiskowe na <xref:host-and-deploy/iis/transform-webconfig>podstawie konfiguracji, profilu lub środowiska), zobacz .

## <a name="iis-configuration"></a>Konfiguracja usług IIS

**Systemy operacyjne Windows Server**

Włącz rolę serwera **sieci Web (IIS)** i ustal usługi ról.

1. Użyj Kreatora **Dodawania ról i funkcji** z menu **Zarządzanie** lub łącza w **Menedżerze serwera**. W kroku **Role serwera** zaznacz pole wyboru Dla serwera sieci **Web (IIS)**.

   ![Rola usług IIS serwera sieci Web jest zaznaczona w kroku Wybierz role serwera.](index/_static/server-roles-ws2016.png)

1. Po kroku **Funkcje** krok **usług roli** ładuje się dla serwera sieci Web (IIS). Wybierz żądane usługi ról usług IIS lub zaakceptuj domyślne usługi ról.

   ![Domyślne usługi ról są zaznaczone w kroku Wybierz usługi roli.](index/_static/role-services-ws2016.png)

   **Uwierzytelnianie systemu Windows (opcjonalnie)**  
   Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły:**Zabezpieczenia** **serwera** > sieci Web . Wybierz funkcję **uwierzytelniania systemu Windows.** Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie \<systemu Windows>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

   **WebSockets (opcjonalnie)**  
   WebSockets jest obsługiwany z ASP.NET Core 1.1 lub nowszym. Aby włączyć websockets, rozwiń następujące węzły:**Tworzenie aplikacji** **serwera** > sieci Web . Wybierz funkcję **Protokołu WebSocket.** Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).

1. Przejdź przez krok **potwierdzenia,** aby zainstalować rolę i usługi serwera sieci web. Ponowne uruchomienie serwera/usług IIS nie jest wymagane po zainstalowaniu roli **serwera sieci Web (IIS).**

**Systemy operacyjne Windows dla komputerów stacjonarnych**

Włącz **konsolę zarządzania usługami IIS** i **usługi sieci World Wide Web**.

1. Przejdź do pozycji **Programy i funkcje** > **Panelu** > **sterowania** > **Włączanie lub wyłączanie funkcji systemu Windows** (po lewej stronie ekranu).

1. Otwórz węzeł **Internetowe Usługi informacyjne.** Otwórz węzeł **Narzędzia do zarządzania siecią Web.**

1. Zaznacz pole wyboru **Konsola zarządzania usługami IIS**.

1. Zaznacz pole wyboru **Usługi sieci World Wide Web**.

1. Zaakceptuj domyślne funkcje **usług sieci World Wide Web** lub dostosuj funkcje usług IIS.

   **Uwierzytelnianie systemu Windows (opcjonalnie)**  
   Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **Zabezpieczenia usług** > sieci World Wide Web **.** Wybierz funkcję **uwierzytelniania systemu Windows.** Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie \<systemu Windows>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

   **WebSockets (opcjonalnie)**  
   WebSockets jest obsługiwany z ASP.NET Core 1.1 lub nowszym. Aby włączyć websockets, rozwiń następujące węzły:**Funkcje tworzenia aplikacji** **world wide web services** > . Wybierz funkcję **Protokołu WebSocket.** Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).

1. Jeśli instalacja usług IIS wymaga ponownego uruchomienia, uruchom ponownie system.

![W programie Funkcje systemu Windows wybrano konsolę zarządzania usługami IIS i usługi sieci Web World Wide Web.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Zainstaluj pakiet hostingowy .NET Core

Zainstaluj *pakiet hostingowy .NET Core* w systemie hostingowym. Pakiet instaluje środowisko uruchomieniowe .NET Core, bibliotekę .NET Core library i [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module). Moduł umożliwia ASP.NET aplikacje Core działać za usługami IIS.

> [!IMPORTANT]
> Jeśli pakiet hostingowy jest zainstalowany przed usługami IIS, instalacja pakietu musi zostać naprawiona. Uruchom instalator pakietu hostingowego ponownie po zainstalowaniu usługi IIS.
>
> Jeśli pakiet hostingowy jest zainstalowany po zainstalowaniu 64-bitowej (x64) wersji .NET Core, może wydawać się brak zestawów SDK[(nie wykryto żadnych zestawów SDK .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Aby rozwiązać ten <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>problem, zobacz .

### <a name="direct-download-current-version"></a>Pobieranie bezpośrednie (aktualna wersja)

Pobierz instalatora za pomocą następującego linku:

[Aktualny instalator pakietu hostingu .NET Core (pobieranie bezpośrednie)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Starsze wersje instalatora

Aby uzyskać wcześniejszą wersję instalatora:

1. Przejdź do strony [Pobierz .NET Core.](https://dotnet.microsoft.com/download/dotnet-core)
1. Kliknij żądaną wersję .NET Core.
1. W kolumnie **Uruchom aplikacje — środowisko uruchomieniowe** znajdź wiersz żądanej wersji środowiska uruchomieniowego .NET Core.
1. Pobierz instalatora za pomocą łącza **Runtime & Hosting Bundle.**

> [!WARNING]
> Niektóre instalatory zawierają wersje wersji, które osiągnęły swój koniec życia (EOL) i nie są już obsługiwane przez firmę Microsoft. Aby uzyskać więcej informacji, zobacz [zasady pomocy technicznej](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Zainstaluj pakiet hostingowy

1. Uruchom instalator na serwerze. Następujące parametry są dostępne podczas uruchamiania instalatora z powłoki poleceń administratora:

   * `OPT_NO_ANCM=1`&ndash; Pomiń instalację modułu ASP.NET Core.
   * `OPT_NO_RUNTIME=1`&ndash; Pomiń instalowanie środowiska uruchomieniowego .NET Core. Używane, gdy serwer obsługuje tylko [samodzielne wdrożenia (SCD).](/dotnet/core/deploying/#self-contained-deployments-scd)
   * `OPT_NO_SHAREDFX=1`&ndash; Pomiń instalowanie ASP.NET udostępnionej struktury (ASP.NET środowiska uruchomieniowego). Używane, gdy serwer obsługuje tylko [samodzielne wdrożenia (SCD).](/dotnet/core/deploying/#self-contained-deployments-scd)
   * `OPT_NO_X86=1`&ndash; Pomiń instalowanie środowiska wykonawczego x86. Użyj tego parametru, gdy wiesz, że nie będziesz hostować aplikacji 32-bitowych. Jeśli istnieje jakakolwiek szansa, że będziesz hostować zarówno aplikacje 32-bitowe, jak i 64-bitowe w przyszłości, nie używaj tego parametru i instaluj oba środowiska wykonawcze.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`&ndash; Wyłącz sprawdzanie przy użyciu konfiguracji udostępnionej usług IIS, gdy konfiguracja udostępniona *(applicationHost.config)* znajduje się na tym samym komputerze co instalacja usług IIS. *Dostępne tylko dla instalatorów ASP.NET Core 2.2 lub nowszych Hosting Bundler.* Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Uruchom ponownie system lub wykonaj następujące polecenia w powłoce poleceń:

   ```console
   net stop was /y
   net start w3svc
   ```
   Ponowne uruchomienie usługi IIS odbiera zmianę w systemie PATH, która jest zmienną środowiskową, dokonaną przez instalatora.

Podczas instalowania pakietu hostingowego nie jest konieczne ręczne zatrzymywania poszczególnych witryn w serwisach IIS. Hostowane aplikacje (witryny usług IIS) są ponownie uruchamiane po ponownym uruchomieniu usług IIS. Aplikacje uruchamiają się ponownie po otrzymaniu pierwszego żądania, w tym z [modułu inicjowania aplikacji](#application-initialization-module-and-idle-timeout).

ASP.NET Core przyjmuje zachowanie do przekazywania dla wersji poprawek udostępnionych pakietów framework. Po ponownym uruchomieniu aplikacji obsługiwanych przez usługi IIS aplikacje ładują się z najnowszymi wersjami poprawek pakietów, do których istnieją odwołania, gdy otrzymają pierwsze żądanie. Jeśli usługi IIS nie zostaną ponownie uruchomione, aplikacje ponownie uruchomią się ponownie i wykazują zachowanie do przodu, gdy ich procesy robocze są poddane recyklingowi i otrzymują pierwsze żądanie.

> [!NOTE]
> Aby uzyskać informacje na temat konfiguracji udostępnionej usług IIS, zobacz [ASP.NET modułu rdzenia z konfiguracją współużytku iis](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Instalowanie wdrażania w sieci Web podczas publikowania w programie Visual Studio

Podczas wdrażania aplikacji na serwerach z [programem Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)należy zainstalować najnowszą wersję programu Web Deploy na serwerze. Aby zainstalować program Web Deploy, użyj [instalatora platformy sieci Web (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) lub uzyskaj instalator bezpośrednio z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=43717). Preferowaną metodą jest użycie interfejsu WebPI. WebPI oferuje autonomiczną konfigurację i konfigurację dla dostawców hostingu.

## <a name="create-the-iis-site"></a>Tworzenie witryny usługi IIS

1. W systemie hostingowym utwórz folder zawierający opublikowane foldery i pliki aplikacji. W następnym kroku ścieżka folderu jest dostarczana do iIS jako ścieżka fizyczna do aplikacji. Aby uzyskać więcej informacji na temat folderu <xref:host-and-deploy/directory-structure>wdrażania aplikacji i układu plików, zobacz .

1. W Menedżerze usług IIS otwórz węzeł serwera w panelu **Połączenia.** Kliknij prawym przyciskiem myszy folder **Witryny.** Z menu kontekstowego **wybierz polecenie Dodaj witrynę sieci Web.**

1. Podaj **nazwę witryny** i ustaw **ścieżkę fizyczną** do folderu wdrażania aplikacji. Podaj konfigurację **powiązania** i utwórz witrynę sieci Web, wybierając **przycisk OK:**

   ![Podaj nazwę witryny, ścieżkę fizyczną i nazwę hosta w kroku Dodaj witrynę sieci Web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Wiązania wieloznaczne najwyższego`http://*:80/` `http://+:80`poziomu ( i ) **nie** powinny być używane. Powiązania symboli wieloznacznych najwyższego poziomu mogą otworzyć aplikację na luki w zabezpieczeniach. Dotyczy to zarówno silnych, jak i słabych symboli wieloznacznych. Użyj jawnych nazw hostów, a nie symboli wieloznacznych. Powiązanie symboli wieloznacznych `*.mysub.com`poddomeny (na przykład) nie ma tego zagrożenia bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do `*.com`, która jest zagrożona). Zobacz [rfc7230 section-5.4,](https://tools.ietf.org/html/rfc7230#section-5.4) aby uzyskać więcej informacji.

1. W obszarze węzła serwera wybierz pozycję **Pule aplikacji**.

1. Kliknij prawym przyciskiem myszy pulę aplikacji witryny i wybierz polecenie **Ustawienia podstawowe** z menu kontekstowego.

1. W oknie **Edytuj pulę aplikacji** ustaw **wersję .NET CLR** na **Brak kodu zarządzanego:**

   ![Ustaw brak kodu zarządzanego dla wersji CLR .NET.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core działa w oddzielnym procesie i zarządza środowiska wykonawczego. ASP.NET Core nie polega na załadowaniu pulpitu CLR (.NET CLR)&mdash;Core Common Language Runtime (CoreCLR) dla platformy .NET Core jest uruchamiany w celu hostowania aplikacji w procesie roboczym. Ustawienie **wersji .NET CLR** na **Brak kodu zarządzanego** jest opcjonalne, ale zalecane.

1. *ASP.NET Core 2.2 lub nowszym:* W przypadku [samodzielnego wdrożenia](/dotnet/core/deploying/#self-contained-deployments-scd) 64-bitowego (x64), które korzysta z [modelu hostingu w trakcie,](#in-process-hosting-model)wyłącz pulę aplikacji dla procesów 32-bitowych (x86).

   Na pasku bocznym **Akcje** Menedżera usług IIS > **pul aplikacji**wybierz pozycję **Ustaw ustawienia domyślne puli aplikacji** lub Ustawienia **zaawansowane**. Zlokalizuj **włącz aplikacje 32-bitowe** i ustaw wartość na `False`. To ustawienie nie ma wpływu na aplikacje wdrożone do [hostingu poza procesem.](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)

1. Upewnij się, że tożsamość modelu procesu ma odpowiednie uprawnienia.

   Jeśli domyślna tożsamość puli aplikacji > **(Tożsamość****modelu procesu)** zostanie zmieniona z **ApplicationPoolIdentity** na inną tożsamość, sprawdź, czy nowa tożsamość ma wymagane uprawnienia dostępu do folderu, bazy danych i innych wymaganych zasobów aplikacji. Na przykład pula aplikacji wymaga dostępu do odczytu i zapisu do folderów, w których aplikacja odczytuje i zapisuje pliki.

**Konfiguracja uwierzytelniania systemu Windows (opcjonalnie)**  
Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

Wdrażanie aplikacji w folderze **ścieżki fizycznej** iIS, który został ustanowiony w sekcji [Tworzenie witryny IIS.](#create-the-iis-site) [Wdrażanie w sieci Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) jest zalecanym mechanizmem wdrażania, ale istnieje kilka opcji przenoszenia aplikacji z folderu *publikowania* projektu do folderu wdrażania systemu hostingowego.

### <a name="web-deploy-with-visual-studio"></a>Wdrażanie w sieci Web za pomocą programu Visual Studio

Zobacz profile publikowania w [programie Visual Studio, aby ASP.NET temat wdrażania aplikacji Core,](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) aby dowiedzieć się, jak utworzyć profil publikowania do użytku z wdrażaniem w sieci Web. Jeśli dostawca hostingu udostępnia profil publikowania lub obsługę tworzenia, pobierz jego profil i zaimportuj go za pomocą okna dialogowego **Publikowania** programu Visual Studio:

![Strona okna dialogowego Publikowania](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Wdrażanie w sieci Web poza programem Visual Studio

[Wdrażanie w sieci Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) może być również używane poza programem Visual Studio z wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Narzędzie wdrażania sieci Web](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternatywy dla wdrażania w sieci Web

Użyj dowolnej z kilku metod, aby przenieść aplikację do systemu hostingowego, takich jak [ręczne kopiowanie, Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)lub [PowerShell](/powershell/).

Aby uzyskać więcej informacji na temat wdrożenia ASP.NET core w usługach IIS, zobacz sekcję [Zasoby wdrażania dla administratorów usług IIS.](#deployment-resources-for-iis-administrators)

## <a name="browse-the-website"></a>Przeglądanie witryny sieci Web

Po wdrożeniu aplikacji w systemie hostingowym należy wysłać żądanie do jednego z publicznych punktów końcowych aplikacji.

W poniższym przykładzie witryna jest powiązana z `www.mysite.com` **nazwą hosta** IIS w **porcie** `80`. Wniosek składa się `http://www.mysite.com`na:

![Przeglądarka Microsoft Edge załadowała stronę startową usługi IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Zablokowane pliki wdrażania

Pliki w folderze wdrażania są zablokowane, gdy aplikacja jest uruchomiona. Zablokowanych plików nie można nadpisywane podczas wdrażania. Aby zwolnić zablokowane pliki we wdrożeniu, zatrzymaj pulę aplikacji przy użyciu **jednego** z następujących metod:

* Użyj wdrażania w `Microsoft.NET.Sdk.Web` sieci Web i odwołania w pliku projektu. Plik *app_offline.htm* jest umieszczany w katalogu głównym katalogu aplikacji sieci web. Gdy plik jest obecny, ASP.NET Moduł podstawowy bezpiecznie zamyka aplikację i obsługuje *plik app_offline.htm* podczas wdrażania. Aby uzyskać więcej informacji, zobacz [ASP.NET odwołanie do konfiguracji modułu rdzenia](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Ręcznie zatrzymaj pulę aplikacji w Menedżerze usług IIS na serwerze.
* Użyj programu PowerShell, aby upuścić *app_offline.htm* (wymaga programu PowerShell 5 lub nowszego):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Ochrona danych

[Stos ASP.NET Core Data Protection](xref:security/data-protection/introduction) jest używany przez kilka ASP.NET core [oprogramowania pośredniczącego,](xref:fundamentals/middleware/index)w tym oprogramowania pośredniczącego używanego w uwierzytelniania. Nawet jeśli interfejsy API ochrony danych nie są wywoływane przez kod użytkownika, ochrona danych powinna być skonfigurowana za pomocą skryptu wdrażania lub kodu użytkownika w celu utworzenia trwałego [magazynu kluczy](xref:security/data-protection/implementation/key-management)kryptograficznych . Jeśli ochrona danych nie jest skonfigurowana, klucze są przechowywane w pamięci i odrzucane po ponownym uruchomieniu aplikacji.

Jeśli pierścień klucza jest przechowywany w pamięci po ponownym uruchomieniu aplikacji:

* Wszystkie tokeny uwierzytelniania oparte na plikach cookie są unieważnione. 
* Użytkownicy muszą zalogować się ponownie przy następnym żądaniu. 
* Nie można już odszyfrować żadnych danych chronionych za pomocą pierścienia kluczy. Może to obejmować [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) i [ASP.NET podstawowe pliki cookie MVC TempData](xref:fundamentals/app-state#tempdata).

Aby skonfigurować ochronę danych w ramach usługi IIS do utrwalania pierścienia kluczy, należy użyć **jednego** z następujących metod:

* **Tworzenie kluczy rejestru ochrony danych**

  Klucze ochrony danych używane przez aplikacje ASP.NET Core są przechowywane w rejestrze zewnętrznym dla aplikacji. Aby utrwalić klucze dla danej aplikacji, utwórz klucze rejestru dla puli aplikacji.

  W przypadku autonomicznych instalacji usług IIS innych niż webfarm [skrypt PowerShell zapewnia ochronę danych](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) może być używany dla każdej puli aplikacji używanej z aplikacją ASP.NET Core. Ten skrypt tworzy klucz rejestru w rejestrze HKLM, który jest dostępny tylko dla konta procesu roboczego puli aplikacji. Klucze są szyfrowane w spoczynku przy użyciu DPAPI z kluczem dla całego komputera.

  W scenariuszach farmy sieci web można skonfigurować aplikację do używania ścieżki UNC do przechowywania pierścienia kluczy ochrony danych. Domyślnie klucze ochrony danych nie są szyfrowane. Upewnij się, że uprawnienia do plików dla udziału sieciowego są ograniczone do konta systemu Windows, pod które działa aplikacja. Certyfikat X509 może służyć do ochrony kluczy w spoczynku. Należy wziąć pod uwagę mechanizm umożliwiający użytkownikom przekazywanie certyfikatów: Umieść certyfikaty w zaufanym magazynie certyfikatów użytkownika i upewnij się, że są one dostępne na wszystkich komputerach, na których działa aplikacja użytkownika. Szczegółowe informacje można [znaleźć w temacie Konfigurowanie ASP.NET podstawowej ochrony danych.](xref:security/data-protection/configuration/overview)

* **Konfigurowanie puli aplikacji usług IIS w celu załadowania profilu użytkownika**

  To ustawienie znajduje się w sekcji **Model procesu** w obszarze **Ustawienia zaawansowane** dla puli aplikacji. Ustaw **załaduj profil użytkownika** na `True`. Po ustawieniu `True`klawiszy są przechowywane w katalogu profilu użytkownika i chronione przy użyciu dpapi z kluczem specyficznym dla konta użytkownika. Klucze są zachowywane w folderze *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys.*

  Należy również [włączyć atrybut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) puli aplikacji. Wartością `setProfileEnvironment` domyślną `true`jest . W niektórych scenariuszach (na przykład `setProfileEnvironment` system `false`operacyjny Windows) jest ustawiona na . Jeśli klucze nie są przechowywane w katalogu profilu użytkownika zgodnie z oczekiwaniami:

  1. Przejdź do folderu *%windir%/system32/inetsrv/config.*
  1. Otwórz plik *applicationHost.config.*
  1. Zlokalizuj `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.
  1. Upewnij się, że `setProfileEnvironment` atrybut nie jest obecny, `true`co domyślnie wartość lub jawnie `true`ustawić wartość atrybutu .

* **Używanie systemu plików jako magazynu dzwonków z kluczami**

  Dostosuj kod aplikacji, aby [używać systemu plików jako magazynu kluczy](xref:security/data-protection/configuration/overview). Użyj certyfikatu X509, aby chronić pierścień kluczy i upewnić się, że certyfikat jest zaufanym certyfikatem. Jeśli certyfikat jest podpisany samodzielnie, umieść go w magazynie Zaufanych Root.

  W przypadku korzystania z usług IIS w farmie internetowej:

  * Użyj udziału plików, do którego mają dostęp wszystkie maszyny.
  * Wdrażanie certyfikatu X509 na każdym komputerze. Skonfiguruj [ochronę danych w kodzie](xref:security/data-protection/configuration/overview).

* **Ustawianie zasad ochrony danych dla całej maszyny**

  System ochrony danych ma ograniczoną obsługę ustawiania domyślnych [zasad dla wszystkich](xref:security/data-protection/configuration/machine-wide-policy) aplikacji, które korzystają z interfejsów API ochrony danych. Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Katalogi wirtualne

[Katalogi wirtualne usług IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) nie są obsługiwane w aplikacjach ASP.NET Core. Aplikacja może być hostowana jako [podnauka](#sub-applications).

## <a name="sub-applications"></a>Podprocesy

Aplikacja ASP.NET Core może być hostowana jako [podnauka usług IIS (podnauka).](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) Ścieżka aplikacji podrzędnej staje się częścią adresu URL aplikacji głównej.

Podnauka nie powinna zawierać ASP.NET modułu core jako programu obsługi. Jeśli moduł zostanie dodany jako program obsługi w pliku *web.config* aplikacji podkoszowej, podczas próby przeglądania aplikacji podrzędnej zostanie odebrany *błąd serwera wewnętrznego 500.19* odwołujący się do wadliwego pliku konfiguracji.

W poniższym przykładzie pokazano opublikowany plik *web.config* dla poddołty ASP.NET Core:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Podczas hostowania aplikacji podrzędnej non-ASP.NET Core pod aplikacją ASP.NET Core jawnie usuń odziedziczony program obsługi w pliku *web.config* aplikacji podrzędnej:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Statyczne łącza zasobów w aplikacji podrzędnej powinny`~/`używać notacji tilde-slash ( ). Notacja o rozszyciu tyldy wyzwala [Pomocnika tagów,](xref:mvc/views/tag-helpers/intro) aby dołączyć do bazy ścieżek aplikacji podrzędnej do renderowanego łącza względnego. W przypadku poddolizmy w `/subapp_path` `src="~/image.png"` punkcie , `src="/subapp_path/image.png"`obraz połączony z jest renderowany jako . Oprogramowanie pośredniczące w pliku statycznym aplikacji głównej nie przetwarza żądania pliku statycznego. Żądanie jest przetwarzane przez oprogramowanie pośredniczące statycznego pliku aplikacji sub-app.

Jeśli atrybut zasobu `src` statycznego jest ustawiony na ścieżkę `src="/image.png"`bezwzględną (na przykład), łącze jest renderowane bez bazy ścieżek aplikacji podrzędnej. Oprogramowanie pośredniczące w aplikacji głównej Statyczne pliki próbuje obsługiwać zasób z katalogu głównego aplikacji [głównej,](xref:fundamentals/index#web-root)co skutkuje *odpowiedzią 404 — nie znaleziono,* chyba że zasób statyczny jest dostępny z aplikacji głównej.

Aby hostować aplikację core ASP.NET jako poddołkę w innej aplikacji ASP.NET Core:

1. Ustanowienie puli aplikacji dla podkoszuli. Ustaw **wersję CLR .NET** na **Brak kodu zarządzanego,** ponieważ podstawowy wspólny język runtime (CoreCLR) dla .NET Core jest uruchamiany do obsługi aplikacji w procesie roboczym, a nie na pulpicie CLR (.NET CLR).

1. Dodaj witrynę główną w Menedżerze usług IIS z aplikacją podrzędną w folderze pod witryną główną.

1. Kliknij prawym przyciskiem myszy folder aplikacji podrzędnej w Menedżerze usług IIS i wybierz polecenie **Konwertuj na aplikację**.

1. W oknie dialogowym **Dodawanie aplikacji** użyj przycisku **Wybierz** dla **puli aplikacji,** aby przypisać pulę aplikacji utworzoną dla aplikacji podrzędnej. Kliknij przycisk **OK**.

Przypisanie oddzielnej puli aplikacji do podkoszuli jest wymagane podczas korzystania z modelu hostingu w trakcie.

Aby uzyskać więcej informacji na temat modelu hostingu w trakcie procesu <xref:host-and-deploy/aspnet-core-module>i konfigurowania ASP.NET Core Module, zobacz .

## <a name="configuration-of-iis-with-webconfig"></a>Konfiguracja usług IIS za pomocą pliku web.config

Na konfigurację usług IIS ma wpływ `<system.webServer>` sekcja *web.config* dla scenariuszy usług IIS, które działają dla aplikacji ASP.NET Core z modułem ASP.NET Core. Na przykład konfiguracja usług IIS działa w przypadku kompresji dynamicznej. Jeśli usługi IIS są skonfigurowane na poziomie `<urlCompression>` serwera do używania kompresji dynamicznej, element w pliku *web.config* aplikacji może go wyłączyć dla aplikacji ASP.NET Core.

Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Odwołanie do \<konfiguracji dla>system.webServer](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Aby ustawić zmienne środowiskowe dla poszczególnych aplikacji działających w pulach aplikacji izolowanych (obsługiwanych dla usług IIS 10.0 lub nowszych), zobacz *sekcję polecenia AppCmd.exe* [środowiska zmiennych środowiska Zmienne \<środowiskoweWarzyny>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) tematu w dokumentacji odwołania usług IIS.

## <a name="configuration-sections-of-webconfig"></a>Sekcje konfiguracji web.config

Sekcje konfiguracji aplikacji ASP.NET 4.x w *witrynie web.config* nie są używane przez aplikacje ASP.NET Core do konfiguracji:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

aplikacje ASP.NET Core są konfigurowane przy użyciu innych dostawców konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Pule aplikacji

Podczas hostowania wielu witryn sieci Web na serwerze zalecamy izolowanie aplikacji od siebie nawzajem, uruchamiając każdą aplikację we własnej puli aplikacji. Domyślnie w tej konfiguracji jest to okno dialogowe **Dodaj witrynę sieci Web** usług IIS. Po podaniu **nazwy witryny** tekst jest automatycznie przesyłany do pola tekstowego **Puli aplikacji.** Nowa pula aplikacji jest tworzona przy użyciu nazwy witryny po dodaniu witryny.

## <a name="application-pool-identity"></a>Tożsamość puli aplikacji

Konto tożsamości puli aplikacji umożliwia aplikacji uruchamianie na unikatowym koncie bez konieczności tworzenia domen lub kont lokalnych oraz zarządzania nimi. W serwisach IIS 8.0 lub nowszych proces administracyjnego procesu roboczego usług IIS (WAS) tworzy konto wirtualne o nazwie nowej puli aplikacji i domyślnie uruchamia procesy robocze puli aplikacji w ramach tego konta. W konsoli zarządzania usługami IIS w obszarze **Ustawienia zaawansowane** dla puli aplikacji upewnij się, że **tożsamość** jest ustawiona na używanie **applicationpoolidentity:**

![Okno dialogowe Ustawień zaawansowanych puli aplikacji](index/_static/apppool-identity.png)

Proces zarządzania usługami IIS tworzy bezpieczny identyfikator o nazwie puli aplikacji w systemie zabezpieczeń systemu Windows. Zasoby można zabezpieczyć przy użyciu tej tożsamości. Jednak ta tożsamość nie jest rzeczywistym kontem użytkownika i nie jest chylić się w Konsoli zarządzania użytkownikami systemu Windows.

Jeśli proces roboczy IIS wymaga podwyższonego dostępu do aplikacji, zmodyfikuj listę kontroli dostępu (ACL) dla katalogu zawierającego aplikację:

1. Otwórz Eksploratora Windows i przejdź do katalogu.

1. Kliknij prawym przyciskiem myszy katalog i wybierz polecenie **Właściwości**.

1. W obszarze karta **Zabezpieczenia** wybierz przycisk **Edytuj,** a następnie przycisk **Dodaj.**

1. Wybierz przycisk **Lokalizacje** i upewnij się, że system jest zaznaczony.

1. Wprowadź **\\<<app_pool_name<usług IIS>** w obszarze Wprowadź nazwy **obiektów, aby wybrać** obszar. Wybierz przycisk **Sprawdź nazwy.** W przypadku *pola DefaultAppPool* sprawdź nazwy przy użyciu usługi **IIS AppPool\DefaultAppPool**. Po wybraniu przycisku **Sprawdź nazwy** w obszarze nazwy obiektów jest wskazywana wartość **DefaultAppPool.** Nie można wprowadzić nazwy puli aplikacji bezpośrednio w obszarze nazw obiektów. Podczas sprawdzania nazwy obiektu należy używać **\\<<app_pool_name>.**

   ![Wybierz okno dialogowe Użytkownicy lub grupy dla folderu aplikacji: Nazwa puli aplikacji "DefaultAppPool" jest dołączana do "Puli aplikacji usług IIS\" w obszarze nazw obiektów przed wybraniem opcji "Sprawdź nazwy".](index/_static/select-users-or-groups-1.png)

1. Kliknij przycisk **OK**.

   ![Wybierz okno dialogowe Użytkownicy lub grupy dla folderu aplikacji: Po wybraniu opcji "Sprawdź nazwy" nazwa obiektu "DefaultAppPool" jest wyświetlana w obszarze nazw obiektów.](index/_static/select-users-or-groups-2.png)

1. Uprawnienia &amp; do wykonania odczytu powinny być przyznawane domyślnie. W razie potrzeby podaj dodatkowe uprawnienia.

Dostęp można również udzielić w wierszu polecenia za pomocą narzędzia **ICACLS.** Na przykład przy użyciu *domyślnej dupy jako* przykładu używane jest następujące polecenie:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Aby uzyskać więcej informacji, zobacz temat [icacls.](/windows-server/administration/windows-commands/icacls)

## <a name="http2-support"></a>Obsługa protokołu HTTP/2

[Protokół HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwany w przypadku wdrożeń poza procesem, które spełniają następujące wymagania podstawowe:

* Windows Server 2016/Windows 10 lub nowsza; IIS 10 lub nowsze
* Połączenia serwerów brzegowych wychodzących na publicznej stronie używają protokołu HTTP/2, ale odwrotne połączenie proxy z [serwerem Kestrel](xref:fundamentals/servers/kestrel) używa protokołu HTTP/1.1.
* Struktura docelowa: nie dotyczy wdrożeń poza procesem, ponieważ połączenie HTTP/2 jest obsługiwane w całości przez usługi IIS.
* Połączenie TLS 1.2 lub nowsze

Jeśli zostanie nawiązane połączenie HTTP/2, [httpRequest.protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) zgłasza `HTTP/1.1`.

Protokół HTTP/2 jest domyślnie włączony. Połączenia są przywracane do protokołu HTTP/1.1, jeśli połączenie HTTP/2 nie zostanie nawiązane. Aby uzyskać więcej informacji na temat konfiguracji HTTP/2 z wdrożeniami usług IIS, zobacz [HTTP/2 na usługach IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Żądania inspekcji wstępnej CORS

*Ta sekcja dotyczy tylko aplikacji ASP.NET Core, które są przeznaczone dla programu .NET Framework.*

W przypadku aplikacji ASP.NET Core przeznaczonej dla programu .NET Framework żądania opcji nie są domyślnie przekazywane do aplikacji w uiświadach. Aby dowiedzieć się, jak skonfigurować programy obsługi usług IIS aplikacji w *witrynie web.config* do przekazywania żądań OPCJI, zobacz [Włączanie żądań między źródłami w ASP.NET interfejsu API sieci Web 2: Jak działa cors](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="deployment-resources-for-iis-administrators"></a>Zasoby wdrażania dla administratorów usług IIS

* [Dokumentacja iis](/iis)
* [Wprowadzenie do menedżera usług IIS w usługach IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Wdrożenie aplikacji .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:test/troubleshoot>
* <xref:index>
* [Oficjalna witryna microsoft iis](https://www.iis.net/)
* [Biblioteka zawartości technicznej systemu Windows Server](/windows-server/windows-server)
* [HTTP/2 na usługi IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
