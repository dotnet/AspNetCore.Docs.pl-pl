---
title: ASP.NET Core hosta w systemie Windows z usługami IIS
author: rick-anderson
description: Dowiedz się, jak hostować ASP.NET Core aplikacje w systemie Windows Server Internet Information Services (IIS).
monikerRange: '>= aspnetcore-2.1'
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
uid: host-and-deploy/iis/index
ms.openlocfilehash: 1338ecd6205a351b54a58522b1713a6d70e2d22a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634751"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>ASP.NET Core hosta w systemie Windows z usługami IIS

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

Aby zapoznać się z samouczkiem dotyczącym publikowania aplikacji ASP.NET Core na serwerze usług IIS, zobacz <xref:tutorials/publish-to-iis> .

[Zainstaluj pakiet hostingu platformy .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Obsługiwane są następujące systemy operacyjne:

* System Windows 7 lub nowszy
* System Windows Server 2012 R2 lub nowszy

[ SerwerHTTP.sys](xref:fundamentals/servers/httpsys) (znany wcześniej jako webListener) nie działa w konfiguracji zwrotnego serwera proxy z usługami IIS. Użyj [serwera Kestrel](xref:fundamentals/servers/kestrel).

Aby uzyskać informacje na temat hostingu na platformie Azure, zobacz <xref:host-and-deploy/azure-apps/index> .

Aby uzyskać wskazówki dotyczące rozwiązywania problemów, zobacz <xref:test/troubleshoot> .

## <a name="supported-platforms"></a>Obsługiwane platformy

Obsługiwane są aplikacje opublikowane dla wdrożenia 32-bitowego (x86) lub 64-bitowego (x64). Wdróż aplikację 32-bitową z 32-bitową (x86) zestaw .NET Core SDK, chyba że aplikacja:

* Wymagana jest większa przestrzeń adresów pamięci wirtualnej dla aplikacji 64-bitowej.
* Wymaga większego rozmiaru stosu IIS.
* Ma 64-bitowe zależności natywne.

Aplikacje opublikowane dla 32-bitowej (x86) muszą mieć włączone 32-bitowe dla ich pul aplikacji usług IIS. Aby uzyskać więcej informacji, zobacz sekcję [Tworzenie witryny usług IIS](#create-the-iis-site) .

Aby opublikować aplikację 64-bitową, należy użyć 64-bitowej (x64) zestaw .NET Core SDK. 64-bitowy środowisko uruchomieniowe musi być obecny w systemie hosta.

## <a name="hosting-models"></a>Modele hostingu

### <a name="in-process-hosting-model"></a>Model hostingu w procesie

Korzystając z hostingu w procesie, aplikacja ASP.NET Core jest uruchamiana w tym samym procesie co proces roboczy usług IIS. Hosting w procesie zapewnia lepszą wydajność w porównaniu z obsługą hostingu, ponieważ żądania nie są kierowane do serwera proxy za pośrednictwem karty sprzężenia zwrotnego, czyli interfejsu sieciowego, który zwraca wychodzący ruch sieciowy z powrotem do tego samego komputera. Usługi IIS obsługują zarządzanie procesami przy użyciu [usługi aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module):

* Wykonuje inicjalizację aplikacji.
  * Ładuje [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Wywołania `Program.Main` .
* Obsługuje okres istnienia żądania natywnego usług IIS.

Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowaną w procesie aplikacją:

![Moduł ASP.NET Core w scenariuszu hostingu w procesie](index/_static/ancm-inprocess.png)

1. Żądanie dociera do sieci Web do sterownika HTTP.sys trybu jądra.
1. Sterownik kieruje natywne żądanie do usług IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS).
1. Moduł ASP.NET Core odbiera żądanie natywne i przekazuje go do serwera HTTP usług IIS ( `IISHttpServer` ). Serwer HTTP IIS jest implementacją serwera w procesie dla usług IIS, która konwertuje żądanie z natywnego na zarządzane.

Po przetworzeniu żądania przez serwer HTTP IIS:

1. Żądanie jest wysyłane do ASP.NET Core potoku oprogramowania pośredniczącego.
1. Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji.
1. Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS za pośrednictwem serwera HTTP IIS.
1. Program IIS wysyła odpowiedź do klienta, który zainicjował żądanie.

Hosting w procesie jest zgodą na istniejące aplikacje. Szablony sieci Web ASP.NET Core korzystają z modelu hostingu w procesie.

`CreateDefaultBuilder` dodaje <xref:Microsoft.AspNetCore.Hosting.Server.IServer> wystąpienie przez wywołanie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> metody w celu uruchomienia [CoreCLR](/dotnet/standard/glossary#coreclr) i hostowania aplikacji w procesie roboczym usług IIS (*w3wp.exe* lub *iisexpress.exe*). Testy wydajności wskazują, że hostowanie aplikacji platformy .NET Core w procesie zapewnia znacznie wyższą przepływność żądań w porównaniu z obsługą żądań proxy poza procesem i [Kestrel](xref:fundamentals/servers/kestrel).

Aplikacje publikowane jako pojedynczy plik wykonywalny nie mogą zostać załadowane przez model hostingu w procesie.

### <a name="out-of-process-hosting-model"></a>Model hostingu poza procesem

Ponieważ ASP.NET Core aplikacje działają w procesie innym niż proces roboczy usług IIS, moduł ASP.NET Core obsługuje zarządzanie procesami. Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli zostanie zamknięty lub ulegnie awarii. Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowanym przez aplikację aplikacją:

![Moduł ASP.NET Core w scenariuszu hostingu poza procesem](index/_static/ancm-outofprocess.png)

1. Żądania docierają z sieci Web do sterownika HTTP.sys trybu jądra.
1. Sterownik kieruje żądania do usług IIS na skonfigurowanym porcie witryny sieci Web. Skonfigurowany port jest zwykle 80 (HTTP) lub 443 (HTTPS).
1. Moduł przekazuje żądania do Kestrel na losowo wybranym porcie dla aplikacji. Port losowy nie jest 80 lub 443.

<!-- make this a bullet list -->
Moduł ASP.NET Core określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>Rozszerzenie konfiguruje serwer do nasłuchiwania `http://localhost:{PORT}` . Dodatkowe sprawdzenia są wykonywane, a żądania, które nie pochodzą z modułu, są odrzucane. Moduł nie obsługuje przekazywania HTTPS. Żądania są przekazywane przez protokół HTTP nawet wtedy, gdy są odbierane przez usługi IIS przez protokół HTTPS.

Po poKestrel przez program żądania z modułu żądanie zostanie przekazane do ASP.NET Core potoku programu pośredniczącego. Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji. Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i pathbase, aby można było przesłać żądanie do Kestrel. Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS, która przekazuje ją z powrotem do klienta HTTP, który zainicjował żądanie.

Aby uzyskać wskazówki dotyczące konfiguracji modułu ASP.NET Core, zobacz <xref:host-and-deploy/aspnet-core-module> .

Aby uzyskać więcej informacji na temat hostingu, zobacz [host in ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Konfiguracja aplikacji

### <a name="enable-the-iisintegration-components"></a>Włącz składniki IISIntegration

Podczas kompilowania hosta w `CreateHostBuilder` (*program.cs*), wywołaj, <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> Aby włączyć integrację usług IIS:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

Aby uzyskać więcej informacji na temat `CreateDefaultBuilder` , zobacz <xref:fundamentals/host/generic-host#default-builder-settings> .

### <a name="iis-options"></a>Opcje usług IIS

**Model hostingu w procesie**

Aby skonfigurować opcje serwera usług IIS, należy uwzględnić <xref:Microsoft.AspNetCore.Builder.IISServerOptions> w programie konfigurację usługi <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> . Poniższy przykład wyłącza AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Opcja                         | Domyślny | Ustawienie |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Jeśli `true` serwer usług IIS ustawi uwierzytelnienie `HttpContext.User` za pomocą [uwierzytelniania systemu Windows](xref:security/authentication/windowsauth). Jeśli `false` serwer zawiera tylko tożsamość dla `HttpContext.User` i reaguje na wyzwania, gdy zostanie jawnie zlecony przez `AuthenticationScheme` . Aby program mógł działać, należy włączyć uwierzytelnianie systemu Windows `AutomaticAuthentication` . Aby uzyskać więcej informacji, zobacz [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Ustawia nazwę wyświetlaną pokazywaną użytkownikom na stronach logowania. |
| `AllowSynchronousIO`           | `false` | Czy synchroniczna operacja we/wy jest dozwolona dla `HttpContext.Request` i `HttpContext.Response` . |
| `MaxRequestBodySize`           | `30000000`  | Pobiera lub ustawia maksymalny rozmiar treści żądania dla `HttpRequest` . Należy zauważyć, że same usługi IIS mają limit, `maxAllowedContentLength` który zostanie przetworzony przed `MaxRequestBodySize` zestawem w `IISServerOptions` . Zmiana `MaxRequestBodySize` nie wpłynie na `maxAllowedContentLength` . Aby zwiększyć `maxAllowedContentLength` , Dodaj wpis w *web.config* , aby ustawić `maxAllowedContentLength` wyższą wartość. Aby uzyskać więcej informacji, zobacz [Konfiguracja](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

**Model hostingu poza procesem**

Aby skonfigurować opcje usług IIS, należy uwzględnić <xref:Microsoft.AspNetCore.Builder.IISOptions> w programie konfigurację usługi <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> . Poniższy przykład uniemożliwia wypełnianie aplikacji `HttpContext.Connection.ClientCertificate` :

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Opcja                         | Domyślny | Ustawienie |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Jeśli `true` [oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components) ustawi uwierzytelnienie `HttpContext.User` za pomocą [uwierzytelniania systemu Windows](xref:security/authentication/windowsauth). Jeśli `false` oprogramowanie pośredniczące zapewnia tylko tożsamość `HttpContext.User` i reaguje na wyzwania, gdy zostanie jawnie zażądana przez program `AuthenticationScheme` . Aby program mógł działać, należy włączyć uwierzytelnianie systemu Windows `AutomaticAuthentication` . Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth) . |
| `AuthenticationDisplayName`    | `null`  | Ustawia nazwę wyświetlaną pokazywaną użytkownikom na stronach logowania. |
| `ForwardClientCertificate`     | `true`  | Jeśli `true` `MS-ASPNETCORE-CLIENTCERT` jest obecny nagłówek żądania, `HttpContext.Connection.ClientCertificate` zostanie wypełniony. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

[Oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components) i moduł ASP.NET Core są skonfigurowane do przesyłania dalej:

* Schemat (HTTP/HTTPS).
* Zdalny adres IP, z którego pochodzi żądanie.

[Oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components) konfiguruje przekazane nagłówki pośredniczące.

W przypadku aplikacji hostowanych za dodatkowymi serwerami proxy i modułami równoważenia obciążenia może być wymagana dodatkowa konfiguracja. Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>Plik web.config

Plik *web.config* konfiguruje [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Tworzenie, przekształcanie i publikowanie pliku *web.config* jest obsługiwane przez obiekt docelowy MSBuild ( `_TransformWebConfig` ), gdy projekt jest publikowany. Ten element docelowy znajduje się w obiektach docelowych zestawu SDK sieci Web ( `Microsoft.NET.Sdk.Web` ). Zestaw SDK jest ustawiany u góry pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Jeśli plik *web.config* nie jest obecny w projekcie, plik zostanie utworzony z prawidłowymi *argumentami* *ProcessPath* i, aby skonfigurować moduł ASP.NET Core i przeniesiony do [publikowanych danych wyjściowych](xref:host-and-deploy/directory-structure).

Jeśli plik *web.config* jest obecny w projekcie, plik zostanie przekształcony przy użyciu poprawnych *processPath* i *argumentów* w celu skonfigurowania modułu ASP.NET Core i przeniesiona do publikowanych danych wyjściowych. Transformacja nie modyfikuje ustawień konfiguracji usług IIS w pliku.

Plik *web.config* może zapewnić dodatkowe ustawienia konfiguracji usług IIS kontrolujące aktywne moduły usług IIS. Aby uzyskać informacje na temat modułów usług IIS, które mogą przetwarzać żądania z aplikacjami ASP.NET Core, zobacz temat [moduły usług IIS](xref:host-and-deploy/iis/modules) .

Aby zapobiec transformacji pliku *web.config* przez zestaw SDK sieci Web, należy użyć **\<IsTransformWebConfigDisabled>** właściwości w pliku projektu:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Podczas wyłączania pliku zestawu SDK sieci Web, *processPath* i *argumenty* powinny być ustawiane ręcznie przez dewelopera. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>Lokalizacja pliku web.config

W celu poprawnego skonfigurowania [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) plik *web.config* musi znajdować się w ścieżce [katalogu głównego zawartości](xref:fundamentals/index#content-root) (zazwyczaj ścieżka podstawowa aplikacji) wdrożonej aplikacji. Ta sama lokalizacja jest taka sama jak ścieżka fizyczna witryny sieci Web dostarczana do usług IIS. Plik *web.config* jest wymagany w katalogu głównym aplikacji, aby umożliwić Publikowanie wielu aplikacji przy użyciu Web Deploy.

Poufne pliki znajdują się w ścieżce fizycznej aplikacji, na przykład * \<assembly>.runtimeconfig.jsna*, * \<assembly> . XML* (Komentarze dokumentacji XML) i * \<assembly>.deps.js*. Gdy plik *web.config* jest obecny, a lokacja jest uruchamiana normalnie, usługi IIS nie będą obsługiwały tych poufnych plików, jeśli są żądane. Jeśli brakuje pliku *web.config* , nazwa jest nieprawidłowa lub nie można skonfigurować lokacji do normalnego uruchamiania, usługi IIS mogą publicznie udostępniać poufne pliki.

**Plik *web.config* musi być obecny we wdrożeniu przez cały czas, prawidłowo nazwany i można skonfigurować lokację pod kątem normalnego uruchamiania. Nigdy nie usuwaj pliku *web.config* z wdrożenia produkcyjnego.**

### <a name="transform-webconfig"></a>Przekształcanie pliku web.config

Jeśli musisz przekształcić *web.config* przy publikowaniu, zobacz <xref:host-and-deploy/iis/transform-webconfig> . Może być konieczne przekształcenie *web.config* przy publikowaniu w celu ustawienia zmiennych środowiskowych na podstawie konfiguracji, profilu lub środowiska.

## <a name="iis-configuration"></a>Konfiguracja usług IIS

**Systemy operacyjne Windows Server**

Włącz rolę serwera **serwera sieci Web (IIS)** i Ustanów usługi ról.

1. Użyj kreatora **dodawania ról i funkcji** z menu **Zarządzaj** lub łącza w **Menedżer serwera**. W kroku **role serwera** zaznacz pole wyboru **serwer sieci Web (IIS)**.

   ![W kroku wybierz role serwera zostanie wybrana rola IIS serwera sieci Web.](index/_static/server-roles-ws2016.png)

1. Po wykonaniu **kroków krok po kroku** **usługi ról** są ładowane dla serwera sieci Web (IIS). Wybierz żądane usługi roli IIS lub zaakceptuj domyślne usługi ról.

   ![Domyślne usługi ról są wybrane w kroku Wybierz usługi ról.](index/_static/role-services-ws2016.png)

   **Uwierzytelnianie systemu Windows (opcjonalnie)**  
   Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **Zabezpieczenia serwera sieci Web**  >  **Security**. Wybierz funkcję **uwierzytelniania systemu Windows** . Aby uzyskać więcej informacji, zobacz [uwierzytelnianie \<windowsAuthentication> systemu Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

   **Obiekty WebSockets (opcjonalnie)**  
   Obiekty WebSockets są obsługiwane w ASP.NET Core 1,1 lub nowszych. Aby włączyć obiekty WebSockets, rozwiń następujące węzły: projektowanie aplikacji **serwera sieci Web**  >  **Application Development**. Wybierz funkcję **protokołu WebSocket** . Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).

1. Przejdź do kroku **potwierdzenia** , aby zainstalować rolę i usługi serwera sieci Web. Po zainstalowaniu roli **serwera sieci Web (IIS)** nie jest wymagane ponowne uruchomienie serwera ani usług IIS.

**Systemy operacyjne Windows dla komputerów stacjonarnych**

Włącz **konsolę zarządzania usług IIS** i **usługi World Wide Web**.

1. Przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).

1. Otwórz węzeł **Internet Information Services** . Otwórz węzeł **Narzędzia do zarządzania siecią Web** .

1. Zaznacz pole wyboru w obszarze **Konsola zarządzania usługami IIS**.

1. Zaznacz pole wyboru **usług World Wide Web Services**.

1. Zaakceptuj domyślne funkcje **usług World Wide Web Services** lub Dostosuj funkcje usług IIS.

   **Uwierzytelnianie systemu Windows (opcjonalnie)**  
   Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **World Wide Web usług**  >  **zabezpieczenia**. Wybierz funkcję **uwierzytelniania systemu Windows** . Aby uzyskać więcej informacji, zobacz [uwierzytelnianie \<windowsAuthentication> systemu Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

   **Obiekty WebSockets (opcjonalnie)**  
   Obiekty WebSockets są obsługiwane w ASP.NET Core 1,1 lub nowszych. Aby włączyć obiekty WebSockets, rozwiń następujące węzły: **World Wide Web Services**  >  **Application Development Features**. Wybierz funkcję **protokołu WebSocket** . Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).

1. Jeśli instalacja usług IIS wymaga ponownego uruchomienia, należy ponownie uruchomić system.

![Konsola zarządzania usługami IIS i usługi World Wide Web są wybrane w funkcjach systemu Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Zainstaluj pakiet hostingu platformy .NET Core

Zainstaluj *pakiet hostingu platformy .NET Core* w systemie hostingu. Pakiet instaluje środowisko uruchomieniowe programu .NET Core, bibliotekę .NET Core i [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Moduł umożliwia uruchamianie aplikacji ASP.NET Core za pomocą usług IIS.

> [!IMPORTANT]
> Jeśli pakiet hostingu jest zainstalowany przed usługami IIS, należy naprawić pakiet instalacyjny. Uruchom ponownie Instalatora pakietu hostingu po zainstalowaniu usług IIS.
>
> Jeśli pakiet hostingu jest instalowany po zainstalowaniu 64-bitowej (x64) wersji programu .NET Core, prawdopodobnie brakuje zestawów SDK ([nie wykryto żadnych zestawów SDK dla platformy .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Aby rozwiązać ten problem, zobacz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> .

### <a name="direct-download-current-version"></a>Pobieranie bezpośrednie (aktualna wersja)

Pobierz instalatora przy użyciu następującego linku:

[Bieżący Instalator pakietu hostingu platformy .NET Core (Pobieranie bezpośrednie)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Starsze wersje Instalatora

Aby uzyskać wcześniejszą wersję Instalatora:

1. Przejdź do strony [pobieranie platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .
1. Wybierz żądaną wersję programu .NET Core.
1. W kolumnie **Uruchom aplikacje — środowisko uruchomieniowe** Znajdź wiersz żądanej wersji środowiska uruchomieniowego platformy .NET Core.
1. Pobierz instalatora przy użyciu linku do **pakietu hostingu** .

> [!WARNING]
> Niektóre Instalatory zawierają wersje wydania, które osiągnęły koniec cyklu życia (EOL) i nie są już obsługiwane przez firmę Microsoft. Aby uzyskać więcej informacji, zobacz [zasady pomocy technicznej](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Zainstaluj pakiet hostingu

1. Uruchom Instalatora na serwerze. Następujące parametry są dostępne podczas uruchamiania Instalatora z powłoki poleceń administratora:

   * `OPT_NO_ANCM=1`: Pomiń Instalowanie modułu ASP.NET Core.
   * `OPT_NO_RUNTIME=1`: Pomiń Instalowanie środowiska uruchomieniowego platformy .NET Core. Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1`: Pomiń instalację ASP.NET Shared Framework (ASP.NET Runtime). Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1`: Pomiń Instalowanie środowiska uruchomieniowego x86. Użyj tego parametru, Jeśli wiesz, że nie będziesz hostować aplikacji 32-bitowych. Jeśli w przyszłości będziesz hostować zarówno aplikacje 32-bitowe, jak i 64-bitowe, nie używaj tego parametru i zainstaluj oba środowiska uruchomieniowe.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: Wyłącz sprawdzanie przy użyciu konfiguracji udostępnionej usług IIS, gdy konfiguracja udostępniona (*applicationHost.config*) znajduje się na tym samym komputerze, na którym zainstalowano program IIS. *Dostępne tylko w przypadku ASP.NET Core 2,2 lub nowszych instalatorów pakietu do obsługi.* Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Uruchom ponownie system lub wykonaj następujące polecenia w powłoce poleceń:

   ```console
   net stop was /y
   net start w3svc
   ```
   Ponowne uruchomienie usług IIS powoduje zmianę ścieżki systemowej, która jest zmienną środowiskową, wykonaną przez Instalatora.

ASP.NET Core nie przyjmuje zachowania z przekazaniem do przodu dla wersji poprawki współużytkowanych pakietów platformy. Po uaktualnieniu udostępnionej platformy, instalując nowy pakiet hostingu, ponownie uruchom system lub wykonaj następujące polecenia w powłoce poleceń:

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> Aby uzyskać informacje na temat konfiguracji udostępnionej usług IIS, zobacz [ASP.NET Core Module z udostępnioną konfiguracją usług IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Zainstaluj Web Deploy przy publikowaniu w programie Visual Studio

Podczas wdrażania aplikacji na serwerach z [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)Zainstaluj najnowszą wersję Web Deploy na serwerze. Aby zainstalować Web Deploy, należy użyć [Instalatora platformy sieci Web (Instalatora WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) lub uzyskać instalatora bezpośrednio z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=43717). Preferowaną metodą jest użycie Instalatora WebPI. Instalatora WebPI oferuje autonomiczną konfigurację i konfigurację dla dostawców hostingu.

## <a name="create-the-iis-site"></a>Tworzenie witryny usług IIS

1. W systemie hostingu Utwórz folder, który będzie zawierać opublikowane foldery i pliki aplikacji. W poniższym kroku ścieżka folderu jest dostarczana do usług IIS jako ścieżka fizyczna do aplikacji. Aby uzyskać więcej informacji na temat folderu wdrożenia aplikacji i układu pliku, zobacz <xref:host-and-deploy/directory-structure> .

1. W Menedżerze usług IIS Otwórz węzeł serwera w panelu **połączenia** . Kliknij prawym przyciskiem myszy folder **sitess** . Wybierz pozycję **Dodaj witrynę internetową** z menu kontekstowego.

1. Podaj **nazwę lokacji** i ustaw **ścieżkę fizyczną** folderu wdrożenia aplikacji. Podaj konfigurację **powiązania** i Utwórz witrynę sieci Web, wybierając **przycisk OK**:

   ![Podaj nazwę lokacji, ścieżkę fizyczną i nazwę hosta w kroku Dodaj witrynę sieci Web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > `http://*:80/` `http://+:80` **Nie** należy używać powiązań z symbolami wieloznacznymi (i) najwyższego poziomu. Powiązania z symbolami wieloznacznymi najwyższego poziomu mogą otwierać aplikację pod kątem luk w zabezpieczeniach. Dotyczy to zarówno silnych, jak i słabych symboli wieloznacznych. Używaj jawnych nazw hostów zamiast symboli wieloznacznych. Powiązanie symboli wieloznacznych z poddomeną (na przykład `*.mysub.com` ) nie ma tego ryzyka bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do `*.com` , która jest narażona). Aby uzyskać więcej informacji, zobacz [sekcję rfc7230-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .

1. W węźle serwera wybierz pozycję **Pule aplikacji**.

1. Kliknij prawym przyciskiem myszy pulę aplikacji lokacji i wybierz pozycję **Ustawienia podstawowe** z menu kontekstowego.

1. W oknie **Edytowanie puli aplikacji** Ustaw **wersję środowiska .NET CLR** na **Brak kodu zarządzanego**:

   ![Nie ustawiaj kodu zarządzanego dla wersji środowiska .NET CLR.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core działa w osobnym procesie i zarządza środowiskiem uruchomieniowym. ASP.NET Core nie polega na ładowaniu programu Desktop CLR (.NET CLR). Podstawowe środowisko uruchomieniowe języka wspólnego (CoreCLR) dla platformy .NET Core jest uruchamiane w celu hostowania aplikacji w procesie roboczym. Ustawienie **wersji środowiska .NET CLR** na **Brak kodu zarządzanego** jest opcjonalne, ale zalecane.

1. *ASP.NET Core 2,2 lub nowszy*:

   * W przypadku 32-bitowego (x86) [wdrożenia](/dotnet/core/deploying/#self-contained-deployments-scd) opublikowanego z 32-bitowym zestawem SDK, który korzysta z [modelu hostingu w procesie](#in-process-hosting-model), należy włączyć pulę aplikacji dla 32-bitowego. W Menedżerze usług IIS przejdź do **pul aplikacji** na pasku bocznym **połączenia** . Wybierz pulę aplikacji aplikacji. Na pasku bocznym **Akcje** wybierz pozycję **Ustawienia zaawansowane**. Ustaw dla opcji **Włącz aplikacje 32-bitowe** `True` . 

   * W przypadku [wdrożenia](/dotnet/core/deploying/#self-contained-deployments-scd) z systemem 64-bitowym (x64), które używa [modelu hostingu w procesie](#in-process-hosting-model), wyłącz pulę aplikacji dla procesów 32-bitowych (x86). W Menedżerze usług IIS przejdź do **pul aplikacji** na pasku bocznym **połączenia** . Wybierz pulę aplikacji aplikacji. Na pasku bocznym **Akcje** wybierz pozycję **Ustawienia zaawansowane**. Ustaw dla opcji **Włącz aplikacje 32-bitowe** `False` . 

1. Potwierdź, że tożsamość modelu procesu ma odpowiednie uprawnienia.

   Jeśli domyślna tożsamość puli aplikacji (**model procesów**  >  **Identity** ) została zmieniona z **ApplicationPool Identity ** na inną tożsamość, sprawdź, czy Nowa tożsamość ma wymagane uprawnienia dostępu do folderu, bazy danych i innych wymaganych zasobów aplikacji. Na przykład Pula aplikacji wymaga dostępu do odczytu i zapisu do folderów, w których aplikacja odczytuje i zapisuje pliki.

**Konfiguracja uwierzytelniania systemu Windows (opcjonalnie)**  
Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

Wdróż aplikację w folderze **ścieżki fizycznej** usług IIS, który został ustanowiony w sekcji [Tworzenie witryny usług IIS](#create-the-iis-site) . [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) jest zalecanym mechanizmem wdrażania, ale istnieje kilka opcji przeniesienia aplikacji z folderu *publikowania* projektu do folderu wdrożenia systemu hostingu.

### <a name="web-deploy-with-visual-studio"></a>Web Deploy z programem Visual Studio

Aby dowiedzieć się, jak utworzyć profil publikowania do użytku z usługą Web Deploy, zobacz temat [Profile publikacji programu Visual Studio dla wdrożenia aplikacji ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) . Jeśli dostawca hostingu udostępnia profil publikowania lub obsługę tworzenia aplikacji, pobierz swój profil i zaimportuj go za pomocą okna dialogowego **publikowania** programu Visual Studio:

![Strona okna dialogowego publikowanie](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Web Deploy poza programem Visual Studio

[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) można również użyć poza programem Visual Studio z wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Narzędzie do wdrażania w sieci Web](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternatywy dla Web Deploy

Użyj dowolnej z kilku metod, aby przenieść aplikację do systemu hostingu, takich jak ręczna kopia, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)lub [PowerShell](/powershell/).

Aby uzyskać więcej informacji na ASP.NET Core wdrażania usług IIS, zobacz sekcję [zasoby wdrażania dla administratorów usług IIS](#deployment-resources-for-iis-administrators) .

## <a name="browse-the-website"></a>Przeglądanie witryny sieci Web

Po wdrożeniu aplikacji w systemie hostingu należy wysłać żądanie do jednej z publicznych punktów końcowych aplikacji.

W poniższym przykładzie lokacja jest powiązana z **nazwą hosta** usług IIS `www.mysite.com` na **porcie** `80` . Zostanie wysłane żądanie `http://www.mysite.com` :

![Przeglądarka Microsoft Edge załadowała stronę startową usług IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Zablokowane pliki wdrożenia

Pliki w folderze wdrożenia są zablokowane, gdy aplikacja jest uruchomiona. Nie można zastąpić zablokowanych plików podczas wdrażania. Aby zwolnić zablokowane pliki we wdrożeniu, Zatrzymaj pulę aplikacji, korzystając z **jednej** z następujących metod:

* Użyj Web Deploy i Reference `Microsoft.NET.Sdk.Web` w pliku projektu. Plik *app_offline.htm* jest umieszczony w katalogu głównym katalogu aplikacji sieci Web. Gdy plik jest obecny, moduł ASP.NET Core bezpiecznie zamyka aplikację i obsługuje plik *app_offline.htm* podczas wdrażania. Aby uzyskać więcej informacji, zobacz [Informacje o konfiguracji modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Ręcznie Zatrzymaj pulę aplikacji w Menedżerze usług IIS na serwerze.
* Użyj programu PowerShell do porzucenia *app_offline.htm* (wymaga programu PowerShell 5 lub nowszego):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Ochrona danych

[Stos ochrony danych ASP.NET Core](xref:security/data-protection/introduction) jest używany przez kilka ASP.NET Core [middlewares](xref:fundamentals/middleware/index), w tym oprogramowanie pośredniczące używane podczas uwierzytelniania. Nawet jeśli interfejsy API ochrony danych nie są wywoływane przez kod użytkownika, należy skonfigurować ochronę danych przy użyciu skryptu wdrożenia lub w kodzie użytkownika, aby utworzyć trwały [Magazyn kluczy](xref:security/data-protection/implementation/key-management)kryptograficznych. Jeśli ochrona danych nie jest skonfigurowana, klucze są przechowywane w pamięci i usuwane po ponownym uruchomieniu aplikacji.

Jeśli pierścień kluczy jest przechowywany w pamięci po ponownym uruchomieniu aplikacji:

* cookieTokeny uwierzytelniania na podstawie wszystkich są unieważnione. 
* Użytkownicy muszą ponownie zalogować się przy następnym żądaniu. 
* Nie można już odszyfrować żadnych danych chronionych za pomocą dzwonka klucza. Może to obejmować [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) i [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata).

Aby skonfigurować ochronę danych w ramach usług IIS w celu utrwalenia pierścienia kluczy, należy użyć **jednej** z następujących metod:

* **Tworzenie kluczy rejestru ochrony danych**

  Klucze ochrony danych używane przez aplikacje ASP.NET Core są przechowywane w rejestrze zewnętrznym dla aplikacji. Aby utrzymać klucze dla danej aplikacji, należy utworzyć klucze rejestru dla puli aplikacji.

  W przypadku autonomicznych, nieopartych na webfarmie instalacji usług IIS [skrypt ochrony danych Provision-AutoGenKeys.ps1 programu PowerShell](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) może być używany dla każdej puli aplikacji używanej w aplikacji ASP.NET Core. Ten skrypt tworzy klucz rejestru w rejestrze HKLM, który jest dostępny tylko dla konta procesu roboczego puli aplikacji aplikacji. Klucze są szyfrowane przy użyciu funkcji DPAPI z kluczem dla całego komputera.

  W scenariuszach farmy sieci Web aplikacja może być skonfigurowana pod kątem używania ścieżki UNC do przechowywania tego dzwonka klucza ochrony danych. Domyślnie klucze ochrony danych nie są szyfrowane. Upewnij się, że uprawnienia do pliku dla udziału sieciowego są ograniczone do konta systemu Windows, w którym jest uruchamiana aplikacja. Certyfikat x509 może służyć do ochrony kluczy w spoczynku. Rozważ zastosowanie mechanizmu umożliwiającego użytkownikom przekazywanie certyfikatów: Umieść certyfikaty w zaufanym magazynie certyfikatów użytkownika i upewnij się, że są one dostępne na wszystkich komputerach, na których działa aplikacja użytkownika. Szczegóły można znaleźć w temacie [Konfigurowanie ochrony danych ASP.NET Core](xref:security/data-protection/configuration/overview) .

* **Konfigurowanie puli aplikacji usług IIS w celu załadowania profilu użytkownika**

  To ustawienie znajduje się w sekcji **model procesów** w obszarze **Ustawienia zaawansowane** dla puli aplikacji. Ustaw **Załaduj profil użytkownika** na `True` . Po ustawieniu opcji `True` klucze są przechowywane w katalogu profilu użytkownika i chronione przy użyciu funkcji DPAPI z kluczem specyficznym dla konta użytkownika. Klucze są utrwalane w folderze *% LocalAppData%/ASP.NET/dataprotection-Keys* .

  [Atrybut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) puli aplikacji również musi być włączony. Wartość domyślna `setProfileEnvironment` to `true` . W niektórych scenariuszach (na przykład system operacyjny Windows) `setProfileEnvironment` jest ustawiony na `false` . Jeśli klucze nie są przechowywane w katalogu profilu użytkownika zgodnie z oczekiwaniami:

  1. Przejdź do folderu *% windir%/system32/inetsrv/config*
  1. Otwórz plik *applicationHost.config* .
  1. Znajdź `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.
  1. Upewnij się, że `setProfileEnvironment` atrybut nie istnieje, który jest wartością domyślną `true` , lub jawnie ustaw wartość atrybutu na `true` .

* **Używanie systemu plików jako magazynu kluczy**

  Dostosuj kod aplikacji, [tak aby używał systemu plików jako magazynu kluczy](xref:security/data-protection/configuration/overview). Użyj certyfikatu x509 do ochrony pierścienia kluczy i upewnij się, że certyfikat jest certyfikatem zaufanym. Jeśli certyfikat jest podpisany z podpisem własnym, umieść certyfikat w zaufanym magazynie głównym.

  W przypadku korzystania z usług IIS w kolektywie serwerów sieci Web:

  * Użyj udziału plików, do którego mają dostęp wszystkie maszyny.
  * Wdróż certyfikat x509 na każdym komputerze. Konfigurowanie [ochrony danych w kodzie](xref:security/data-protection/configuration/overview).

* **Ustawianie zasad dla całej maszyny na potrzeby ochrony danych**

  System ochrony danych ma ograniczoną obsługę ustawiania domyślnych [zasad komputera](xref:security/data-protection/configuration/machine-wide-policy) dla wszystkich aplikacji korzystających z interfejsów API ochrony danych. Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/introduction>.

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

1. W oknie dialogowym **Dodawanie aplikacji** Użyj przycisku **Wybierz** dla **puli aplikacji** , aby przypisać pulę aplikacji utworzoną dla aplikacji podrzędnej. Kliknij przycisk **OK**.

Przypisanie oddzielnej puli aplikacji do aplikacji podrzędnej jest wymagane w przypadku korzystania z modelu hostingu w procesie.

Aby uzyskać więcej informacji na temat modelu hostingu w procesie i konfigurowania modułu ASP.NET Core, zobacz <xref:host-and-deploy/aspnet-core-module> .

## <a name="configuration-of-iis-with-webconfig"></a>Konfiguracja usług IIS z web.config

Na konfigurację usług IIS wpływa `<system.webServer>` sekcja *web.config* dla scenariuszy usług IIS, które są funkcjonalne dla ASP.NET Core aplikacji za pomocą modułu ASP.NET Core. Na przykład konfiguracja usług IIS jest funkcjonalna dla kompresji dynamicznej. Jeśli usługi IIS są skonfigurowane na poziomie serwera do korzystania z kompresji dynamicznej, `<urlCompression>` element w pliku *web.config* aplikacji może go wyłączyć dla aplikacji ASP.NET Core.

Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Dokumentacja konfiguracyjna programu \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Aby ustawić zmienne środowiskowe dla poszczególnych aplikacji działających w pulach izolowanych aplikacji (obsługiwanych w przypadku usług IIS 10,0 lub nowszych), zobacz sekcję *polecenieAppCmd.exe* w temacie [ \<environmentVariables> zmienne środowiskowe](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) w dokumentacji dotyczącej usług IIS.

## <a name="configuration-sections-of-webconfig"></a>Sekcje konfiguracji web.config

Sekcje konfiguracyjne aplikacji ASP.NET 4. x w *web.config* nie są używane przez aplikacje ASP.NET Core do konfiguracji:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Aplikacje ASP.NET Core są konfigurowane przy użyciu innych dostawców konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Pule aplikacji

Izolacja puli aplikacji jest określana przez model hostingu:

* Hosting w procesie: aplikacje muszą być uruchamiane w oddzielnych pulach aplikacji.
* Hosting poza procesem: zalecamy izolowanie aplikacji od siebie, uruchamiając każdą aplikację w jej własnej puli aplikacji.

Okno dialogowe **Dodaj witrynę sieci Web** usług IIS domyślnie umożliwia pojedynczej puli aplikacji na aplikację. Po podaniu **nazwy witryny** tekst zostanie automatycznie przeniesiony do pola tekstowego **Pula aplikacji** . Nowa pula aplikacji jest tworzona przy użyciu nazwy lokacji, gdy zostanie dodana lokacja.

## <a name="application-pool-no-locidentity"></a>Pula aplikacji Identity

Konto tożsamości puli aplikacji umożliwia uruchamianie aplikacji na unikatowym koncie bez konieczności tworzenia domen ani kont lokalnych oraz zarządzania nimi. W przypadku usług IIS 8,0 lub nowszych proces roboczy administratora usług IIS tworzy konto wirtualne o nazwie nowej puli aplikacji i domyślnie uruchamia procesy robocze puli aplikacji w ramach tego konta. W konsoli zarządzania usługami IIS w obszarze **Ustawienia zaawansowane** dla puli aplikacji upewnij się, że **Identity** ustawiono opcję Użyj **ApplicationPool Identity **:

![Okno dialogowe Zaawansowane ustawienia puli aplikacji](index/_static/apppool-identity.png)

Proces zarządzania usługami IIS tworzy Bezpieczny identyfikator z nazwą puli aplikacji w systemie zabezpieczeń systemu Windows. Zasoby mogą być zabezpieczone przy użyciu tej tożsamości. Jednak ta tożsamość nie jest kontem użytkownika rzeczywistego i nie jest wyświetlana w konsoli zarządzania użytkownikami systemu Windows.

Jeśli proces roboczy usług IIS wymaga podwyższonego poziomu dostępu do aplikacji, Zmodyfikuj listę Access Control (ACL) dla katalogu zawierającego aplikację:

1. Otwórz Eksploratora Windows i przejdź do katalogu.

1. Kliknij prawym przyciskiem myszy katalog i wybierz polecenie **Właściwości**.

1. Na karcie **zabezpieczenia** wybierz przycisk **Edytuj** , a następnie przycisk **Dodaj** .

1. Wybierz przycisk **lokalizacje** i upewnij się, że wybrano system.

1. Wprowadź **<usług IIS puli aplikacji \\ app_pool_name>** w polu **Wprowadź nazwy obiektów do wybrania** . Wybierz przycisk **Sprawdź nazwy** . W obszarze *Domyślna pula aplikacji* Sprawdź nazwy za pomocą **usług IIS AppPool\DefaultAppPool**. Po wybraniu przycisku **Sprawdź nazwy** w obszarze nazwy obiektów zostanie wykazana wartość **Domyślna pula aplikacji** . Nie można wprowadzić nazwy puli aplikacji bezpośrednio w obszarze nazw obiektów. Podczas sprawdzania nazwy obiektu Użyj **<IIS puli aplikacji \\ app_pool_name>** .

   ![Okno dialogowe Wybieranie użytkowników lub grup dla folderu aplikacji: Nazwa puli aplikacji "domyślna pula" jest dołączana do "puli aplikacji IIS \" w obszarze nazw obiektów przed wybraniem pozycji" Sprawdź nazwy ".](index/_static/select-users-or-groups-1.png)

1. Kliknij przycisk **OK**.

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

W przypadku wdrożenia w procesie podczas ustanawiania połączenia HTTP/2 w raportach [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` . W przypadku wdrożenia poza procesem, gdy jest nawiązywane połączenie HTTP/2, raporty [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/1.1` .

Aby uzyskać więcej informacji na temat modeli hostingu w procesie i poza procesami, zobacz <xref:host-and-deploy/aspnet-core-module> .

Protokół HTTP/2 jest domyślnie włączony. Połączenia powracają do protokołu HTTP/1.1, jeśli połączenie HTTP/2 nie zostało ustanowione. Aby uzyskać więcej informacji na temat konfiguracji protokołu HTTP/2 z wdrożeniami usług IIS, zobacz [http/2 w usługach IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Żądania inspekcji wstępnej CORS

*Ta sekcja dotyczy tylko ASP.NET Core aplikacji przeznaczonych dla .NET Framework.*

W przypadku aplikacji ASP.NET Core, która jest przeznaczona dla .NET Framework, żądania opcji nie są domyślnie przesyłane do aplikacji w usługach IIS. Aby dowiedzieć się, jak skonfigurować obsługę usług IIS w *web.config* , aby przekazywać żądania dotyczące opcji, zobacz [Włączanie żądań między źródłami w ASP.NET Web API 2: jak działa mechanizm CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Moduł inicjalizacji aplikacji i limit czasu bezczynności

Hostowane w usługach IIS przez moduł ASP.NET Core w wersji 2:

* [Moduł inicjalizacji aplikacji](#application-initialization-module): hostowana [w procesie](#in-process-hosting-model) lub [poza procesem](#out-of-process-hosting-model) aplikacja może zostać skonfigurowana do automatycznego uruchamiania na potrzeby ponownego uruchomienia procesu roboczego lub ponownego uruchomienia serwera.
* [Limit czasu bezczynności](#idle-timeout): hostowana [w trakcie](#in-process-hosting-model) działania aplikacji można skonfigurować w taki sposób, aby nie przekroczyć limitu czasu podczas okresów braku aktywności.

### <a name="application-initialization-module"></a>Moduł inicjalizacji aplikacji

*Dotyczy aplikacji hostowanych w procesie i pozaprocesowe.*

[Inicjowanie aplikacji IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) to funkcja usług IIS, która wysyła do aplikacji żądanie HTTP, gdy pula aplikacji zostanie uruchomiona lub zostanie odtworzona. Żądanie wyzwala uruchomienie aplikacji. Domyślnie usługi IIS wysyłają żądanie do głównego adresu URL aplikacji ( `/` ) w celu zainicjowania aplikacji (zobacz [dodatkowe zasoby](#application-initialization-module-and-idle-timeout-additional-resources) , aby uzyskać więcej informacji na temat konfiguracji).

Upewnij się, że funkcja inicjowania roli inicjalizacji aplikacji IIS jest włączona:

Na komputerach z systemem Windows 7 lub nowszym w przypadku lokalnego korzystania z usług IIS:

1. Przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).
1. Otwórz **Internet Information Services** > **World Wide Web Services** > **funkcje projektowania aplikacji**Internet Information Services World Wide Web Services.
1. Zaznacz pole wyboru dla **inicjowania aplikacji**.

W systemie Windows Server 2008 R2 lub nowszym:

1. Otwórz **Kreatora dodawania ról i funkcji**.
1. W panelu **Wybierz usługi ról** Otwórz węzeł **Programowanie aplikacji** .
1. Zaznacz pole wyboru dla **inicjowania aplikacji**.

Użyj jednego z poniższych metod, aby włączyć moduł inicjowania aplikacji dla lokacji:

* Za pomocą Menedżera usług IIS:

  1. W panelu **połączenia** wybierz pozycję **Pule aplikacji** .
  1. Kliknij prawym przyciskiem myszy pulę aplikacji aplikacji na liście i wybierz pozycję **Ustawienia zaawansowane**.
  1. Domyślny **tryb uruchamiania** to **OnDemand**. Ustaw **tryb uruchamiania** na **AlwaysRunning**. Kliknij przycisk **OK**.
  1. Otwórz węzeł **Lokacje** w panelu **połączenia** .
  1. Kliknij prawym przyciskiem myszy aplikację i wybierz pozycję Zarządzaj ustawieniami zaawansowanymi **witryny sieci Web** > **Advanced Settings**.
  1. Domyślnym ustawieniem **wstępnego ładowania** jest **wartość false**. Ustaw dla opcji **wstępnego ładowania** **wartość true**. Kliknij przycisk **OK**.

* Za pomocą *web.config*Dodaj `<applicationInitialization>` element z `doAppInitAfterRestart` ustawionym do `true` `<system.webServer>` elementów w pliku *web.config* aplikacji:

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
1. Domyślny **limit czasu bezczynności (w minutach)** wynosi **20** minut. Ustaw **limit czasu bezczynności (w minutach)** na **0** (zero). Kliknij przycisk **OK**.
1. Odtwórz proces roboczy.

Aby zapobiec przekroczeniu limitu [czasu hostowanych przez aplikacje](#out-of-process-hosting-model) aplikacji, użyj jednej z następujących metod:

* Wyślij polecenie ping do aplikacji z zewnętrznej usługi, aby było ono uruchomione.
* Jeśli aplikacja obsługuje tylko usługi w tle, należy unikać hostingu usług IIS i używać [usługi systemu Windows do hostowania aplikacji ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Dodatkowe zasoby dotyczące modułu inicjalizacji aplikacji i limitu czasu bezczynności

* [Inicjowanie aplikacji usług IIS 8,0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Inicjowanie \<applicationInitialization> aplikacji ](/iis/configuration/system.webserver/applicationinitialization/).
* [Ustawienia modelu procesów dla \<processModel> puli aplikacji ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="deployment-resources-for-iis-administrators"></a>Zasoby wdrażania dla administratorów usług IIS

* [Dokumentacja usług IIS](/iis)
* [Wprowadzenie z menedżerem usług IIS w usługach IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Wdrażanie aplikacji .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:test/troubleshoot>
* <xref:index>
* [Oficjalna witryna programu Microsoft IIS](https://www.iis.net/)
* [Biblioteka zawartości technicznej systemu Windows Server](/windows-server/windows-server)
* [Protokół HTTP/2 w usługach IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Aby zapoznać się z samouczkiem dotyczącym publikowania aplikacji ASP.NET Core na serwerze usług IIS, zobacz <xref:tutorials/publish-to-iis> .

[Zainstaluj pakiet hostingu platformy .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Obsługiwane są następujące systemy operacyjne:

* System Windows 7 lub nowszy
* Windows Server 2008 R2 lub nowszy

[ SerwerHTTP.sys](xref:fundamentals/servers/httpsys) (znany wcześniej jako webListener) nie działa w konfiguracji zwrotnego serwera proxy z usługami IIS. Użyj [serwera Kestrel](xref:fundamentals/servers/kestrel).

Aby uzyskać informacje na temat hostingu na platformie Azure, zobacz <xref:host-and-deploy/azure-apps/index> .

Aby uzyskać wskazówki dotyczące rozwiązywania problemów, zobacz <xref:test/troubleshoot> .

## <a name="supported-platforms"></a>Obsługiwane platformy

Obsługiwane są aplikacje opublikowane dla wdrożenia 32-bitowego (x86) lub 64-bitowego (x64). Wdróż aplikację 32-bitową z 32-bitową (x86) zestaw .NET Core SDK, chyba że aplikacja:

* Wymagana jest większa przestrzeń adresów pamięci wirtualnej dla aplikacji 64-bitowej.
* Wymaga większego rozmiaru stosu IIS.
* Ma 64-bitowe zależności natywne.

Aby opublikować aplikację 64-bitową, należy użyć 64-bitowej (x64) zestaw .NET Core SDK. 64-bitowy środowisko uruchomieniowe musi być obecny w systemie hosta.

## <a name="hosting-models"></a>Modele hostingu

### <a name="in-process-hosting-model"></a>Model hostingu w procesie

Korzystając z hostingu w procesie, aplikacja ASP.NET Core jest uruchamiana w tym samym procesie co proces roboczy usług IIS. Hosting w procesie zapewnia lepszą wydajność w porównaniu z obsługą hostingu poza procesem, ponieważ:

* Żądania nie są przekazywane za pośrednictwem karty sprzężenia zwrotnego. Karta sprzężenia zwrotnego jest interfejsem sieciowym, który zwraca wychodzący ruch sieciowy z powrotem do tego samego komputera.

Usługi IIS obsługują zarządzanie procesami przy użyciu [usługi aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

[Moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module):

* Wykonuje inicjalizację aplikacji.
  * Ładuje [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Wywołania `Program.Main` .
* Obsługuje okres istnienia żądania natywnego usług IIS.

Model hostingu w procesie nie jest obsługiwany w przypadku aplikacji ASP.NET Core przeznaczonych dla .NET Framework.

Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowaną w procesie aplikacją:

![Moduł ASP.NET Core w scenariuszu hostingu w procesie](index/_static/ancm-inprocess.png)

Żądanie dociera do sieci Web do sterownika HTTP.sys trybu jądra. Sterownik kieruje natywne żądanie do usług IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS). Moduł ASP.NET Core odbiera żądanie natywne i przekazuje go do serwera HTTP usług IIS ( `IISHttpServer` ). Serwer HTTP IIS jest implementacją serwera w procesie dla usług IIS, która konwertuje żądanie z natywnego na zarządzane.

Po przetworzeniu żądania przez serwer HTTP IIS żądanie jest wypychane do potoku ASP.NET Core pośredniczącego. Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji. Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS za pośrednictwem serwera HTTP IIS. Program IIS wysyła odpowiedź do klienta, który zainicjował żądanie.

Hosting w procesie jest nieobecny w przypadku istniejących aplikacji, ale w przypadku wszystkich scenariuszy z usługami w ramach programu z obsługą IIS Express usług w toku są domyślnie obsługiwane [nowe](/dotnet/core/tools/dotnet-new) szablony.

`CreateDefaultBuilder` dodaje <xref:Microsoft.AspNetCore.Hosting.Server.IServer> wystąpienie przez wywołanie <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> metody w celu uruchomienia [CoreCLR](/dotnet/standard/glossary#coreclr) i hostowania aplikacji w procesie roboczym usług IIS (*w3wp.exe* lub *iisexpress.exe*). Testy wydajności wskazują, że hostowanie aplikacji platformy .NET Core w procesie zapewnia znacznie wyższą przepływność żądań w porównaniu z obsługą żądań serwera proxy poza procesem i serwerem [Kestrel](xref:fundamentals/servers/kestrel) .

### <a name="out-of-process-hosting-model"></a>Model hostingu poza procesem

Ponieważ ASP.NET Core aplikacje działają w procesie innym niż proces roboczy usług IIS, moduł ASP.NET Core obsługuje zarządzanie procesami. Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli zostanie zamknięty lub ulegnie awarii. Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowanym przez aplikację aplikacją:

![Moduł ASP.NET Core w scenariuszu hostingu poza procesem](index/_static/ancm-outofprocess.png)

Żądania docierają z sieci Web do sterownika HTTP.sys trybu jądra. Sterownik kieruje żądania do usług IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS). Moduł przekazuje żądania do Kestrel na losowo wybranym porcie dla aplikacji, która nie jest portem 80 lub 443.

Moduł określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania, a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> rozszerzenie konfiguruje serwer do nasłuchiwania `http://localhost:{PORT}` . Dodatkowe sprawdzenia są wykonywane, a żądania, które nie pochodzą z modułu, są odrzucane. Moduł nie obsługuje przekazywania HTTPS, dlatego żądania są przekazywane przez protokół HTTP nawet wtedy, gdy są odbierane przez usługę IIS przez protokół HTTPS.

Po podaniu przez Kestrel żądania z modułu żądanie jest wypychane do potoku ASP.NET Core pośredniczącego. Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji. Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i pathbase, aby można było przesłać żądanie do Kestrel. Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS, która wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.

Aby uzyskać wskazówki dotyczące konfiguracji modułu ASP.NET Core, zobacz <xref:host-and-deploy/aspnet-core-module> .

Aby uzyskać więcej informacji na temat hostingu, zobacz [host in ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Konfiguracja aplikacji

### <a name="enable-the-iisintegration-components"></a>Włącz składniki IISIntegration

Podczas kompilowania hosta w `CreateWebHostBuilder` (*program.cs*), wywołaj, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> Aby włączyć integrację usług IIS:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Aby uzyskać więcej informacji na temat `CreateDefaultBuilder` , zobacz <xref:fundamentals/host/web-host#set-up-a-host> .

### <a name="iis-options"></a>Opcje usług IIS

**Model hostingu w procesie**

Aby skonfigurować opcje serwera usług IIS, należy uwzględnić <xref:Microsoft.AspNetCore.Builder.IISServerOptions> w programie konfigurację usługi <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> . Poniższy przykład wyłącza AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Opcja                         | Domyślny | Ustawienie |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Jeśli `true` serwer usług IIS ustawi uwierzytelnienie `HttpContext.User` za pomocą [uwierzytelniania systemu Windows](xref:security/authentication/windowsauth). Jeśli `false` serwer zawiera tylko tożsamość dla `HttpContext.User` i reaguje na wyzwania, gdy zostanie jawnie zlecony przez `AuthenticationScheme` . Aby program mógł działać, należy włączyć uwierzytelnianie systemu Windows `AutomaticAuthentication` . Aby uzyskać więcej informacji, zobacz [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Ustawia nazwę wyświetlaną pokazywaną użytkownikom na stronach logowania. |

**Model hostingu poza procesem**

Aby skonfigurować opcje usług IIS, należy uwzględnić <xref:Microsoft.AspNetCore.Builder.IISOptions> w programie konfigurację usługi <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> . Poniższy przykład uniemożliwia wypełnianie aplikacji `HttpContext.Connection.ClientCertificate` :

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Opcja                         | Domyślny | Ustawienie |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Jeśli `true` [oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components) ustawi uwierzytelnienie `HttpContext.User` za pomocą [uwierzytelniania systemu Windows](xref:security/authentication/windowsauth). Jeśli `false` oprogramowanie pośredniczące zapewnia tylko tożsamość `HttpContext.User` i reaguje na wyzwania, gdy zostanie jawnie zażądana przez program `AuthenticationScheme` . Aby program mógł działać, należy włączyć uwierzytelnianie systemu Windows `AutomaticAuthentication` . Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth) . |
| `AuthenticationDisplayName`    | `null`  | Ustawia nazwę wyświetlaną pokazywaną użytkownikom na stronach logowania. |
| `ForwardClientCertificate`     | `true`  | Jeśli `true` `MS-ASPNETCORE-CLIENTCERT` jest obecny nagłówek żądania, `HttpContext.Connection.ClientCertificate` zostanie wypełniony. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

[Oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components), które konfiguruje przekazane nagłówki oprogramowania pośredniczącego, a moduł ASP.NET Core jest skonfigurowany do przesyłania dalej schematu (http/https) i zdalnego adresu IP, z którego pochodzi żądanie. W przypadku aplikacji hostowanych za dodatkowymi serwerami proxy i modułami równoważenia obciążenia może być wymagana dodatkowa konfiguracja. Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>Plik web.config

Plik *web.config* konfiguruje [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Tworzenie, przekształcanie i publikowanie pliku *web.config* jest obsługiwane przez obiekt docelowy MSBuild ( `_TransformWebConfig` ), gdy projekt jest publikowany. Ten element docelowy znajduje się w obiektach docelowych zestawu SDK sieci Web ( `Microsoft.NET.Sdk.Web` ). Zestaw SDK jest ustawiany u góry pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Jeśli plik *web.config* nie jest obecny w projekcie, plik zostanie utworzony z prawidłowymi *argumentami* *ProcessPath* i, aby skonfigurować moduł ASP.NET Core i przeniesiony do [publikowanych danych wyjściowych](xref:host-and-deploy/directory-structure).

Jeśli plik *web.config* jest obecny w projekcie, plik zostanie przekształcony przy użyciu poprawnych *processPath* i *argumentów* w celu skonfigurowania modułu ASP.NET Core i przeniesiona do publikowanych danych wyjściowych. Transformacja nie modyfikuje ustawień konfiguracji usług IIS w pliku.

Plik *web.config* może zapewnić dodatkowe ustawienia konfiguracji usług IIS kontrolujące aktywne moduły usług IIS. Aby uzyskać informacje na temat modułów usług IIS, które mogą przetwarzać żądania z aplikacjami ASP.NET Core, zobacz temat [moduły usług IIS](xref:host-and-deploy/iis/modules) .

Aby zapobiec transformacji pliku *web.config* przez zestaw SDK sieci Web, należy użyć **\<IsTransformWebConfigDisabled>** właściwości w pliku projektu:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Podczas wyłączania pliku zestawu SDK sieci Web, *processPath* i *argumenty* powinny być ustawiane ręcznie przez dewelopera. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>Lokalizacja pliku web.config

W celu poprawnego skonfigurowania [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) plik *web.config* musi znajdować się w ścieżce [katalogu głównego zawartości](xref:fundamentals/index#content-root) (zazwyczaj ścieżka podstawowa aplikacji) wdrożonej aplikacji. Ta sama lokalizacja jest taka sama jak ścieżka fizyczna witryny sieci Web dostarczana do usług IIS. Plik *web.config* jest wymagany w katalogu głównym aplikacji, aby umożliwić Publikowanie wielu aplikacji przy użyciu Web Deploy.

Poufne pliki znajdują się w ścieżce fizycznej aplikacji, na przykład * \<assembly>.runtimeconfig.jsna*, * \<assembly> . XML* (Komentarze dokumentacji XML) i * \<assembly>.deps.js*. Gdy plik *web.config* jest obecny, a lokacja jest uruchamiana normalnie, usługi IIS nie będą obsługiwały tych poufnych plików, jeśli są żądane. Jeśli brakuje pliku *web.config* , nazwa jest nieprawidłowa lub nie można skonfigurować lokacji do normalnego uruchamiania, usługi IIS mogą publicznie udostępniać poufne pliki.

**Plik *web.config* musi być obecny we wdrożeniu przez cały czas, prawidłowo nazwany i można skonfigurować lokację pod kątem normalnego uruchamiania. Nigdy nie usuwaj pliku *web.config* z wdrożenia produkcyjnego.**

### <a name="transform-webconfig"></a>Przekształcanie pliku web.config

Jeśli musisz przekształcić *web.config* przy publikowaniu (na przykład ustawić zmienne środowiskowe na podstawie konfiguracji, profilu lub środowiska), zobacz <xref:host-and-deploy/iis/transform-webconfig> .

## <a name="iis-configuration"></a>Konfiguracja usług IIS

**Systemy operacyjne Windows Server**

Włącz rolę serwera **serwera sieci Web (IIS)** i Ustanów usługi ról.

1. Użyj kreatora **dodawania ról i funkcji** z menu **Zarządzaj** lub łącza w **Menedżer serwera**. W kroku **role serwera** zaznacz pole wyboru **serwer sieci Web (IIS)**.

   ![W kroku wybierz role serwera zostanie wybrana rola IIS serwera sieci Web.](index/_static/server-roles-ws2016.png)

1. Po wykonaniu **kroków krok po kroku** **usługi ról** są ładowane dla serwera sieci Web (IIS). Wybierz żądane usługi roli IIS lub zaakceptuj domyślne usługi ról.

   ![Domyślne usługi ról są wybrane w kroku Wybierz usługi ról.](index/_static/role-services-ws2016.png)

   **Uwierzytelnianie systemu Windows (opcjonalnie)**  
   Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **Zabezpieczenia serwera sieci Web**  >  **Security**. Wybierz funkcję **uwierzytelniania systemu Windows** . Aby uzyskać więcej informacji, zobacz [uwierzytelnianie \<windowsAuthentication> systemu Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

   **Obiekty WebSockets (opcjonalnie)**  
   Obiekty WebSockets są obsługiwane w ASP.NET Core 1,1 lub nowszych. Aby włączyć obiekty WebSockets, rozwiń następujące węzły: projektowanie aplikacji **serwera sieci Web**  >  **Application Development**. Wybierz funkcję **protokołu WebSocket** . Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).

1. Przejdź do kroku **potwierdzenia** , aby zainstalować rolę i usługi serwera sieci Web. Po zainstalowaniu roli **serwera sieci Web (IIS)** nie jest wymagane ponowne uruchomienie serwera ani usług IIS.

**Systemy operacyjne Windows dla komputerów stacjonarnych**

Włącz **konsolę zarządzania usług IIS** i **usługi World Wide Web**.

1. Przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).

1. Otwórz węzeł **Internet Information Services** . Otwórz węzeł **Narzędzia do zarządzania siecią Web** .

1. Zaznacz pole wyboru w obszarze **Konsola zarządzania usługami IIS**.

1. Zaznacz pole wyboru **usług World Wide Web Services**.

1. Zaakceptuj domyślne funkcje **usług World Wide Web Services** lub Dostosuj funkcje usług IIS.

   **Uwierzytelnianie systemu Windows (opcjonalnie)**  
   Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **World Wide Web usług**  >  **zabezpieczenia**. Wybierz funkcję **uwierzytelniania systemu Windows** . Aby uzyskać więcej informacji, zobacz [uwierzytelnianie \<windowsAuthentication> systemu Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

   **Obiekty WebSockets (opcjonalnie)**  
   Obiekty WebSockets są obsługiwane w ASP.NET Core 1,1 lub nowszych. Aby włączyć obiekty WebSockets, rozwiń następujące węzły: **World Wide Web Services**  >  **Application Development Features**. Wybierz funkcję **protokołu WebSocket** . Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).

1. Jeśli instalacja usług IIS wymaga ponownego uruchomienia, należy ponownie uruchomić system.

![Konsola zarządzania usługami IIS i usługi World Wide Web są wybrane w funkcjach systemu Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Zainstaluj pakiet hostingu platformy .NET Core

Zainstaluj *pakiet hostingu platformy .NET Core* w systemie hostingu. Pakiet instaluje środowisko uruchomieniowe programu .NET Core, bibliotekę .NET Core i [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Moduł umożliwia uruchamianie aplikacji ASP.NET Core za pomocą usług IIS.

> [!IMPORTANT]
> Jeśli pakiet hostingu jest zainstalowany przed usługami IIS, należy naprawić pakiet instalacyjny. Uruchom ponownie Instalatora pakietu hostingu po zainstalowaniu usług IIS.
>
> Jeśli pakiet hostingu jest instalowany po zainstalowaniu 64-bitowej (x64) wersji programu .NET Core, prawdopodobnie brakuje zestawów SDK ([nie wykryto żadnych zestawów SDK dla platformy .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Aby rozwiązać ten problem, zobacz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> .

### <a name="download"></a>Pobierz

1. Przejdź do strony [pobieranie platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .
1. Wybierz żądaną wersję programu .NET Core.
1. W kolumnie **Uruchom aplikacje — środowisko uruchomieniowe** Znajdź wiersz żądanej wersji środowiska uruchomieniowego platformy .NET Core.
1. Pobierz instalatora przy użyciu linku do **pakietu hostingu** .

> [!WARNING]
> Niektóre Instalatory zawierają wersje wydania, które osiągnęły koniec cyklu życia (EOL) i nie są już obsługiwane przez firmę Microsoft. Aby uzyskać więcej informacji, zobacz [zasady pomocy technicznej](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Zainstaluj pakiet hostingu

1. Uruchom Instalatora na serwerze. Następujące parametry są dostępne podczas uruchamiania Instalatora z powłoki poleceń administratora:

   * `OPT_NO_ANCM=1`: Pomiń Instalowanie modułu ASP.NET Core.
   * `OPT_NO_RUNTIME=1`: Pomiń Instalowanie środowiska uruchomieniowego platformy .NET Core. Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1`: Pomiń instalację ASP.NET Shared Framework (ASP.NET Runtime). Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1`: Pomiń Instalowanie środowiska uruchomieniowego x86. Użyj tego parametru, Jeśli wiesz, że nie będziesz hostować aplikacji 32-bitowych. Jeśli w przyszłości będziesz hostować zarówno aplikacje 32-bitowe, jak i 64-bitowe, nie używaj tego parametru i zainstaluj oba środowiska uruchomieniowe.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: Wyłącz sprawdzanie przy użyciu konfiguracji udostępnionej usług IIS, gdy konfiguracja udostępniona (*applicationHost.config*) znajduje się na tym samym komputerze, na którym zainstalowano program IIS. *Dostępne tylko w przypadku ASP.NET Core 2,2 lub nowszych instalatorów pakietu do obsługi.* Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Uruchom ponownie system lub wykonaj następujące polecenia w powłoce poleceń:

   ```console
   net stop was /y
   net start w3svc
   ```
   Ponowne uruchomienie usług IIS powoduje zmianę ścieżki systemowej, która jest zmienną środowiskową, wykonaną przez Instalatora.

Nie jest konieczne ręczne zatrzymanie poszczególnych lokacji w usługach IIS podczas instalowania pakietu hostingu. Aplikacje hostowane (lokacje IIS) są ponownie uruchamiane po ponownym uruchomieniu usług IIS. Aplikacje są uruchamiane ponownie po otrzymaniu pierwszego żądania, w tym od [modułu inicjalizacji aplikacji](#application-initialization-module-and-idle-timeout).

ASP.NET Core przyjmuje zachowanie funkcji przekazywania do przodu dla wydań poprawek udostępnionych pakietów platformy. Po ponownym uruchomieniu aplikacji hostowanych przez usługi IIS przy użyciu usług IIS aplikacje są ładowane z najnowszymi wersjami poprawki pakietów, do których się odwołują, gdy otrzymają swoje pierwsze żądanie. Jeśli usługi IIS nie zostaną ponownie uruchomione, aplikacje ponownie uruchamiają się i wykazują zachowanie przekazujące, gdy procesy robocze są odtwarzane i otrzymują swoje pierwsze żądanie.

> [!NOTE]
> Aby uzyskać informacje na temat konfiguracji udostępnionej usług IIS, zobacz [ASP.NET Core Module z udostępnioną konfiguracją usług IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Zainstaluj Web Deploy przy publikowaniu w programie Visual Studio

Podczas wdrażania aplikacji na serwerach z [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)Zainstaluj najnowszą wersję Web Deploy na serwerze. Aby zainstalować Web Deploy, należy użyć [Instalatora platformy sieci Web (Instalatora WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) lub uzyskać instalatora bezpośrednio z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=43717). Preferowaną metodą jest użycie Instalatora WebPI. Instalatora WebPI oferuje autonomiczną konfigurację i konfigurację dla dostawców hostingu.

## <a name="create-the-iis-site"></a>Tworzenie witryny usług IIS

1. W systemie hostingu Utwórz folder, który będzie zawierać opublikowane foldery i pliki aplikacji. W poniższym kroku ścieżka folderu jest dostarczana do usług IIS jako ścieżka fizyczna do aplikacji. Aby uzyskać więcej informacji na temat folderu wdrożenia aplikacji i układu pliku, zobacz <xref:host-and-deploy/directory-structure> .

1. W Menedżerze usług IIS Otwórz węzeł serwera w panelu **połączenia** . Kliknij prawym przyciskiem myszy folder **sitess** . Wybierz pozycję **Dodaj witrynę internetową** z menu kontekstowego.

1. Podaj **nazwę lokacji** i ustaw **ścieżkę fizyczną** folderu wdrożenia aplikacji. Podaj konfigurację **powiązania** i Utwórz witrynę sieci Web, wybierając **przycisk OK**:

   ![Podaj nazwę lokacji, ścieżkę fizyczną i nazwę hosta w kroku Dodaj witrynę sieci Web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > `http://*:80/` `http://+:80` **Nie** należy używać powiązań z symbolami wieloznacznymi (i) najwyższego poziomu. Powiązania z symbolami wieloznacznymi najwyższego poziomu mogą otwierać aplikację pod kątem luk w zabezpieczeniach. Dotyczy to zarówno silnych, jak i słabych symboli wieloznacznych. Używaj jawnych nazw hostów zamiast symboli wieloznacznych. Powiązanie symboli wieloznacznych z poddomeną (na przykład `*.mysub.com` ) nie ma tego ryzyka bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do `*.com` , która jest narażona). Aby uzyskać więcej informacji, zobacz [sekcję rfc7230-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .

1. W węźle serwera wybierz pozycję **Pule aplikacji**.

1. Kliknij prawym przyciskiem myszy pulę aplikacji lokacji i wybierz pozycję **Ustawienia podstawowe** z menu kontekstowego.

1. W oknie **Edytowanie puli aplikacji** Ustaw **wersję środowiska .NET CLR** na **Brak kodu zarządzanego**:

   ![Nie ustawiaj kodu zarządzanego dla wersji środowiska .NET CLR.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core działa w osobnym procesie i zarządza środowiskiem uruchomieniowym. ASP.NET Core nie polega na ładowaniu środowiska CLR (.NET CLR) &mdash; podstawowego środowiska uruchomieniowego języka wspólnego (CoreCLR) dla platformy .NET Core w celu hostowania aplikacji w procesie roboczym. Ustawienie **wersji środowiska .NET CLR** na **Brak kodu zarządzanego** jest opcjonalne, ale zalecane.

1. *ASP.NET Core 2,2 lub nowsza*: w przypadku [wdrożenia](/dotnet/core/deploying/#self-contained-deployments-scd) z systemem 64-bitowym (x64), które korzysta z [modelu hostingu w procesie](#in-process-hosting-model), wyłączaj pulę aplikacji dla procesów 32-bit (x86).

   Na pasku bocznym **Akcje** Menedżera usług IIS > **Pule aplikacji**wybierz pozycję **Ustaw ustawienia domyślne puli aplikacji** lub **Zaawansowane**. Znajdź **opcję Włącz aplikacje 32-bitowe** i ustaw wartość na `False` . To ustawienie nie ma wpływu na aplikacje wdrożone na potrzeby [hostingu poza procesem](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).

1. Potwierdź, że tożsamość modelu procesu ma odpowiednie uprawnienia.

   Jeśli domyślna tożsamość puli aplikacji (**model procesów**  >  **Identity** ) została zmieniona z **ApplicationPool Identity ** na inną tożsamość, sprawdź, czy Nowa tożsamość ma wymagane uprawnienia dostępu do folderu, bazy danych i innych wymaganych zasobów aplikacji. Na przykład Pula aplikacji wymaga dostępu do odczytu i zapisu do folderów, w których aplikacja odczytuje i zapisuje pliki.

**Konfiguracja uwierzytelniania systemu Windows (opcjonalnie)**  
Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

Wdróż aplikację w folderze **ścieżki fizycznej** usług IIS, który został ustanowiony w sekcji [Tworzenie witryny usług IIS](#create-the-iis-site) . [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) jest zalecanym mechanizmem wdrażania, ale istnieje kilka opcji przeniesienia aplikacji z folderu *publikowania* projektu do folderu wdrożenia systemu hostingu.

### <a name="web-deploy-with-visual-studio"></a>Web Deploy z programem Visual Studio

Aby dowiedzieć się, jak utworzyć profil publikowania do użytku z usługą Web Deploy, zobacz temat [Profile publikacji programu Visual Studio dla wdrożenia aplikacji ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) . Jeśli dostawca hostingu udostępnia profil publikowania lub obsługę tworzenia aplikacji, pobierz swój profil i zaimportuj go za pomocą okna dialogowego **publikowania** programu Visual Studio:

![Strona okna dialogowego publikowanie](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Web Deploy poza programem Visual Studio

[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) można również użyć poza programem Visual Studio z wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Narzędzie do wdrażania w sieci Web](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternatywy dla Web Deploy

Użyj dowolnej z kilku metod, aby przenieść aplikację do systemu hostingu, takich jak ręczna kopia, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)lub [PowerShell](/powershell/).

Aby uzyskać więcej informacji na ASP.NET Core wdrażania usług IIS, zobacz sekcję [zasoby wdrażania dla administratorów usług IIS](#deployment-resources-for-iis-administrators) .

## <a name="browse-the-website"></a>Przeglądanie witryny sieci Web

Po wdrożeniu aplikacji w systemie hostingu należy wysłać żądanie do jednej z publicznych punktów końcowych aplikacji.

W poniższym przykładzie lokacja jest powiązana z **nazwą hosta** usług IIS `www.mysite.com` na **porcie** `80` . Zostanie wysłane żądanie `http://www.mysite.com` :

![Przeglądarka Microsoft Edge załadowała stronę startową usług IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Zablokowane pliki wdrożenia

Pliki w folderze wdrożenia są zablokowane, gdy aplikacja jest uruchomiona. Nie można zastąpić zablokowanych plików podczas wdrażania. Aby zwolnić zablokowane pliki we wdrożeniu, Zatrzymaj pulę aplikacji, korzystając z **jednej** z następujących metod:

* Użyj Web Deploy i Reference `Microsoft.NET.Sdk.Web` w pliku projektu. Plik *app_offline.htm* jest umieszczony w katalogu głównym katalogu aplikacji sieci Web. Gdy plik jest obecny, moduł ASP.NET Core bezpiecznie zamyka aplikację i obsługuje plik *app_offline.htm* podczas wdrażania. Aby uzyskać więcej informacji, zobacz [Informacje o konfiguracji modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Ręcznie Zatrzymaj pulę aplikacji w Menedżerze usług IIS na serwerze.
* Użyj programu PowerShell do porzucenia *app_offline.htm* (wymaga programu PowerShell 5 lub nowszego):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Ochrona danych

[Stos ochrony danych ASP.NET Core](xref:security/data-protection/introduction) jest używany przez kilka ASP.NET Core [middlewares](xref:fundamentals/middleware/index), w tym oprogramowanie pośredniczące używane podczas uwierzytelniania. Nawet jeśli interfejsy API ochrony danych nie są wywoływane przez kod użytkownika, należy skonfigurować ochronę danych przy użyciu skryptu wdrożenia lub w kodzie użytkownika, aby utworzyć trwały [Magazyn kluczy](xref:security/data-protection/implementation/key-management)kryptograficznych. Jeśli ochrona danych nie jest skonfigurowana, klucze są przechowywane w pamięci i usuwane po ponownym uruchomieniu aplikacji.

Jeśli pierścień kluczy jest przechowywany w pamięci po ponownym uruchomieniu aplikacji:

* cookieTokeny uwierzytelniania na podstawie wszystkich są unieważnione. 
* Użytkownicy muszą ponownie zalogować się przy następnym żądaniu. 
* Nie można już odszyfrować żadnych danych chronionych za pomocą dzwonka klucza. Może to obejmować [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) i [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata).

Aby skonfigurować ochronę danych w ramach usług IIS w celu utrwalenia pierścienia kluczy, należy użyć **jednej** z następujących metod:

* **Tworzenie kluczy rejestru ochrony danych**

  Klucze ochrony danych używane przez aplikacje ASP.NET Core są przechowywane w rejestrze zewnętrznym dla aplikacji. Aby utrzymać klucze dla danej aplikacji, należy utworzyć klucze rejestru dla puli aplikacji.

  W przypadku autonomicznych, nieopartych na webfarmie instalacji usług IIS [skrypt ochrony danych Provision-AutoGenKeys.ps1 programu PowerShell](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) może być używany dla każdej puli aplikacji używanej w aplikacji ASP.NET Core. Ten skrypt tworzy klucz rejestru w rejestrze HKLM, który jest dostępny tylko dla konta procesu roboczego puli aplikacji aplikacji. Klucze są szyfrowane przy użyciu funkcji DPAPI z kluczem dla całego komputera.

  W scenariuszach farmy sieci Web aplikacja może być skonfigurowana pod kątem używania ścieżki UNC do przechowywania tego dzwonka klucza ochrony danych. Domyślnie klucze ochrony danych nie są szyfrowane. Upewnij się, że uprawnienia do pliku dla udziału sieciowego są ograniczone do konta systemu Windows, w którym jest uruchamiana aplikacja. Certyfikat x509 może służyć do ochrony kluczy w spoczynku. Rozważ zastosowanie mechanizmu umożliwiającego użytkownikom przekazywanie certyfikatów: Umieść certyfikaty w zaufanym magazynie certyfikatów użytkownika i upewnij się, że są one dostępne na wszystkich komputerach, na których działa aplikacja użytkownika. Szczegóły można znaleźć w temacie [Konfigurowanie ochrony danych ASP.NET Core](xref:security/data-protection/configuration/overview) .

* **Konfigurowanie puli aplikacji usług IIS w celu załadowania profilu użytkownika**

  To ustawienie znajduje się w sekcji **model procesów** w obszarze **Ustawienia zaawansowane** dla puli aplikacji. Ustaw **Załaduj profil użytkownika** na `True` . Po ustawieniu opcji `True` klucze są przechowywane w katalogu profilu użytkownika i chronione przy użyciu funkcji DPAPI z kluczem specyficznym dla konta użytkownika. Klucze są utrwalane w folderze *% LocalAppData%/ASP.NET/dataprotection-Keys* .

  [Atrybut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) puli aplikacji również musi być włączony. Wartość domyślna `setProfileEnvironment` to `true` . W niektórych scenariuszach (na przykład system operacyjny Windows) `setProfileEnvironment` jest ustawiony na `false` . Jeśli klucze nie są przechowywane w katalogu profilu użytkownika zgodnie z oczekiwaniami:

  1. Przejdź do folderu *% windir%/system32/inetsrv/config*
  1. Otwórz plik *applicationHost.config* .
  1. Znajdź `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.
  1. Upewnij się, że `setProfileEnvironment` atrybut nie istnieje, który jest wartością domyślną `true` , lub jawnie ustaw wartość atrybutu na `true` .

* **Używanie systemu plików jako magazynu kluczy**

  Dostosuj kod aplikacji, [tak aby używał systemu plików jako magazynu kluczy](xref:security/data-protection/configuration/overview). Użyj certyfikatu x509 do ochrony pierścienia kluczy i upewnij się, że certyfikat jest certyfikatem zaufanym. Jeśli certyfikat jest podpisany z podpisem własnym, umieść certyfikat w zaufanym magazynie głównym.

  W przypadku korzystania z usług IIS w kolektywie serwerów sieci Web:

  * Użyj udziału plików, do którego mają dostęp wszystkie maszyny.
  * Wdróż certyfikat x509 na każdym komputerze. Konfigurowanie [ochrony danych w kodzie](xref:security/data-protection/configuration/overview).

* **Ustawianie zasad dla całej maszyny na potrzeby ochrony danych**

  System ochrony danych ma ograniczoną obsługę ustawiania domyślnych [zasad komputera](xref:security/data-protection/configuration/machine-wide-policy) dla wszystkich aplikacji korzystających z interfejsów API ochrony danych. Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/introduction>.

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

1. W oknie dialogowym **Dodawanie aplikacji** Użyj przycisku **Wybierz** dla **puli aplikacji** , aby przypisać pulę aplikacji utworzoną dla aplikacji podrzędnej. Kliknij przycisk **OK**.

Przypisanie oddzielnej puli aplikacji do aplikacji podrzędnej jest wymagane w przypadku korzystania z modelu hostingu w procesie.

Aby uzyskać więcej informacji na temat modelu hostingu w procesie i konfigurowania modułu ASP.NET Core, zobacz <xref:host-and-deploy/aspnet-core-module> .

## <a name="configuration-of-iis-with-webconfig"></a>Konfiguracja usług IIS z web.config

Na konfigurację usług IIS wpływa `<system.webServer>` sekcja *web.config* dla scenariuszy usług IIS, które są funkcjonalne dla ASP.NET Core aplikacji za pomocą modułu ASP.NET Core. Na przykład konfiguracja usług IIS jest funkcjonalna dla kompresji dynamicznej. Jeśli usługi IIS są skonfigurowane na poziomie serwera do korzystania z kompresji dynamicznej, `<urlCompression>` element w pliku *web.config* aplikacji może go wyłączyć dla aplikacji ASP.NET Core.

Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Dokumentacja konfiguracyjna programu \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Aby ustawić zmienne środowiskowe dla poszczególnych aplikacji działających w pulach izolowanych aplikacji (obsługiwanych w przypadku usług IIS 10,0 lub nowszych), zobacz sekcję *polecenieAppCmd.exe* w temacie [ \<environmentVariables> zmienne środowiskowe](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) w dokumentacji dotyczącej usług IIS.

## <a name="configuration-sections-of-webconfig"></a>Sekcje konfiguracji web.config

Sekcje konfiguracyjne aplikacji ASP.NET 4. x w *web.config* nie są używane przez aplikacje ASP.NET Core do konfiguracji:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Aplikacje ASP.NET Core są konfigurowane przy użyciu innych dostawców konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Pule aplikacji

Izolacja puli aplikacji jest określana przez model hostingu:

* Hosting w procesie: aplikacje muszą być uruchamiane w oddzielnych pulach aplikacji.
* Hosting poza procesem: zalecamy izolowanie aplikacji od siebie, uruchamiając każdą aplikację w jej własnej puli aplikacji.

Okno dialogowe **Dodaj witrynę sieci Web** usług IIS domyślnie umożliwia pojedynczej puli aplikacji na aplikację. Po podaniu **nazwy witryny** tekst zostanie automatycznie przeniesiony do pola tekstowego **Pula aplikacji** . Nowa pula aplikacji jest tworzona przy użyciu nazwy lokacji, gdy zostanie dodana lokacja.

## <a name="application-pool-no-locidentity"></a>Pula aplikacji Identity

Konto tożsamości puli aplikacji umożliwia uruchamianie aplikacji na unikatowym koncie bez konieczności tworzenia domen ani kont lokalnych oraz zarządzania nimi. W przypadku usług IIS 8,0 lub nowszych proces roboczy administratora usług IIS tworzy konto wirtualne o nazwie nowej puli aplikacji i domyślnie uruchamia procesy robocze puli aplikacji w ramach tego konta. W konsoli zarządzania usługami IIS w obszarze **Ustawienia zaawansowane** dla puli aplikacji upewnij się, że **Identity** ustawiono opcję Użyj **ApplicationPool Identity **:

![Okno dialogowe Zaawansowane ustawienia puli aplikacji](index/_static/apppool-identity.png)

Proces zarządzania usługami IIS tworzy Bezpieczny identyfikator z nazwą puli aplikacji w systemie zabezpieczeń systemu Windows. Zasoby mogą być zabezpieczone przy użyciu tej tożsamości. Jednak ta tożsamość nie jest kontem użytkownika rzeczywistego i nie jest wyświetlana w konsoli zarządzania użytkownikami systemu Windows.

Jeśli proces roboczy usług IIS wymaga podwyższonego poziomu dostępu do aplikacji, Zmodyfikuj listę Access Control (ACL) dla katalogu zawierającego aplikację:

1. Otwórz Eksploratora Windows i przejdź do katalogu.

1. Kliknij prawym przyciskiem myszy katalog i wybierz polecenie **Właściwości**.

1. Na karcie **zabezpieczenia** wybierz przycisk **Edytuj** , a następnie przycisk **Dodaj** .

1. Wybierz przycisk **lokalizacje** i upewnij się, że wybrano system.

1. Wprowadź **<usług IIS puli aplikacji \\ app_pool_name>** w polu **Wprowadź nazwy obiektów do wybrania** . Wybierz przycisk **Sprawdź nazwy** . W obszarze *Domyślna pula aplikacji* Sprawdź nazwy za pomocą **usług IIS AppPool\DefaultAppPool**. Po wybraniu przycisku **Sprawdź nazwy** w obszarze nazwy obiektów zostanie wykazana wartość **Domyślna pula aplikacji** . Nie można wprowadzić nazwy puli aplikacji bezpośrednio w obszarze nazw obiektów. Podczas sprawdzania nazwy obiektu Użyj **<IIS puli aplikacji \\ app_pool_name>** .

   ![Okno dialogowe Wybieranie użytkowników lub grup dla folderu aplikacji: Nazwa puli aplikacji "domyślna pula" jest dołączana do "puli aplikacji IIS \" w obszarze nazw obiektów przed wybraniem pozycji" Sprawdź nazwy ".](index/_static/select-users-or-groups-1.png)

1. Kliknij przycisk **OK**.

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

W przypadku wdrożenia w procesie podczas ustanawiania połączenia HTTP/2 w raportach [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` . W przypadku wdrożenia poza procesem, gdy jest nawiązywane połączenie HTTP/2, raporty [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/1.1` .

Aby uzyskać więcej informacji na temat modeli hostingu w procesie i poza procesami, zobacz <xref:host-and-deploy/aspnet-core-module> .

Protokół HTTP/2 jest domyślnie włączony. Połączenia powracają do protokołu HTTP/1.1, jeśli połączenie HTTP/2 nie zostało ustanowione. Aby uzyskać więcej informacji na temat konfiguracji protokołu HTTP/2 z wdrożeniami usług IIS, zobacz [http/2 w usługach IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Żądania inspekcji wstępnej CORS

*Ta sekcja dotyczy tylko ASP.NET Core aplikacji przeznaczonych dla .NET Framework.*

W przypadku aplikacji ASP.NET Core, która jest przeznaczona dla .NET Framework, żądania opcji nie są domyślnie przesyłane do aplikacji w usługach IIS. Aby dowiedzieć się, jak skonfigurować obsługę usług IIS w *web.config* , aby przekazywać żądania dotyczące opcji, zobacz [Włączanie żądań między źródłami w ASP.NET Web API 2: jak działa mechanizm CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Moduł inicjalizacji aplikacji i limit czasu bezczynności

Hostowane w usługach IIS przez moduł ASP.NET Core w wersji 2:

* [Moduł inicjalizacji aplikacji](#application-initialization-module): hostowana [w procesie](#in-process-hosting-model) lub [poza procesem](#out-of-process-hosting-model) aplikacja może zostać skonfigurowana do automatycznego uruchamiania na potrzeby ponownego uruchomienia procesu roboczego lub ponownego uruchomienia serwera.
* [Limit czasu bezczynności](#idle-timeout): hostowana [w trakcie](#in-process-hosting-model) działania aplikacji można skonfigurować w taki sposób, aby nie przekroczyć limitu czasu podczas okresów braku aktywności.

### <a name="application-initialization-module"></a>Moduł inicjalizacji aplikacji

*Dotyczy aplikacji hostowanych w procesie i pozaprocesowe.*

[Inicjowanie aplikacji IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) to funkcja usług IIS, która wysyła do aplikacji żądanie HTTP, gdy pula aplikacji zostanie uruchomiona lub zostanie odtworzona. Żądanie wyzwala uruchomienie aplikacji. Domyślnie usługi IIS wysyłają żądanie do głównego adresu URL aplikacji ( `/` ) w celu zainicjowania aplikacji (zobacz [dodatkowe zasoby](#application-initialization-module-and-idle-timeout-additional-resources) , aby uzyskać więcej informacji na temat konfiguracji).

Upewnij się, że funkcja inicjowania roli inicjalizacji aplikacji IIS jest włączona:

Na komputerach z systemem Windows 7 lub nowszym w przypadku lokalnego korzystania z usług IIS:

1. Przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).
1. Otwórz **Internet Information Services** > **World Wide Web Services** > **funkcje projektowania aplikacji**Internet Information Services World Wide Web Services.
1. Zaznacz pole wyboru dla **inicjowania aplikacji**.

W systemie Windows Server 2008 R2 lub nowszym:

1. Otwórz **Kreatora dodawania ról i funkcji**.
1. W panelu **Wybierz usługi ról** Otwórz węzeł **Programowanie aplikacji** .
1. Zaznacz pole wyboru dla **inicjowania aplikacji**.

Użyj jednego z poniższych metod, aby włączyć moduł inicjowania aplikacji dla lokacji:

* Za pomocą Menedżera usług IIS:

  1. W panelu **połączenia** wybierz pozycję **Pule aplikacji** .
  1. Kliknij prawym przyciskiem myszy pulę aplikacji aplikacji na liście i wybierz pozycję **Ustawienia zaawansowane**.
  1. Domyślny **tryb uruchamiania** to **OnDemand**. Ustaw **tryb uruchamiania** na **AlwaysRunning**. Kliknij przycisk **OK**.
  1. Otwórz węzeł **Lokacje** w panelu **połączenia** .
  1. Kliknij prawym przyciskiem myszy aplikację i wybierz pozycję Zarządzaj ustawieniami zaawansowanymi **witryny sieci Web** > **Advanced Settings**.
  1. Domyślnym ustawieniem **wstępnego ładowania** jest **wartość false**. Ustaw dla opcji **wstępnego ładowania** **wartość true**. Kliknij przycisk **OK**.

* Za pomocą *web.config*Dodaj `<applicationInitialization>` element z `doAppInitAfterRestart` ustawionym do `true` `<system.webServer>` elementów w pliku *web.config* aplikacji:

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
1. Domyślny **limit czasu bezczynności (w minutach)** wynosi **20** minut. Ustaw **limit czasu bezczynności (w minutach)** na **0** (zero). Kliknij przycisk **OK**.
1. Odtwórz proces roboczy.

Aby zapobiec przekroczeniu limitu [czasu hostowanych przez aplikacje](#out-of-process-hosting-model) aplikacji, użyj jednej z następujących metod:

* Wyślij polecenie ping do aplikacji z zewnętrznej usługi, aby było ono uruchomione.
* Jeśli aplikacja obsługuje tylko usługi w tle, należy unikać hostingu usług IIS i używać [usługi systemu Windows do hostowania aplikacji ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Dodatkowe zasoby dotyczące modułu inicjalizacji aplikacji i limitu czasu bezczynności

* [Inicjowanie aplikacji usług IIS 8,0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Inicjowanie \<applicationInitialization> aplikacji ](/iis/configuration/system.webserver/applicationinitialization/).
* [Ustawienia modelu procesów dla \<processModel> puli aplikacji ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="deployment-resources-for-iis-administrators"></a>Zasoby wdrażania dla administratorów usług IIS

* [Dokumentacja usług IIS](/iis)
* [Wprowadzenie z menedżerem usług IIS w usługach IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Wdrażanie aplikacji .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:test/troubleshoot>
* <xref:index>
* [Oficjalna witryna programu Microsoft IIS](https://www.iis.net/)
* [Biblioteka zawartości technicznej systemu Windows Server](/windows-server/windows-server)
* [Protokół HTTP/2 w usługach IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Aby zapoznać się z samouczkiem dotyczącym publikowania aplikacji ASP.NET Core na serwerze usług IIS, zobacz <xref:tutorials/publish-to-iis> .

[Zainstaluj pakiet hostingu platformy .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Obsługiwane są następujące systemy operacyjne:

* System Windows 7 lub nowszy
* Windows Server 2008 R2 lub nowszy

[ SerwerHTTP.sys](xref:fundamentals/servers/httpsys) (znany wcześniej jako webListener) nie działa w konfiguracji zwrotnego serwera proxy z usługami IIS. Użyj [serwera Kestrel](xref:fundamentals/servers/kestrel).

Aby uzyskać informacje na temat hostingu na platformie Azure, zobacz <xref:host-and-deploy/azure-apps/index> .

Aby uzyskać wskazówki dotyczące rozwiązywania problemów, zobacz <xref:test/troubleshoot> .

## <a name="supported-platforms"></a>Obsługiwane platformy

Obsługiwane są aplikacje opublikowane dla wdrożenia 32-bitowego (x86) lub 64-bitowego (x64). Wdróż aplikację 32-bitową z 32-bitową (x86) zestaw .NET Core SDK, chyba że aplikacja:

* Wymagana jest większa przestrzeń adresów pamięci wirtualnej dla aplikacji 64-bitowej.
* Wymaga większego rozmiaru stosu IIS.
* Ma 64-bitowe zależności natywne.

Aby opublikować aplikację 64-bitową, należy użyć 64-bitowej (x64) zestaw .NET Core SDK. 64-bitowy środowisko uruchomieniowe musi być obecny w systemie hosta.

ASP.NET Core jest dostarczany z [serwerem Kestrel](xref:fundamentals/servers/kestrel), a domyślnym serwerem HTTP na wielu platformach.

W przypadku korzystania z [usług IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikacja działa w procesie innym niż proces roboczy usług IIS (*poza procesem*) z [serwerem Kestrel](xref:fundamentals/servers/index#kestrel).

Ponieważ ASP.NET Core aplikacje działają w procesie innym niż proces roboczy usług IIS, moduł obsługuje zarządzanie procesami. Moduł uruchamia proces dla aplikacji ASP.NET Core, gdy pierwsze żądanie zostanie odebrane i ponownie uruchomiony, jeśli zostanie zamknięty lub ulegnie awarii. Jest to zasadniczo takie samo zachowanie jak w przypadku aplikacji uruchamianych w procesie, które są zarządzane przez [usługę aktywacji procesów systemu Windows (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Na poniższym diagramie przedstawiono relację między usługami IIS, modułem ASP.NET Core i hostowanym przez aplikację aplikacją:

![Moduł ASP.NET Core](index/_static/ancm-outofprocess.png)

Żądania docierają z sieci Web do sterownika HTTP.sys trybu jądra. Sterownik kieruje żądania do usług IIS na skonfigurowanym porcie witryny sieci Web, zwykle 80 (HTTP) lub 443 (HTTPS). Moduł przekazuje żądania do Kestrel na losowo wybranym porcie dla aplikacji, która nie jest portem 80 lub 443.

Moduł określa port za pośrednictwem zmiennej środowiskowej podczas uruchamiania, a [oprogramowanie pośredniczące integracji usług IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguruje serwer do nasłuchiwania `http://localhost:{port}` . Dodatkowe sprawdzenia są wykonywane, a żądania, które nie pochodzą z modułu, są odrzucane. Moduł nie obsługuje przekazywania HTTPS, dlatego żądania są przekazywane przez protokół HTTP nawet wtedy, gdy są odbierane przez usługę IIS przez protokół HTTPS.

Po podaniu przez Kestrel żądania z modułu żądanie jest wypychane do potoku ASP.NET Core pośredniczącego. Potok oprogramowania pośredniczącego obsługuje żądanie i przekazuje go jako `HttpContext` wystąpienie do logiki aplikacji. Oprogramowanie pośredniczące dodane przez integrację usług IIS aktualizuje schemat, zdalny adres IP i pathbase, aby można było przesłać żądanie do Kestrel. Odpowiedź aplikacji jest przesyłana z powrotem do usług IIS, która wypycha ją z powrotem do klienta HTTP, który zainicjował żądanie.

`CreateDefaultBuilder` Konfiguruje serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci Web i włącza INTEGRACJĘ usług IIS przez skonfigurowanie ścieżki podstawowej i portu dla [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

Moduł ASP.NET Core generuje port dynamiczny do przypisania do procesu zaplecza. `CreateDefaultBuilder` wywołuje <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> metodę. `UseIISIntegration` konfiguruje Kestrel do nasłuchiwania na porcie dynamicznym przy użyciu adresu IP hosta lokalnego ( `127.0.0.1` ). Jeśli port dynamiczny to 1234, Kestrel nasłuchuje na `127.0.0.1:1234` . Ta konfiguracja zastępuje inne konfiguracje URL dostarczone przez:

* `UseUrls`
* [Interfejs API Listen Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Konfiguracja](xref:fundamentals/configuration/index) (lub [Opcja wiersza polecenia--URL](xref:fundamentals/host/web-host#override-configuration))

Wywołania `UseUrls` interfejsu API lub Kestrel `Listen` nie są wymagane w przypadku korzystania z modułu. Jeśli `UseUrls` lub `Listen` jest wywoływana, Kestrel nasłuchuje na porcie określonym tylko podczas uruchamiania aplikacji bez usług IIS.

Aby uzyskać wskazówki dotyczące konfiguracji modułu ASP.NET Core, zobacz <xref:host-and-deploy/aspnet-core-module> .

Aby uzyskać więcej informacji na temat hostingu, zobacz [host in ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Konfiguracja aplikacji

### <a name="enable-the-iisintegration-components"></a>Włącz składniki IISIntegration

Podczas kompilowania hosta w `CreateWebHostBuilder` (*program.cs*), wywołaj, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> Aby włączyć integrację usług IIS:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Aby uzyskać więcej informacji na temat `CreateDefaultBuilder` , zobacz <xref:fundamentals/host/web-host#set-up-a-host> .

### <a name="iis-options"></a>Opcje usług IIS

| Opcja                         | Domyślny | Ustawienie |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Jeśli `true` serwer usług IIS ustawi uwierzytelnienie `HttpContext.User` za pomocą [uwierzytelniania systemu Windows](xref:security/authentication/windowsauth). Jeśli `false` serwer zawiera tylko tożsamość dla `HttpContext.User` i reaguje na wyzwania, gdy zostanie jawnie zlecony przez `AuthenticationScheme` . Aby program mógł działać, należy włączyć uwierzytelnianie systemu Windows `AutomaticAuthentication` . Aby uzyskać więcej informacji, zobacz [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Ustawia nazwę wyświetlaną pokazywaną użytkownikom na stronach logowania. |

Aby skonfigurować opcje usług IIS, należy uwzględnić <xref:Microsoft.AspNetCore.Builder.IISOptions> w programie konfigurację usługi <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> . Poniższy przykład uniemożliwia wypełnianie aplikacji `HttpContext.Connection.ClientCertificate` :

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Opcja                         | Domyślny | Ustawienie |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Jeśli `true` [oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components) ustawi uwierzytelnienie `HttpContext.User` za pomocą [uwierzytelniania systemu Windows](xref:security/authentication/windowsauth). Jeśli `false` oprogramowanie pośredniczące zapewnia tylko tożsamość `HttpContext.User` i reaguje na wyzwania, gdy zostanie jawnie zażądana przez program `AuthenticationScheme` . Aby program mógł działać, należy włączyć uwierzytelnianie systemu Windows `AutomaticAuthentication` . Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie systemu Windows](xref:security/authentication/windowsauth) . |
| `AuthenticationDisplayName`    | `null`  | Ustawia nazwę wyświetlaną pokazywaną użytkownikom na stronach logowania. |
| `ForwardClientCertificate`     | `true`  | Jeśli `true` `MS-ASPNETCORE-CLIENTCERT` jest obecny nagłówek żądania, `HttpContext.Connection.ClientCertificate` zostanie wypełniony. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Scenariusze serwera proxy i modułu równoważenia obciążenia

[Oprogramowanie pośredniczące integracji usług IIS](#enable-the-iisintegration-components), które konfiguruje przekazane nagłówki oprogramowania pośredniczącego, a moduł ASP.NET Core jest skonfigurowany do przesyłania dalej schematu (http/https) i zdalnego adresu IP, z którego pochodzi żądanie. W przypadku aplikacji hostowanych za dodatkowymi serwerami proxy i modułami równoważenia obciążenia może być wymagana dodatkowa konfiguracja. Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>Plik web.config

Plik *web.config* konfiguruje [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Tworzenie, przekształcanie i publikowanie pliku *web.config* jest obsługiwane przez obiekt docelowy MSBuild ( `_TransformWebConfig` ), gdy projekt jest publikowany. Ten element docelowy znajduje się w obiektach docelowych zestawu SDK sieci Web ( `Microsoft.NET.Sdk.Web` ). Zestaw SDK jest ustawiany u góry pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Jeśli plik *web.config* nie jest obecny w projekcie, plik zostanie utworzony z prawidłowymi *argumentami* *ProcessPath* i, aby skonfigurować moduł ASP.NET Core i przeniesiony do [publikowanych danych wyjściowych](xref:host-and-deploy/directory-structure).

Jeśli plik *web.config* jest obecny w projekcie, plik zostanie przekształcony przy użyciu poprawnych *processPath* i *argumentów* w celu skonfigurowania modułu ASP.NET Core i przeniesiona do publikowanych danych wyjściowych. Transformacja nie modyfikuje ustawień konfiguracji usług IIS w pliku.

Plik *web.config* może zapewnić dodatkowe ustawienia konfiguracji usług IIS kontrolujące aktywne moduły usług IIS. Aby uzyskać informacje na temat modułów usług IIS, które mogą przetwarzać żądania z aplikacjami ASP.NET Core, zobacz temat [moduły usług IIS](xref:host-and-deploy/iis/modules) .

Aby zapobiec transformacji pliku *web.config* przez zestaw SDK sieci Web, należy użyć **\<IsTransformWebConfigDisabled>** właściwości w pliku projektu:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Podczas wyłączania pliku zestawu SDK sieci Web, *processPath* i *argumenty* powinny być ustawiane ręcznie przez dewelopera. Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>Lokalizacja pliku web.config

W celu poprawnego skonfigurowania [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) plik *web.config* musi znajdować się w ścieżce [katalogu głównego zawartości](xref:fundamentals/index#content-root) (zazwyczaj ścieżka podstawowa aplikacji) wdrożonej aplikacji. Ta sama lokalizacja jest taka sama jak ścieżka fizyczna witryny sieci Web dostarczana do usług IIS. Plik *web.config* jest wymagany w katalogu głównym aplikacji, aby umożliwić Publikowanie wielu aplikacji przy użyciu Web Deploy.

Poufne pliki znajdują się w ścieżce fizycznej aplikacji, na przykład * \<assembly>.runtimeconfig.jsna*, * \<assembly> . XML* (Komentarze dokumentacji XML) i * \<assembly>.deps.js*. Gdy plik *web.config* jest obecny, a lokacja jest uruchamiana normalnie, usługi IIS nie będą obsługiwały tych poufnych plików, jeśli są żądane. Jeśli brakuje pliku *web.config* , nazwa jest nieprawidłowa lub nie można skonfigurować lokacji do normalnego uruchamiania, usługi IIS mogą publicznie udostępniać poufne pliki.

**Plik *web.config* musi być obecny we wdrożeniu przez cały czas, prawidłowo nazwany i można skonfigurować lokację pod kątem normalnego uruchamiania. Nigdy nie usuwaj pliku *web.config* z wdrożenia produkcyjnego.**

### <a name="transform-webconfig"></a>Przekształcanie pliku web.config

Jeśli musisz przekształcić *web.config* przy publikowaniu (na przykład ustawić zmienne środowiskowe na podstawie konfiguracji, profilu lub środowiska), zobacz <xref:host-and-deploy/iis/transform-webconfig> .

## <a name="iis-configuration"></a>Konfiguracja usług IIS

**Systemy operacyjne Windows Server**

Włącz rolę serwera **serwera sieci Web (IIS)** i Ustanów usługi ról.

1. Użyj kreatora **dodawania ról i funkcji** z menu **Zarządzaj** lub łącza w **Menedżer serwera**. W kroku **role serwera** zaznacz pole wyboru **serwer sieci Web (IIS)**.

   ![W kroku wybierz role serwera zostanie wybrana rola IIS serwera sieci Web.](index/_static/server-roles-ws2016.png)

1. Po wykonaniu **kroków krok po kroku** **usługi ról** są ładowane dla serwera sieci Web (IIS). Wybierz żądane usługi roli IIS lub zaakceptuj domyślne usługi ról.

   ![Domyślne usługi ról są wybrane w kroku Wybierz usługi ról.](index/_static/role-services-ws2016.png)

   **Uwierzytelnianie systemu Windows (opcjonalnie)**  
   Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **Zabezpieczenia serwera sieci Web**  >  **Security**. Wybierz funkcję **uwierzytelniania systemu Windows** . Aby uzyskać więcej informacji, zobacz [uwierzytelnianie \<windowsAuthentication> systemu Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

   **Obiekty WebSockets (opcjonalnie)**  
   Obiekty WebSockets są obsługiwane w ASP.NET Core 1,1 lub nowszych. Aby włączyć obiekty WebSockets, rozwiń następujące węzły: projektowanie aplikacji **serwera sieci Web**  >  **Application Development**. Wybierz funkcję **protokołu WebSocket** . Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).

1. Przejdź do kroku **potwierdzenia** , aby zainstalować rolę i usługi serwera sieci Web. Po zainstalowaniu roli **serwera sieci Web (IIS)** nie jest wymagane ponowne uruchomienie serwera ani usług IIS.

**Systemy operacyjne Windows dla komputerów stacjonarnych**

Włącz **konsolę zarządzania usług IIS** i **usługi World Wide Web**.

1. Przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje** > **Włącz lub wyłącz funkcje systemu Windows** (po lewej stronie ekranu).

1. Otwórz węzeł **Internet Information Services** . Otwórz węzeł **Narzędzia do zarządzania siecią Web** .

1. Zaznacz pole wyboru w obszarze **Konsola zarządzania usługami IIS**.

1. Zaznacz pole wyboru **usług World Wide Web Services**.

1. Zaakceptuj domyślne funkcje **usług World Wide Web Services** lub Dostosuj funkcje usług IIS.

   **Uwierzytelnianie systemu Windows (opcjonalnie)**  
   Aby włączyć uwierzytelnianie systemu Windows, rozwiń następujące węzły: **World Wide Web usług**  >  **zabezpieczenia**. Wybierz funkcję **uwierzytelniania systemu Windows** . Aby uzyskać więcej informacji, zobacz [uwierzytelnianie \<windowsAuthentication> systemu Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) i [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

   **Obiekty WebSockets (opcjonalnie)**  
   Obiekty WebSockets są obsługiwane w ASP.NET Core 1,1 lub nowszych. Aby włączyć obiekty WebSockets, rozwiń następujące węzły: **World Wide Web Services**  >  **Application Development Features**. Wybierz funkcję **protokołu WebSocket** . Aby uzyskać więcej informacji, zobacz [WebSockets](xref:fundamentals/websockets).

1. Jeśli instalacja usług IIS wymaga ponownego uruchomienia, należy ponownie uruchomić system.

![Konsola zarządzania usługami IIS i usługi World Wide Web są wybrane w funkcjach systemu Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Zainstaluj pakiet hostingu platformy .NET Core

Zainstaluj *pakiet hostingu platformy .NET Core* w systemie hostingu. Pakiet instaluje środowisko uruchomieniowe programu .NET Core, bibliotekę .NET Core i [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Moduł umożliwia uruchamianie aplikacji ASP.NET Core za pomocą usług IIS.

> [!IMPORTANT]
> Jeśli pakiet hostingu jest zainstalowany przed usługami IIS, należy naprawić pakiet instalacyjny. Uruchom ponownie Instalatora pakietu hostingu po zainstalowaniu usług IIS.
>
> Jeśli pakiet hostingu jest instalowany po zainstalowaniu 64-bitowej (x64) wersji programu .NET Core, prawdopodobnie brakuje zestawów SDK ([nie wykryto żadnych zestawów SDK dla platformy .NET Core](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Aby rozwiązać ten problem, zobacz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> .

### <a name="download"></a>Pobierz

1. Przejdź do strony [pobieranie platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .
1. Wybierz żądaną wersję programu .NET Core.
1. W kolumnie **Uruchom aplikacje — środowisko uruchomieniowe** Znajdź wiersz żądanej wersji środowiska uruchomieniowego platformy .NET Core.
1. Pobierz instalatora przy użyciu linku do **pakietu hostingu** .

> [!WARNING]
> Niektóre Instalatory zawierają wersje wydania, które osiągnęły koniec cyklu życia (EOL) i nie są już obsługiwane przez firmę Microsoft. Aby uzyskać więcej informacji, zobacz [zasady pomocy technicznej](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Zainstaluj pakiet hostingu

1. Uruchom Instalatora na serwerze. Następujące parametry są dostępne podczas uruchamiania Instalatora z powłoki poleceń administratora:

   * `OPT_NO_ANCM=1`: Pomiń Instalowanie modułu ASP.NET Core.
   * `OPT_NO_RUNTIME=1`: Pomiń Instalowanie środowiska uruchomieniowego platformy .NET Core. Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1`: Pomiń instalację ASP.NET Shared Framework (ASP.NET Runtime). Używany, gdy serwer obsługuje tylko [wdrożenia samodzielne (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1`: Pomiń Instalowanie środowiska uruchomieniowego x86. Użyj tego parametru, Jeśli wiesz, że nie będziesz hostować aplikacji 32-bitowych. Jeśli w przyszłości będziesz hostować zarówno aplikacje 32-bitowe, jak i 64-bitowe, nie używaj tego parametru i zainstaluj oba środowiska uruchomieniowe.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: Wyłącz sprawdzanie przy użyciu konfiguracji udostępnionej usług IIS, gdy konfiguracja udostępniona (*applicationHost.config*) znajduje się na tym samym komputerze, na którym zainstalowano program IIS. *Dostępne tylko w przypadku ASP.NET Core 2,2 lub nowszych instalatorów pakietu do obsługi.* Aby uzyskać więcej informacji, zobacz <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Uruchom ponownie system lub wykonaj następujące polecenia w powłoce poleceń:

   ```console
   net stop was /y
   net start w3svc
   ```
   Ponowne uruchomienie usług IIS powoduje zmianę ścieżki systemowej, która jest zmienną środowiskową, wykonaną przez Instalatora.

Nie jest konieczne ręczne zatrzymanie poszczególnych lokacji w usługach IIS podczas instalowania pakietu hostingu. Aplikacje hostowane (lokacje IIS) są ponownie uruchamiane po ponownym uruchomieniu usług IIS. Aplikacje są uruchamiane ponownie po otrzymaniu pierwszego żądania, w tym od [modułu inicjalizacji aplikacji](#application-initialization-module-and-idle-timeout).

ASP.NET Core przyjmuje zachowanie funkcji przekazywania do przodu dla wydań poprawek udostępnionych pakietów platformy. Po ponownym uruchomieniu aplikacji hostowanych przez usługi IIS przy użyciu usług IIS aplikacje są ładowane z najnowszymi wersjami poprawki pakietów, do których się odwołują, gdy otrzymają swoje pierwsze żądanie. Jeśli usługi IIS nie zostaną ponownie uruchomione, aplikacje ponownie uruchamiają się i wykazują zachowanie przekazujące, gdy procesy robocze są odtwarzane i otrzymują swoje pierwsze żądanie.

> [!NOTE]
> Aby uzyskać informacje na temat konfiguracji udostępnionej usług IIS, zobacz [ASP.NET Core Module z udostępnioną konfiguracją usług IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Zainstaluj Web Deploy przy publikowaniu w programie Visual Studio

Podczas wdrażania aplikacji na serwerach z [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)Zainstaluj najnowszą wersję Web Deploy na serwerze. Aby zainstalować Web Deploy, należy użyć [Instalatora platformy sieci Web (Instalatora WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) lub uzyskać instalatora bezpośrednio z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=43717). Preferowaną metodą jest użycie Instalatora WebPI. Instalatora WebPI oferuje autonomiczną konfigurację i konfigurację dla dostawców hostingu.

## <a name="create-the-iis-site"></a>Tworzenie witryny usług IIS

1. W systemie hostingu Utwórz folder, który będzie zawierać opublikowane foldery i pliki aplikacji. W poniższym kroku ścieżka folderu jest dostarczana do usług IIS jako ścieżka fizyczna do aplikacji. Aby uzyskać więcej informacji na temat folderu wdrożenia aplikacji i układu pliku, zobacz <xref:host-and-deploy/directory-structure> .

1. W Menedżerze usług IIS Otwórz węzeł serwera w panelu **połączenia** . Kliknij prawym przyciskiem myszy folder **sitess** . Wybierz pozycję **Dodaj witrynę internetową** z menu kontekstowego.

1. Podaj **nazwę lokacji** i ustaw **ścieżkę fizyczną** folderu wdrożenia aplikacji. Podaj konfigurację **powiązania** i Utwórz witrynę sieci Web, wybierając **przycisk OK**:

   ![Podaj nazwę lokacji, ścieżkę fizyczną i nazwę hosta w kroku Dodaj witrynę sieci Web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > `http://*:80/` `http://+:80` **Nie** należy używać powiązań z symbolami wieloznacznymi (i) najwyższego poziomu. Powiązania z symbolami wieloznacznymi najwyższego poziomu mogą otwierać aplikację pod kątem luk w zabezpieczeniach. Dotyczy to zarówno silnych, jak i słabych symboli wieloznacznych. Używaj jawnych nazw hostów zamiast symboli wieloznacznych. Powiązanie symboli wieloznacznych z poddomeną (na przykład `*.mysub.com` ) nie ma tego ryzyka bezpieczeństwa, jeśli kontrolujesz całą domenę nadrzędną (w przeciwieństwie do `*.com` , która jest narażona). Aby uzyskać więcej informacji, zobacz [sekcję rfc7230-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .

1. W węźle serwera wybierz pozycję **Pule aplikacji**.

1. Kliknij prawym przyciskiem myszy pulę aplikacji lokacji i wybierz pozycję **Ustawienia podstawowe** z menu kontekstowego.

1. W oknie **Edytowanie puli aplikacji** Ustaw **wersję środowiska .NET CLR** na **Brak kodu zarządzanego**:

   ![Nie ustawiaj kodu zarządzanego dla wersji środowiska .NET CLR.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core działa w osobnym procesie i zarządza środowiskiem uruchomieniowym. ASP.NET Core nie polega na ładowaniu środowiska CLR (.NET CLR) &mdash; podstawowego środowiska uruchomieniowego języka wspólnego (CoreCLR) dla platformy .NET Core w celu hostowania aplikacji w procesie roboczym. Ustawienie **wersji środowiska .NET CLR** na **Brak kodu zarządzanego** jest opcjonalne, ale zalecane.

1. *ASP.NET Core 2,2 lub nowsza*: w przypadku [wdrożenia](/dotnet/core/deploying/#self-contained-deployments-scd) z systemem 64-bitowym (x64), które korzysta z [modelu hostingu w procesie](#in-process-hosting-model), wyłączaj pulę aplikacji dla procesów 32-bit (x86).

   Na pasku bocznym **Akcje** Menedżera usług IIS > **Pule aplikacji**wybierz pozycję **Ustaw ustawienia domyślne puli aplikacji** lub **Zaawansowane**. Znajdź **opcję Włącz aplikacje 32-bitowe** i ustaw wartość na `False` . To ustawienie nie ma wpływu na aplikacje wdrożone na potrzeby [hostingu poza procesem](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).

1. Potwierdź, że tożsamość modelu procesu ma odpowiednie uprawnienia.

   Jeśli domyślna tożsamość puli aplikacji (**model procesów**  >  **Identity** ) została zmieniona z **ApplicationPool Identity ** na inną tożsamość, sprawdź, czy Nowa tożsamość ma wymagane uprawnienia dostępu do folderu, bazy danych i innych wymaganych zasobów aplikacji. Na przykład Pula aplikacji wymaga dostępu do odczytu i zapisu do folderów, w których aplikacja odczytuje i zapisuje pliki.

**Konfiguracja uwierzytelniania systemu Windows (opcjonalnie)**  
Aby uzyskać więcej informacji, zobacz [Konfigurowanie uwierzytelniania systemu Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

Wdróż aplikację w folderze **ścieżki fizycznej** usług IIS, który został ustanowiony w sekcji [Tworzenie witryny usług IIS](#create-the-iis-site) . [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) jest zalecanym mechanizmem wdrażania, ale istnieje kilka opcji przeniesienia aplikacji z folderu *publikowania* projektu do folderu wdrożenia systemu hostingu.

### <a name="web-deploy-with-visual-studio"></a>Web Deploy z programem Visual Studio

Aby dowiedzieć się, jak utworzyć profil publikowania do użytku z usługą Web Deploy, zobacz temat [Profile publikacji programu Visual Studio dla wdrożenia aplikacji ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) . Jeśli dostawca hostingu udostępnia profil publikowania lub obsługę tworzenia aplikacji, pobierz swój profil i zaimportuj go za pomocą okna dialogowego **publikowania** programu Visual Studio:

![Strona okna dialogowego publikowanie](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Web Deploy poza programem Visual Studio

[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) można również użyć poza programem Visual Studio z wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Narzędzie do wdrażania w sieci Web](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternatywy dla Web Deploy

Użyj dowolnej z kilku metod, aby przenieść aplikację do systemu hostingu, takich jak ręczna kopia, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)lub [PowerShell](/powershell/).

Aby uzyskać więcej informacji na ASP.NET Core wdrażania usług IIS, zobacz sekcję [zasoby wdrażania dla administratorów usług IIS](#deployment-resources-for-iis-administrators) .

## <a name="browse-the-website"></a>Przeglądanie witryny sieci Web

Po wdrożeniu aplikacji w systemie hostingu należy wysłać żądanie do jednej z publicznych punktów końcowych aplikacji.

W poniższym przykładzie lokacja jest powiązana z **nazwą hosta** usług IIS `www.mysite.com` na **porcie** `80` . Zostanie wysłane żądanie `http://www.mysite.com` :

![Przeglądarka Microsoft Edge załadowała stronę startową usług IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Zablokowane pliki wdrożenia

Pliki w folderze wdrożenia są zablokowane, gdy aplikacja jest uruchomiona. Nie można zastąpić zablokowanych plików podczas wdrażania. Aby zwolnić zablokowane pliki we wdrożeniu, Zatrzymaj pulę aplikacji, korzystając z **jednej** z następujących metod:

* Użyj Web Deploy i Reference `Microsoft.NET.Sdk.Web` w pliku projektu. Plik *app_offline.htm* jest umieszczony w katalogu głównym katalogu aplikacji sieci Web. Gdy plik jest obecny, moduł ASP.NET Core bezpiecznie zamyka aplikację i obsługuje plik *app_offline.htm* podczas wdrażania. Aby uzyskać więcej informacji, zobacz [Informacje o konfiguracji modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Ręcznie Zatrzymaj pulę aplikacji w Menedżerze usług IIS na serwerze.
* Użyj programu PowerShell do porzucenia *app_offline.htm* (wymaga programu PowerShell 5 lub nowszego):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Ochrona danych

[Stos ochrony danych ASP.NET Core](xref:security/data-protection/introduction) jest używany przez kilka ASP.NET Core [middlewares](xref:fundamentals/middleware/index), w tym oprogramowanie pośredniczące używane podczas uwierzytelniania. Nawet jeśli interfejsy API ochrony danych nie są wywoływane przez kod użytkownika, należy skonfigurować ochronę danych przy użyciu skryptu wdrożenia lub w kodzie użytkownika, aby utworzyć trwały [Magazyn kluczy](xref:security/data-protection/implementation/key-management)kryptograficznych. Jeśli ochrona danych nie jest skonfigurowana, klucze są przechowywane w pamięci i usuwane po ponownym uruchomieniu aplikacji.

Jeśli pierścień kluczy jest przechowywany w pamięci po ponownym uruchomieniu aplikacji:

* cookieTokeny uwierzytelniania na podstawie wszystkich są unieważnione. 
* Użytkownicy muszą ponownie zalogować się przy następnym żądaniu. 
* Nie można już odszyfrować żadnych danych chronionych za pomocą dzwonka klucza. Może to obejmować [tokeny CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) i [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata).

Aby skonfigurować ochronę danych w ramach usług IIS w celu utrwalenia pierścienia kluczy, należy użyć **jednej** z następujących metod:

* **Tworzenie kluczy rejestru ochrony danych**

  Klucze ochrony danych używane przez aplikacje ASP.NET Core są przechowywane w rejestrze zewnętrznym dla aplikacji. Aby utrzymać klucze dla danej aplikacji, należy utworzyć klucze rejestru dla puli aplikacji.

  W przypadku autonomicznych, nieopartych na webfarmie instalacji usług IIS [skrypt ochrony danych Provision-AutoGenKeys.ps1 programu PowerShell](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) może być używany dla każdej puli aplikacji używanej w aplikacji ASP.NET Core. Ten skrypt tworzy klucz rejestru w rejestrze HKLM, który jest dostępny tylko dla konta procesu roboczego puli aplikacji aplikacji. Klucze są szyfrowane przy użyciu funkcji DPAPI z kluczem dla całego komputera.

  W scenariuszach farmy sieci Web aplikacja może być skonfigurowana pod kątem używania ścieżki UNC do przechowywania tego dzwonka klucza ochrony danych. Domyślnie klucze ochrony danych nie są szyfrowane. Upewnij się, że uprawnienia do pliku dla udziału sieciowego są ograniczone do konta systemu Windows, w którym jest uruchamiana aplikacja. Certyfikat x509 może służyć do ochrony kluczy w spoczynku. Rozważ zastosowanie mechanizmu umożliwiającego użytkownikom przekazywanie certyfikatów: Umieść certyfikaty w zaufanym magazynie certyfikatów użytkownika i upewnij się, że są one dostępne na wszystkich komputerach, na których działa aplikacja użytkownika. Szczegóły można znaleźć w temacie [Konfigurowanie ochrony danych ASP.NET Core](xref:security/data-protection/configuration/overview) .

* **Konfigurowanie puli aplikacji usług IIS w celu załadowania profilu użytkownika**

  To ustawienie znajduje się w sekcji **model procesów** w obszarze **Ustawienia zaawansowane** dla puli aplikacji. Ustaw **Załaduj profil użytkownika** na `True` . Po ustawieniu opcji `True` klucze są przechowywane w katalogu profilu użytkownika i chronione przy użyciu funkcji DPAPI z kluczem specyficznym dla konta użytkownika. Klucze są utrwalane w folderze *% LocalAppData%/ASP.NET/dataprotection-Keys* .

  [Atrybut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) puli aplikacji również musi być włączony. Wartość domyślna `setProfileEnvironment` to `true` . W niektórych scenariuszach (na przykład system operacyjny Windows) `setProfileEnvironment` jest ustawiony na `false` . Jeśli klucze nie są przechowywane w katalogu profilu użytkownika zgodnie z oczekiwaniami:

  1. Przejdź do folderu *% windir%/system32/inetsrv/config*
  1. Otwórz plik *applicationHost.config* .
  1. Znajdź `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.
  1. Upewnij się, że `setProfileEnvironment` atrybut nie istnieje, który jest wartością domyślną `true` , lub jawnie ustaw wartość atrybutu na `true` .

* **Używanie systemu plików jako magazynu kluczy**

  Dostosuj kod aplikacji, [tak aby używał systemu plików jako magazynu kluczy](xref:security/data-protection/configuration/overview). Użyj certyfikatu x509 do ochrony pierścienia kluczy i upewnij się, że certyfikat jest certyfikatem zaufanym. Jeśli certyfikat jest podpisany z podpisem własnym, umieść certyfikat w zaufanym magazynie głównym.

  W przypadku korzystania z usług IIS w kolektywie serwerów sieci Web:

  * Użyj udziału plików, do którego mają dostęp wszystkie maszyny.
  * Wdróż certyfikat x509 na każdym komputerze. Konfigurowanie [ochrony danych w kodzie](xref:security/data-protection/configuration/overview).

* **Ustawianie zasad dla całej maszyny na potrzeby ochrony danych**

  System ochrony danych ma ograniczoną obsługę ustawiania domyślnych [zasad komputera](xref:security/data-protection/configuration/machine-wide-policy) dla wszystkich aplikacji korzystających z interfejsów API ochrony danych. Aby uzyskać więcej informacji, zobacz <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Katalogi wirtualne

[Katalogi wirtualne usług IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) nie są obsługiwane w aplikacjach ASP.NET Core. Aplikacja może być hostowana jako [podaplikacja](#sub-applications).

## <a name="sub-applications"></a>Aplikacje podrzędne

Aplikacja ASP.NET Core może być hostowana jako [podaplikacja usług IIS (podrzędna)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications). Ścieżka aplikacji podrzędnej jest częścią adresu URL aplikacji głównej.

Aplikacja podrzędna nie powinna zawierać modułu ASP.NET Core jako programu obsługi. Jeśli moduł zostanie dodany jako program obsługi w pliku *web.config* aplikacji podrzędnej, *wystąpi błąd wewnętrzny serwera 500,19* odwołujący się do uszkodzonego pliku konfiguracji podczas próby przeglądania aplikacji podrzędnej.

Poniższy przykład przedstawia opublikowany plik *web.config* dla ASP.NET Core aplikacji podrzędnej:

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

W przypadku hostowania aplikacji podrzędnej non-ASP.NET Core w aplikacji ASP.NET Core jawnie Usuń dziedziczoną procedurę obsługi w pliku *web.config* aplikacji podrzędnej:

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

Linki do zasobów statycznych w aplikacji podrzędnej powinny używać notacji z ukośnikiem ( `~/` ). Notacja "ukośnik" wyzwala [pomocnika tagów](xref:mvc/views/tag-helpers/intro) , aby dołączyć pathbase podaplikacji do renderowanego linku względnego. W przypadku aplikacji podrzędnej w `/subapp_path` programie obraz połączony z programem `src="~/image.png"` jest renderowany jako `src="/subapp_path/image.png"` . Oprogramowanie pośredniczące aplikacji głównej nie przetwarza żądania pliku statycznego. Żądanie jest przetwarzane przez oprogramowanie pośredniczące plików statycznych aplikacji podrzędnej.

Jeśli atrybut statycznego zasobu `src` jest ustawiony na ścieżkę bezwzględną (na przykład `src="/image.png"` ), link jest renderowany bez pathbase podaplikacji. Oprogramowanie pośredniczące pliku statycznego aplikacji głównej próbuje obsłużyć zasób z poziomu [głównego katalogu sieci Web](xref:fundamentals/index#web-root)aplikacji głównej, co spowoduje, że odpowiedź na *404 nie zostanie znaleziona* , chyba że statyczny zasób jest dostępny z poziomu aplikacji głównej.

Aby hostować aplikację ASP.NET Core jako aplikację podrzędną w innej aplikacji ASP.NET Core:

1. Ustanów pulę aplikacji dla aplikacji podrzędnej. Ustaw **wersję środowiska .NET CLR** na **Brak kodu zarządzanego** , ponieważ podstawowe środowisko uruchomieniowe języka wspólnego (CoreCLR) dla platformy .NET Core jest uruchomione w celu hostowania aplikacji w procesie roboczym, a nie na pulpicie CLR (.NET CLR).

1. Dodaj lokację główną w Menedżerze usług IIS przy użyciu aplikacji podrzędnej w folderze w lokacji głównej.

1. Kliknij prawym przyciskiem myszy folder subapp w Menedżerze usług IIS, a następnie wybierz polecenie **Konwertuj na aplikację**.

1. W oknie dialogowym **Dodawanie aplikacji** Użyj przycisku **Wybierz** dla **puli aplikacji** , aby przypisać pulę aplikacji utworzoną dla aplikacji podrzędnej. Kliknij przycisk **OK**.

Przypisanie oddzielnej puli aplikacji do aplikacji podrzędnej jest wymagane w przypadku korzystania z modelu hostingu w procesie.

Aby uzyskać więcej informacji na temat modelu hostingu w procesie i konfigurowania modułu ASP.NET Core, zobacz <xref:host-and-deploy/aspnet-core-module> .

## <a name="configuration-of-iis-with-webconfig"></a>Konfiguracja usług IIS z web.config

Na konfigurację usług IIS wpływa `<system.webServer>` sekcja *web.config* dla scenariuszy usług IIS, które są funkcjonalne dla ASP.NET Core aplikacji za pomocą modułu ASP.NET Core. Na przykład konfiguracja usług IIS jest funkcjonalna dla kompresji dynamicznej. Jeśli usługi IIS są skonfigurowane na poziomie serwera do korzystania z kompresji dynamicznej, `<urlCompression>` element w pliku *web.config* aplikacji może go wyłączyć dla aplikacji ASP.NET Core.

Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Dokumentacja konfiguracyjna programu \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Aby ustawić zmienne środowiskowe dla poszczególnych aplikacji działających w pulach izolowanych aplikacji (obsługiwanych w przypadku usług IIS 10,0 lub nowszych), zobacz sekcję *polecenieAppCmd.exe* w temacie [ \<environmentVariables> zmienne środowiskowe](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) w dokumentacji dotyczącej usług IIS.

## <a name="configuration-sections-of-webconfig"></a>Sekcje konfiguracji web.config

Sekcje konfiguracyjne aplikacji ASP.NET 4. x w *web.config* nie są używane przez aplikacje ASP.NET Core do konfiguracji:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Aplikacje ASP.NET Core są konfigurowane przy użyciu innych dostawców konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfiguracja](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Pule aplikacji

W przypadku hostowania wielu witryn sieci Web na serwerze zalecamy odizolowanie aplikacji od siebie, uruchamiając każdą aplikację w jej własnej puli aplikacji. W oknie dialogowym **Dodaj witrynę sieci Web** programu IIS domyślnie zostanie wydana konfiguracja. Po podaniu **nazwy witryny** tekst zostanie automatycznie przeniesiony do pola tekstowego **Pula aplikacji** . Nowa pula aplikacji jest tworzona przy użyciu nazwy lokacji, gdy zostanie dodana lokacja.

## <a name="application-pool-no-locidentity"></a>Pula aplikacji Identity

Konto tożsamości puli aplikacji umożliwia uruchamianie aplikacji na unikatowym koncie bez konieczności tworzenia domen ani kont lokalnych oraz zarządzania nimi. W przypadku usług IIS 8,0 lub nowszych proces roboczy administratora usług IIS tworzy konto wirtualne o nazwie nowej puli aplikacji i domyślnie uruchamia procesy robocze puli aplikacji w ramach tego konta. W konsoli zarządzania usługami IIS w obszarze **Ustawienia zaawansowane** dla puli aplikacji upewnij się, że **Identity** ustawiono opcję Użyj **ApplicationPool Identity **:

![Okno dialogowe Zaawansowane ustawienia puli aplikacji](index/_static/apppool-identity.png)

Proces zarządzania usługami IIS tworzy Bezpieczny identyfikator z nazwą puli aplikacji w systemie zabezpieczeń systemu Windows. Zasoby mogą być zabezpieczone przy użyciu tej tożsamości. Jednak ta tożsamość nie jest kontem użytkownika rzeczywistego i nie jest wyświetlana w konsoli zarządzania użytkownikami systemu Windows.

Jeśli proces roboczy usług IIS wymaga podwyższonego poziomu dostępu do aplikacji, Zmodyfikuj listę Access Control (ACL) dla katalogu zawierającego aplikację:

1. Otwórz Eksploratora Windows i przejdź do katalogu.

1. Kliknij prawym przyciskiem myszy katalog i wybierz polecenie **Właściwości**.

1. Na karcie **zabezpieczenia** wybierz przycisk **Edytuj** , a następnie przycisk **Dodaj** .

1. Wybierz przycisk **lokalizacje** i upewnij się, że wybrano system.

1. Wprowadź **<usług IIS puli aplikacji \\ app_pool_name>** w polu **Wprowadź nazwy obiektów do wybrania** . Wybierz przycisk **Sprawdź nazwy** . W obszarze *Domyślna pula aplikacji* Sprawdź nazwy za pomocą **usług IIS AppPool\DefaultAppPool**. Po wybraniu przycisku **Sprawdź nazwy** w obszarze nazwy obiektów zostanie wykazana wartość **Domyślna pula aplikacji** . Nie można wprowadzić nazwy puli aplikacji bezpośrednio w obszarze nazw obiektów. Podczas sprawdzania nazwy obiektu Użyj **<IIS puli aplikacji \\ app_pool_name>** .

   ![Okno dialogowe Wybieranie użytkowników lub grup dla folderu aplikacji: Nazwa puli aplikacji "domyślna pula" jest dołączana do "puli aplikacji IIS \" w obszarze nazw obiektów przed wybraniem pozycji" Sprawdź nazwy ".](index/_static/select-users-or-groups-1.png)

1. Kliknij przycisk **OK**.

   ![Okno dialogowe Wybieranie użytkowników lub grup dla folderu aplikacji: po wybraniu pozycji "Sprawdź nazwy" w obszarze nazwy obiektów zostanie wyświetlona nazwa obiektu "domyślna pula aplikacji".](index/_static/select-users-or-groups-2.png)

1. &amp;Uprawnienia do wykonywania odczytu powinny być przyznawane domyślnie. Podaj dodatkowe uprawnienia zgodnie z wymaganiami.

Dostęp można także udzielić w wierszu polecenia przy użyciu narzędzia **icacls** . Użycie funkcji *Domyślna pula aplikacji* jako przykładu powoduje użycie następującego polecenia:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Aby uzyskać więcej informacji, zobacz temat [icacls](/windows-server/administration/windows-commands/icacls) .

## <a name="http2-support"></a>Obsługa protokołu HTTP/2

Obsługa [protokołu HTTP/2](https://httpwg.org/specs/rfc7540.html) jest obsługiwana w przypadku wdrożeń pozaprocesowych, które spełniają następujące wymagania podstawowe:

* Windows Server 2016/Windows 10 lub nowszy; Program IIS 10 lub nowszy
* Połączenia z serwerem granicznym dostępnym publicznie korzystają z protokołu HTTP/2, ale połączenie zwrotnego serwera proxy z [serwerem Kestrel](xref:fundamentals/servers/kestrel) korzysta z protokołu HTTP/1.1.
* Struktura docelowa: nie dotyczy wdrożeń poza procesem, ponieważ połączenie HTTP/2 jest obsługiwane całkowicie przez usługi IIS.
* Połączenie TLS 1,2 lub nowsze

W przypadku nawiązania połączenia HTTP/2 raporty [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/1.1` .

Protokół HTTP/2 jest domyślnie włączony. Połączenia powracają do protokołu HTTP/1.1, jeśli połączenie HTTP/2 nie zostało ustanowione. Aby uzyskać więcej informacji na temat konfiguracji protokołu HTTP/2 z wdrożeniami usług IIS, zobacz [http/2 w usługach IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Żądania inspekcji wstępnej CORS

*Ta sekcja dotyczy tylko ASP.NET Core aplikacji przeznaczonych dla .NET Framework.*

W przypadku aplikacji ASP.NET Core, która jest przeznaczona dla .NET Framework, żądania opcji nie są domyślnie przesyłane do aplikacji w usługach IIS. Aby dowiedzieć się, jak skonfigurować obsługę usług IIS w *web.config* , aby przekazywać żądania dotyczące opcji, zobacz [Włączanie żądań między źródłami w ASP.NET Web API 2: jak działa mechanizm CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="deployment-resources-for-iis-administrators"></a>Zasoby wdrażania dla administratorów usług IIS

* [Dokumentacja usług IIS](/iis)
* [Wprowadzenie z menedżerem usług IIS w usługach IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Wdrażanie aplikacji .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:test/troubleshoot>
* <xref:index>
* [Oficjalna witryna programu Microsoft IIS](https://www.iis.net/)
* [Biblioteka zawartości technicznej systemu Windows Server](/windows-server/windows-server)
* [Protokół HTTP/2 w usługach IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
