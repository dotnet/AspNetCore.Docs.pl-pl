---
title: Host ogólny platformy .NET
author: rick-anderson
description: Dowiedz się więcej o hoście ogólnym .NET Core, który jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 46a56c278e889778e58a1fbb41ec217aaf023b13
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488777"
---
# <a name="net-generic-host"></a>Host ogólny platformy .NET

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

Szablony ASP.NET Core tworzą hosta ogólnego .NET Core, <xref:Microsoft.Extensions.Hosting.HostBuilder>.

## <a name="host-definition"></a>Definicja hosta

*Host* jest obiektem, który hermetyzuje zasoby aplikacji, takie jak:

* Wstrzyknięcie zależności (DI)
* Rejestrowanie
* Konfigurowanie
* `IHostedService`Implementacje

Po uruchomieniu hosta wywołuje `IHostedService.StartAsync` każdą <xref:Microsoft.Extensions.Hosting.IHostedService> implementację, która znajduje się w kontenerze DI. W aplikacji sieci web `IHostedService` jedną z implementacji jest usługa sieci web, która uruchamia [implementację serwera HTTP](xref:fundamentals/index#servers).

Głównym powodem włączenia wszystkich współzależnych zasobów aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: kontrola nad uruchamianiem aplikacji i wdzięczne zamykanie.

## <a name="set-up-a-host"></a>Konfigurowanie hosta

Host jest zazwyczaj skonfigurowany, zbudowany i uruchamiany `Program` przez kod w klasie. Metoda: `Main`

* Wywołuje `CreateHostBuilder` metodę tworzenia i konfigurowania obiektu konstruktora.
* `Build` Wywołania `Run` i metody na obiekcie konstruktora.

Szablony sieci Web ASP.NET Core generują następujący kod w celu utworzenia hosta ogólnego:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

Poniższy kod tworzy hosta ogólnego przy użyciu obciążenia innego niż HTTP. Implementacja `IHostedService` jest dodawana do kontenera DI:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

W przypadku obciążenia `Main` HTTP metoda jest `CreateHostBuilder` `ConfigureWebHostDefaults`taka sama, ale wywołuje:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Poprzedni kod jest generowany przez szablony ASP.NET Core.

Jeśli aplikacja używa entity framework core, nie należy zmieniać `CreateHostBuilder` nazwę lub podpis metody. [Narzędzia Entity Framework Core](/ef/core/miscellaneous/cli/) oczekują, aby znaleźć `CreateHostBuilder` metodę, która konfiguruje hosta bez uruchamiania aplikacji. Aby uzyskać więcej informacji, zobacz [Tworzenie dbcontext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).

## <a name="default-builder-settings"></a>Domyślne ustawienia konstruktora

Metoda: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>

* Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na <xref:System.IO.Directory.GetCurrentDirectory*>ścieżkę zwróconą przez program .
* Ładuje konfigurację hosta z:
  * Zmienne środowiskowe poprzedzone . `DOTNET_`
  * Argumenty wiersza polecenia.
* Ładuje konfigurację aplikacji z:
  * *appsettings.json*.
  * *appsettings. {Środowisko}.json*.
  * [Tajny menedżer,](xref:security/app-secrets) gdy aplikacja `Development` działa w środowisku.
  * Zmienne środowiskowe.
  * Argumenty wiersza polecenia.
* Dodaje następujących dostawców [rejestrowania:](xref:fundamentals/logging/index)
  * Konsola
  * Debugowanie
  * EventSource
  * EventLog (tylko w przypadku pracy w systemie Windows)
* Włącza [sprawdzanie poprawności zakresu](xref:fundamentals/dependency-injection#scope-validation) i sprawdzania poprawności [zależności,](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) gdy środowisko jest rozwój.

Metoda: `ConfigureWebHostDefaults`

* Ładuje konfigurację hosta ze zmiennych środowiskowych poprzedzonych programem `ASPNETCORE_`.
* Ustawia serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci web i konfiguruje go przy użyciu dostawców konfiguracji hostingu aplikacji. Aby zapoznać się z domyślnymi <xref:fundamentals/servers/kestrel#kestrel-options>opcjami serwera Kestrel, zobacz .
* Dodaje [oprogramowanie pośredniczące filtrowania hosta](xref:fundamentals/servers/kestrel#host-filtering).
* Dodaje [oprogramowanie pośredniczące nagłówków przesyłanych dalej,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest `true`równe .
* Umożliwia integrację z usługiami IIS. Aby zapoznać się z <xref:host-and-deploy/iis/index#iis-options>domyślnymi opcjami iis, zobacz .

W sekcji [Ustawienia dla wszystkich typów aplikacji](#settings-for-all-app-types) i Ustawienia aplikacji sieci [Web](#settings-for-web-apps) w dalszej części tego artykułu pokazano, jak zastąpić domyślne ustawienia konstruktora.

## <a name="framework-provided-services"></a>Usługi świadczone w ramach ram

Następujące usługi są rejestrowane automatycznie:

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime (czas pracy)](#ihostlifetime)
* [IHostEnvironment / IWebHostŚrodowicielenie](#ihostenvironment)

Aby uzyskać więcej informacji na <xref:fundamentals/dependency-injection#framework-provided-services>temat usług świadczonych w ramach ram, zobacz .

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Wstrzyknąć `IApplicationLifetime`(dawniej) usługi do dowolnej klasy do obsługi zadań po starcie i wdzięku zamknięcia. Trzy właściwości w interfejsie są tokeny anulowania używane do rejestrowania uruchamiania aplikacji i metody obsługi zdarzeń zatrzymania aplikacji. Interfejs zawiera również `StopApplication` metodę.

Poniższy przykład `IHostedService` jest implementacją, która rejestruje `IHostApplicationLifetime` zdarzenia:

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime (czas pracy)

Implementacja <xref:Microsoft.Extensions.Hosting.IHostLifetime> kontroluje, kiedy host uruchamia się i kiedy zatrzymuje. Używana jest ostatnia zarejestrowana implementacja.

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`jest domyślną `IHostLifetime` implementacją. `ConsoleLifetime`:

* Nasłuchuje <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM i wywołuje, <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> aby rozpocząć proces zamykania.
* Odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).

## <a name="ihostenvironment"></a>IHostŚrodowienie

Wstrzyknąć <xref:Microsoft.Extensions.Hosting.IHostEnvironment> usługę do klasy, aby uzyskać informacje o następujących ustawieniach:

* [Applicationname](#applicationname)
* [Nazwa środowiska](#environmentname)
* [Ścieżka contentrootpath](#contentroot)

Aplikacje sieci `IWebHostEnvironment` Web implementują `IHostEnvironment` interfejs, który dziedziczy i dodaje [WebRootPath](#webroot).

## <a name="host-configuration"></a>Konfiguracja hosta

Konfiguracja hosta jest używana <xref:Microsoft.Extensions.Hosting.IHostEnvironment> dla właściwości implementacji.

Konfiguracja hosta jest dostępna w <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>pliku [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) wewnątrz . Po `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , jest zastępowany config aplikacji.

Aby dodać konfigurację <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`hosta, zadzwoń . `ConfigureHostConfiguration`można nazwać wiele razy z addytywnymi wynikami. Host używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.

Dostawca zmiennych środowiskowych `DOTNET_` z prefiksem i `CreateDefaultBuilder`argumentami wiersza polecenia są uwzględniane przez program . W przypadku aplikacji sieci web dodawany jest dostawca zmiennych środowiskowych z prefiksem. `ASPNETCORE_` Prefiks jest usuwany podczas odczytu zmiennych środowiskowych. Na przykład wartość zmiennej `ASPNETCORE_ENVIRONMENT` środowiskowej dla staje `environment` się wartością konfiguracji hosta dla klucza.

W poniższym przykładzie tworzy konfigurację hosta:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>Konfiguracja aplikacji

Konfiguracja aplikacji jest <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder`tworzona przez wywołanie . `ConfigureAppConfiguration`można nazwać wiele razy z addytywnymi wynikami. Aplikacja używa dowolnej opcji ustawia wartość ostatnio na danym kluczu. 

Konfiguracja utworzona `ConfigureAppConfiguration` przez jest dostępna w [witrynie HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) dla kolejnych operacji i jako usługa z DI. Konfiguracja hosta jest również dodawana do konfiguracji aplikacji.

Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).

## <a name="settings-for-all-app-types"></a>Ustawienia dla wszystkich typów aplikacji

W tej sekcji wymieniono ustawienia hosta, które dotyczą zarówno obciążeń HTTP, jak i innych niż HTTP. Domyślnie zmienne środowiskowe używane do konfigurowania `DOTNET_` `ASPNETCORE_` tych ustawień mogą mieć lub prefiks.

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

[Właściwość IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) jest ustawiana z konfiguracji hosta podczas konstruowaniu hosta.

**Klucz:**`applicationName`  
**Typ**:`string`  
**Domyślnie**: Nazwa zestawu zawierającego punkt wejścia aplikacji.  
**Zmienna środowiskowa:**`<PREFIX_>APPLICATIONNAME`

Aby ustawić tę wartość, należy użyć zmiennej środowiskowej. 

### <a name="contentroot"></a>ContentRoot (Typ treści)

[Właściwość IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) określa, gdzie host rozpoczyna wyszukiwanie plików zawartości. Jeśli ścieżka nie istnieje, host nie uruchamia się.

**Klucz:**`contentRoot`  
**Typ**:`string`  
**Domyślnie**: Folder, w którym znajduje się zestaw aplikacji.  
**Zmienna środowiskowa:**`<PREFIX_>CONTENTROOT`

Aby ustawić tę wartość, należy `UseContentRoot` użyć `IHostBuilder`zmiennej środowiskowej lub wywołać na:

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

Aby uzyskać więcej informacji, zobacz:

* [Podstawy: Katalog główny treści](xref:fundamentals/index#content-root)
* [Webroot](#webroot)

### <a name="environmentname"></a>Nazwa środowiska

[Właściwość IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) można ustawić na dowolną wartość. Wartości zdefiniowane `Development`przez `Staging`ramy `Production`obejmują , i . W wartościach nie rozróżnia się wielkość liter.

**Klucz:**`environment`  
**Typ**:`string`  
**Domyślnie**:`Production`  
**Zmienna środowiskowa:**`<PREFIX_>ENVIRONMENT`

Aby ustawić tę wartość, należy `UseEnvironment` użyć `IHostBuilder`zmiennej środowiskowej lub wywołać na:

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>Shutdowntimeout

[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) ustawia limit <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>czasu dla . Wartość domyślna to pięć sekund.  W okresie limitu czasu host:

* Wyzwala [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).
* Próbuje zatrzymać hostowane usługi, rejestrowanie błędów dla usług, które nie mogą się zatrzymać.

Jeśli limit czasu wygaśnie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji. Usługi zatrzymują się nawet wtedy, gdy nie zakończyły przetwarzania. Jeśli usługi wymagają dodatkowego czasu, aby zatrzymać, zwiększyć limit czasu.

**Klucz:**`shutdownTimeoutSeconds`  
**Typ**:`int`  
**Domyślnie**: 5 sekund  
**Zmienna środowiskowa:**`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

Aby ustawić tę wartość, użyj zmiennej środowiskowej lub skonfiguruj `HostOptions`. W poniższym przykładzie ustawia limit czasu na 20 sekund:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a>Ustawienia aplikacji internetowych

Niektóre ustawienia hosta dotyczą tylko obciążeń HTTP. Domyślnie zmienne środowiskowe używane do konfigurowania `DOTNET_` `ASPNETCORE_` tych ustawień mogą mieć lub prefiks.

Dostępne `IWebHostBuilder` są metody rozszerzenia dla tych ustawień. Przykłady kodu, które pokazują, jak `webBuilder` wywołać `IWebHostBuilder`metody rozszerzenia zakłada, że jest wystąpieniem , jak w poniższym przykładzie:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>CaptureStartupErrors

Kiedy `false`, błędy podczas uruchamiania spowodować wyjście hosta. Gdy `true`host przechwytuje wyjątki podczas uruchamiania i próbuje uruchomić serwer.

**Klucz:**`captureStartupErrors`  
**Typ** `bool` :`true` `1`( lub )  
**Domyślnie:** `false` Domyślnie, chyba że aplikacja działa z Kestrel `true`za IIS, gdzie domyślnie jest .  
**Zmienna środowiskowa:**`<PREFIX_>CAPTURESTARTUPERRORS`

Aby ustawić tę wartość, `CaptureStartupErrors`użyj konfiguracji lub wywołania:

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>Szczegółoweerrory

Po włączeniu lub gdy `Development`środowisko jest , aplikacja przechwytuje szczegółowe błędy.

**Klucz:**`detailedErrors`  
**Typ** `bool` :`true` `1`( lub )  
**Domyślnie**:`false`  
**Zmienna środowiskowa:**`<PREFIX_>_DETAILEDERRORS`

Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do załadowania podczas uruchamiania. Mimo że wartość konfiguracji domyślnie pusty ciąg, zestawy uruchamiania hostingu zawsze zawierają zestaw aplikacji. Po udostępnieniu zestawów uruchamiania hostingu są one dodawane do zestawu aplikacji do ładowania, gdy aplikacja tworzy swoje typowe usługi podczas uruchamiania.

**Klucz:**`hostingStartupAssemblies`  
**Typ**:`string`  
**Domyślnie**: Pusty ciąg  
**Zmienna środowiskowa:**`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do wykluczenia podczas uruchamiania.

**Klucz:**`hostingStartupExcludeAssemblies`  
**Typ**:`string`  
**Domyślnie**: Pusty ciąg  
**Zmienna środowiskowa:**`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

Port przekierowania HTTPS. Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).

**Klucz:**`https_port`  
**Typ**:`string`  
**Domyślnie:** Wartość domyślna nie jest ustawiona.  
**Zmienna środowiskowa:**`<PREFIX_>HTTPS_PORT`

Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferHostingUrls (PreferHostingUrls)

Wskazuje, czy host powinien nasłuchiwania adresów URL skonfigurowanych przy 200 000 `IWebHostBuilder` adresów URL skonfigurowanych z `IServer` implementacją.

**Klucz:**`preferHostingUrls`  
**Typ** `bool` :`true` `1`( lub )  
**Domyślnie**:`true`  
**Zmienna środowiskowa:**`<PREFIX_>_PREFERHOSTINGURLS`

Aby ustawić tę wartość, użyj `PreferHostingUrls`zmiennej środowiskowej lub wywołania:

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

Zapobiega automatycznemu obciążaniu zestawów uruchamiania hostingu, w tym zestawów uruchamiania hostingu skonfigurowanych przez zestaw aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.

**Klucz:**`preventHostingStartup`  
**Typ** `bool` :`true` `1`( lub )  
**Domyślnie**:`false`  
**Zmienna środowiskowa:**`<PREFIX_>_PREVENTHOSTINGSTARTUP`

Aby ustawić tę wartość, użyj `UseSetting` zmiennej środowiskowej lub wywołania:

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

Zestaw do wyszukiwania `Startup` klasy.

**Klucz:**`startupAssembly`  
**Typ**:`string`  
**Domyślnie**: Zespół aplikacji  
**Zmienna środowiskowa:**`<PREFIX_>STARTUPASSEMBLY`

Aby ustawić tę wartość, należy `UseStartup`użyć zmiennej środowiskowej lub wywołania . `UseStartup`może przyjąć nazwę`string`zestawu ( )`TStartup`lub typ ( ). Jeśli `UseStartup` wywoływanych jest wiele metod, pierwszeństwo ma ostatnia.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>Adresy URL

Lista adresów IP lub adresów hostów rozdzielanych średnikami z portami i protokołami, na których serwer powinien nasłuchiwał żądań. Na przykład `http://localhost:123`. Użyj\*" ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwach hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000`). Protokół (`http://` `https://`lub ) musi być dołączony do każdego adresu URL. Obsługiwane formaty różnią się w zależności od serwera.

**Klucz:**`urls`  
**Typ**:`string`  
**Domyślnie**: `http://localhost:5000` i`https://localhost:5001`  
**Zmienna środowiskowa:**`<PREFIX_>URLS`

Aby ustawić tę wartość, użyj `UseUrls`zmiennej środowiskowej lub wywołania:

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Pustułka ma własny interfejs API konfiguracji punktu końcowego. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="webroot"></a>Webroot

[Właściwość IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) określa względną ścieżkę do statycznych zasobów aplikacji. Jeśli ścieżka nie istnieje, używany jest dostawca plików bez operacji.  

**Klucz:**`webroot`  
**Typ**:`string`  
**Domyślnie**: `wwwroot`Wartość domyślna to . Musi istnieć ścieżka do *{content root}/wwwroot.*  
**Zmienna środowiskowa:**`<PREFIX_>WEBROOT`

Aby ustawić tę wartość, należy `UseWebRoot` użyć `IWebHostBuilder`zmiennej środowiskowej lub wywołać na:

```csharp
webBuilder.UseWebRoot("public");
```

Aby uzyskać więcej informacji, zobacz:

* [Podstawy: Korzeń sieci Web](xref:fundamentals/index#web-root)
* [ContentRoot (Typ treści)](#contentroot)

## <a name="manage-the-host-lifetime"></a>Zarządzanie okresem istnienia hosta

Wywołaj metody <xref:Microsoft.Extensions.Hosting.IHost> na wbudowanej implementacji, aby uruchomić i zatrzymać aplikację. Te metody <xref:Microsoft.Extensions.Hosting.IHostedService> mają wpływ na wszystkie implementacje, które są zarejestrowane w kontenerze usługi.

### <a name="run"></a>Run

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący, dopóki host nie zostanie zamknięty.

### <a name="runasync"></a>RunAsync ( RunAsync )

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia systemu.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>umożliwia obsługę konsoli, buduje i uruchamia hosta i czeka na <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM, aby zamknąć.

### <a name="start"></a>Uruchamianie

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.

### <a name="startasync"></a>StartAsync (StartAsync)

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia hosta i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na `StartAsync`początku , który czeka, aż zakończy się przed kontynuowaniem. Może to służyć do opóźnienia uruchamiania, dopóki nie zostanie zasygnaligozowana przez zdarzenie zewnętrzne.

### <a name="stopasync"></a>StopAsync (StopAsync)

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w podanym limit czasu.

### <a name="waitforshutdown"></a>WaitForShutdown (WaitForShutdown)

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje wątek wywołujący, dopóki zamknięcie zostanie wywołane przez IHostLifetime, na przykład przez <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task> zwraca, który kończy się po zamknięciu jest <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>wyzwalany za pośrednictwem danego tokenu i wywołania .

### <a name="external-control"></a>Sterowanie zewnętrzne

Bezpośrednią kontrolę okresu istnienia hosta można osiągnąć za pomocą metod, które można wywołać zewnętrznie:

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET aplikacje Core konfigurują i uruchamiają hosta. Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.

W tym artykule omówiono ASP.NET<xref:Microsoft.Extensions.Hosting.HostBuilder>Core Generic Host ( ), który jest używany dla aplikacji, które nie przetwarzają żądań HTTP.

Celem hosta ogólnego jest oddzielenie potoku HTTP od interfejsu API hosta sieci Web, aby włączyć szerszą gamę scenariuszy hosta. Wiadomości, zadania w tle i inne obciążenia inne niż HTTP oparte na generic hosta korzystają z możliwości przekrojowych, takich jak konfiguracja, iniekcja zależności (DI) i rejestrowanie.

Generic Host jest nowy w ASP.NET Core 2.1 i nie nadaje się do scenariuszy hostingu. W przypadku scenariuszy hostingu sieci Web należy użyć [hosta sieci Web](xref:fundamentals/host/web-host). Generic Host zastąpi hosta sieci Web w przyszłej wersji i będzie działać jako podstawowy interfejs API hosta zarówno w scenariuszach HTTP, jak i innych niż HTTP.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Podczas uruchamiania przykładowej aplikacji w [programie Visual Studio Code](https://code.visualstudio.com/)należy użyć zewnętrznego lub *zintegrowanego terminala*. Nie uruchamiaj próbki w `internalConsole`pliku .

Aby ustawić konsolę w programie Visual Studio Code:

1. Otwórz plik *vscode/launch.json.*
1. W konfiguracji **.NET Core Launch (konsola)** znajdź wpis **konsoli.** Ustaw wartość na `externalTerminal` jedną `integratedTerminal`lub .

## <a name="introduction"></a>Wprowadzenie

Biblioteka hosta ogólnego <xref:Microsoft.Extensions.Hosting> jest dostępna w obszarze nazw i jest dostarczana przez pakiet [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) Pakiet `Microsoft.Extensions.Hosting` jest zawarty w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 lub nowszym).

<xref:Microsoft.Extensions.Hosting.IHostedService>jest punktem wejścia do wykonania kodu. Każda <xref:Microsoft.Extensions.Hosting.IHostedService> implementacja jest wykonywana w kolejności [rejestracji usługi w ConfigureServices](#configureservices). <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>jest wywoływana <xref:Microsoft.Extensions.Hosting.IHostedService> na każdym po <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> uruchomieniu hosta i jest wywoływana w odwrotnej kolejności rejestracji, gdy host zamyka się bezpiecznie.

## <a name="set-up-a-host"></a>Konfigurowanie hosta

<xref:Microsoft.Extensions.Hosting.IHostBuilder>jest głównym składnikiem używanym przez biblioteki i aplikacje do inicjowania, tworzenia i uruchamiania hosta:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a>Opcje

<xref:Microsoft.Extensions.Hosting.HostOptions>skonfigurować opcje <xref:Microsoft.Extensions.Hosting.IHost>dla pliku .

### <a name="shutdown-timeout"></a>Limit czasu zamknięcia

<xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>ustawia limit czasu <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>dla . Wartość domyślna to pięć sekund.

Następująca konfiguracja `Program.Main` opcji zwiększa domyślny pięciosekundowy limit czasu zamknięcia do 20 sekund:

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a>Usługi domyślne

Podczas inicjowania hosta rejestrowane są następujące usługi:

* [Środowisko](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>( )
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* [Konfiguracja](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)
* <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHost>
* [Opcje](xref:fundamentals/configuration/options) <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>( )
* [Rejestrowanie](xref:fundamentals/logging/index) <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>( )

## <a name="host-configuration"></a>Konfiguracja hosta

Konfiguracja hosta jest tworzona przez:

* Wywoływanie metod <xref:Microsoft.Extensions.Hosting.IHostBuilder> rozszerzenia w celu ustawienia [katalogu głównego](#content-root) zawartości i [środowiska](#environment).
* Odczytywanie konfiguracji <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>od dostawców konfiguracji w programie .

### <a name="extension-methods"></a>Metody rozszerzeń

### <a name="application-key-name"></a>Klucz aplikacji (nazwa)

[Właściwość IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) jest ustawiona z konfiguracji hosta podczas konstruowania hosta. Aby jawnie ustawić wartość, użyj [hostDefaults.ApplicationKey:](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)

**Klucz:**`applicationName`  
**Typ**:`string`  
**Domyślnie**: Nazwa zestawu zawierającego punkt wejścia aplikacji.  
**Zestaw za pomocą**:`HostBuilderContext.HostingEnvironment.ApplicationName`  
**Zmienna środowiskowa**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` jest [opcjonalna i zdefiniowana przez użytkownika)](#configurehostconfiguration)

### <a name="content-root"></a>Katalog główny zawartości

To ustawienie określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.

**Klucz:**`contentRoot`  
**Typ**:`string`  
**Domyślnie**: Domyślnie folder, w którym znajduje się zestaw aplikacji.  
**Zestaw za pomocą**:`UseContentRoot`  
**Zmienna środowiskowa**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` jest [opcjonalna i zdefiniowana przez użytkownika)](#configurehostconfiguration)

Jeśli ścieżka nie istnieje, host nie uruchamia się.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

Aby uzyskać więcej informacji, zobacz [Podstawy: Katalog główny zawartości](xref:fundamentals/index#content-root).

### <a name="environment"></a>Środowisko

Ustawia [środowisko](xref:fundamentals/environments)aplikacji .

**Klucz:**`environment`  
**Typ**:`string`  
**Domyślnie**:`Production`  
**Zestaw za pomocą**:`UseEnvironment`  
**Zmienna środowiskowa**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` jest [opcjonalna i zdefiniowana przez użytkownika)](#configurehostconfiguration)

Środowisko można ustawić na dowolną wartość. Wartości zdefiniowane `Development`przez `Staging`ramy `Production`obejmują , i . W wartościach nie rozróżnia się wielkość liter.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a>Konfigurowanie konfiguracji hostowania

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>używa do <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> utworzenia <xref:Microsoft.Extensions.Configuration.IConfiguration> dla hosta. Konfiguracja hosta służy do <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> inicjowania do użycia w procesie kompilacji aplikacji.

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>można nazwać wiele razy z addytywnymi wynikami. Host używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.

Domyślnie nie są uwzględniane żadne dostawcy. Należy jawnie określić dostawców konfiguracji, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>których aplikacja wymaga w , w tym:

* Konfiguracja pliku (na przykład z pliku *hostsettings.json).*
* Konfiguracja zmiennych środowiskowych.
* Konfiguracja argumentu wiersza polecenia.
* Wszelkie inne wymagane dostawców konfiguracji.

Konfiguracja pliku hosta jest włączona, określając ścieżkę `SetBasePath` podstawową aplikacji, po której następuje wywołanie jednego z [dostawców konfiguracji plików.](xref:fundamentals/configuration/index#file-configuration-provider) Przykładowa aplikacja używa pliku JSON, *hostsettings.json*i wywołania, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> aby korzystać z ustawień konfiguracji hosta pliku.

Aby dodać [konfigurację zmiennej środowiskowej](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hosta, należy wywołać <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> konstruktora hosta. `AddEnvironmentVariables`akceptuje opcjonalny prefiks zdefiniowany przez użytkownika. Przykładowa aplikacja używa prefiksu `PREFIX_`programu . Prefiks jest usuwany podczas odczytu zmiennych środowiskowych. Po skonfigurowaniu hosta przykładowej aplikacji wartość zmiennej środowiskowej `PREFIX_ENVIRONMENT` staje `environment` się wartością konfiguracji hosta dla klucza.

Podczas tworzenia podczas korzystania z programu `dotnet run`Visual [Studio](https://visualstudio.microsoft.com) lub uruchamiania aplikacji z , zmienne środowiskowe mogą być ustawione w pliku *Properties/launchSettings.json.* W [programie Visual Studio Code](https://code.visualstudio.com/)zmienne środowiskowe mogą być ustawiane w pliku *vscode/launch.json* podczas tworzenia. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

[Konfiguracja wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>jest dodawana przez wywołanie . Konfiguracja wiersza polecenia jest dodawana jako ostatnia, aby umożliwić argumentom wiersza polecenia zastąpienie konfiguracji dostarczonej przez wcześniejszych dostawców konfiguracji.

*hostsettings.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

Dodatkowa konfiguracja może być dostarczona z [kluczami applicationName](#application-key-name) i [contentRoot.](#content-root)

Przykładowa `HostBuilder` <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>konfiguracja przy użyciu:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a>Konfigurowanie konfiguracjiappconfiguration

Konfiguracja aplikacji jest <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> tworzona przez wywołanie <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementacji. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>używa do <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> utworzenia <xref:Microsoft.Extensions.Configuration.IConfiguration> aplikacji. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>można nazwać wiele razy z addytywnymi wynikami. Aplikacja używa dowolnej opcji ustawia wartość ostatnio na danym kluczu. Konfiguracja utworzona <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> przez jest dostępna w [witrynie HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) dla kolejnych operacji i w <xref:Microsoft.Extensions.Hosting.IHost.Services*>programie .

Konfiguracja aplikacji automatycznie odbiera konfigurację hosta udostępnianą przez [ConfigureHostConfiguration](#configurehostconfiguration).

Przykładowa konfiguracja aplikacji przy użyciu: <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

*appsettings.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

*appsettings. Development.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

*appsettings. Produkcja.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

Aby przenieść pliki ustawień do katalogu wyjściowego, określ pliki ustawień jako [elementy projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) w pliku projektu. Przykładowa aplikacja przenosi pliki ustawień aplikacji JSON i *hostsettings.json* z następującym `<Content>` elementem:

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> Metody rozszerzenia konfiguracji, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> takie jak i wymagają dodatkowych pakietów NuGet, takich jak [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) i [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables). Chyba że aplikacja używa [microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), pakiety te muszą zostać dodane do projektu oprócz podstawowego pakietu [Microsoft.Extensions.Configuration.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.

## <a name="configureservices"></a>Konfigurowanie usług

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>dodaje usługi do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>można nazwać wiele razy z addytywnymi wynikami.

Usługa hostowana jest klasą z logiką <xref:Microsoft.Extensions.Hosting.IHostedService> zadań w tle, która implementuje interfejs. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/hosted-services>.

[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) używa `AddHostedService` metody rozszerzenia, aby dodać `LifetimeEventsHostedService`usługę dla zdarzeń okresu `TimedHostedService`istnienia i zadania w tle z odpowiednim czasem, do aplikacji:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a>Konfigurowanie rejestrowania

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>dodaje pełnomocnika do skonfigurowania dostarczonego <xref:Microsoft.Extensions.Logging.ILoggingBuilder>. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>można nazwać wiele razy z addytywnymi wynikami.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a>UseConsoleLifetime

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>nasłuchuje <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM i wywołuje, <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> aby rozpocząć proces zamykania. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync). `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`jest wstępnie zarejestrowany jako domyślna implementacja okresu istnienia. Używany jest ostatni zarejestrowany okres istnienia.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a>Konfiguracja kontenera

Aby obsługiwać podłączanie do innych kontenerów, host może zaakceptować plik <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>. Zapewnienie fabryki nie jest częścią rejestracji kontenera DI, ale zamiast tego jest nieodłącznym elementem hosta używanym do tworzenia betonowego kontenera DI. [UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder)&gt;](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) zastępuje domyślną fabrykę używaną do utworzenia dostawcy usług aplikacji.

Niestandardowa konfiguracja kontenera jest zarządzana <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> przez metodę. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>zapewnia silnie typizowane środowisko konfigurowania kontenera na podstawie podstawowego interfejsu API hosta. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>można nazwać wiele razy z addytywnymi wynikami.

Tworzenie kontenera usługi dla aplikacji:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

Zapewnienie fabryki kontenerów serwisowych:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

Użyj ustawień fabrycznych i skonfiguruj niestandardowy kontener usługi dla aplikacji:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a>Rozszerzalność

Rozszerzalność hosta jest wykonywana <xref:Microsoft.Extensions.Hosting.IHostBuilder>przy metodach rozszerzeń na . W poniższym przykładzie pokazano, <xref:Microsoft.Extensions.Hosting.IHostBuilder> jak metoda rozszerzenia rozszerza implementację za <xref:fundamentals/host/hosted-services>pomocą przykładu [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) przedstawionego w .

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

Aplikacja ustanawia metodę `UseHostedService` rozszerzenia, aby zarejestrować usługę `T`hosta przekazywana w:

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a>Zarządzanie hostem

Implementacja <xref:Microsoft.Extensions.Hosting.IHost> jest odpowiedzialny za <xref:Microsoft.Extensions.Hosting.IHostedService> uruchamianie i zatrzymywanie implementacji, które są zarejestrowane w kontenerze usługi.

### <a name="run"></a>Run

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący, dopóki host nie zostanie zamknięty:

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a>RunAsync ( RunAsync )

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia systemu:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>umożliwia obsługę konsoli, buduje i uruchamia hosta i czeka na <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM, aby zamknąć.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a>Start i StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w podanym limit czasu.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a>StartAsync i StopAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia aplikację.

<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zatrzymuje aplikację.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a>WaitForShutdown (WaitForShutdown)

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>jest wyzwalany <xref:Microsoft.Extensions.Hosting.IHostLifetime>przez , `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` takich jak (nasłuchuje <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM). <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>połączeń <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>telefonicznych .

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task> zwraca, który kończy się po zamknięciu jest <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>wyzwalany za pośrednictwem danego tokenu i wywołania .

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a>Sterowanie zewnętrzne

Zewnętrzna kontrola hosta można osiągnąć za pomocą metod, które można wywołać zewnętrznie:

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>początku , który czeka, aż zakończy się przed kontynuowaniem. Może to służyć do opóźnienia uruchamiania, dopóki nie zostanie zasygnaligozowana przez zdarzenie zewnętrzne.

## <a name="ihostingenvironment-interface"></a>Interfejs IHostingEnvironment

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>zawiera informacje o środowisku hostingowym aplikacji. Użyj [iniekcji konstruktora,](xref:fundamentals/dependency-injection) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> aby uzyskać w celu wykorzystania jego właściwości i metody rozszerzenia:

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

## <a name="iapplicationlifetime-interface"></a>Interfejs IApplicationLifetime

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime>umożliwia działania po starcie i zamknięciu, w tym wdzięczne żądania zamknięcia systemu. Trzy właściwości interfejsu są tokeny anulowania <xref:System.Action> używane do rejestrowania metod, które definiują zdarzenia uruchamiania i zamykania.

| Token anulowania | Wyzwalane podczas&#8230; |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | Gospodarz w pełni się rozpoczął. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | Host kończy wdzięczne zamknięcie. Wszystkie wnioski powinny być przetwarzane. Zamykanie bloków, dopóki to zdarzenie nie zostanie zakończone. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | Host wykonuje wdzięku zamknięcia. Żądania mogą nadal być przetwarzane. Zamykanie bloków, dopóki to zdarzenie nie zostanie zakończone. |

Konstruktor-wstrzyknąć <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> usługę do dowolnej klasy. [Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) używa iniekcji konstruktora `LifetimeEventsHostedService` do klasy <xref:Microsoft.Extensions.Hosting.IHostedService> (implementacji), aby zarejestrować zdarzenia.

*LifetimeEventsHostedService.cs:*

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>żąda rozwiązania aplikacji. Następująca klasa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> używa do bezpiecznie zamknąć aplikację, `Shutdown` gdy wywoływana jest metoda klasy:

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

Szablony ASP.NET Core tworzą hosta ogólnego .NET<xref:Microsoft.Extensions.Hosting.HostBuilder>Core ( ).

## <a name="host-definition"></a>Definicja hosta

*Host* jest obiektem, który hermetyzuje zasoby aplikacji, takie jak:

* Wstrzyknięcie zależności (DI)
* Rejestrowanie
* Konfigurowanie
* `IHostedService`Implementacje

Po uruchomieniu hosta wywołuje `IHostedService.StartAsync` każdą <xref:Microsoft.Extensions.Hosting.IHostedService> implementację, która znajduje się w kontenerze DI. W aplikacji sieci web `IHostedService` jedną z implementacji jest usługa sieci web, która uruchamia [implementację serwera HTTP](xref:fundamentals/index#servers).

Głównym powodem włączenia wszystkich współzależnych zasobów aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: kontrola nad uruchamianiem aplikacji i wdzięczne zamykanie.

## <a name="set-up-a-host"></a>Konfigurowanie hosta

Host jest zazwyczaj skonfigurowany, zbudowany i uruchamiany `Program` przez kod w klasie. Metoda: `Main`

* Wywołuje `CreateHostBuilder` metodę tworzenia i konfigurowania obiektu konstruktora.
* `Build` Wywołania `Run` i metody na obiekcie konstruktora.

Szablony sieci Web ASP.NET Core generują następujący kod w celu utworzenia hosta:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

Poniższy kod tworzy obciążenie inne `IHostedService` niż HTTP z implementacją dodaną do kontenera DI.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

W przypadku obciążenia `Main` HTTP metoda jest `CreateHostBuilder` `ConfigureWebHostDefaults`taka sama, ale wywołuje:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Jeśli aplikacja używa entity framework core, nie należy zmieniać `CreateHostBuilder` nazwę lub podpis metody. [Narzędzia Entity Framework Core](/ef/core/miscellaneous/cli/) oczekują, aby znaleźć `CreateHostBuilder` metodę, która konfiguruje hosta bez uruchamiania aplikacji. Aby uzyskać więcej informacji, zobacz [Tworzenie dbcontext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).

## <a name="default-builder-settings"></a>Domyślne ustawienia konstruktora

Metoda: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>

* Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na <xref:System.IO.Directory.GetCurrentDirectory*>ścieżkę zwróconą przez program .
* Ładuje konfigurację hosta z:
  * Zmienne środowiskowe poprzedzone . `DOTNET_`
  * Argumenty wiersza polecenia.
* Ładuje konfigurację aplikacji z:
  * *appsettings.json*.
  * *appsettings. {Środowisko}.json*.
  * [Tajny menedżer,](xref:security/app-secrets) gdy aplikacja `Development` działa w środowisku.
  * Zmienne środowiskowe.
  * Argumenty wiersza polecenia.
* Dodaje następujących dostawców [rejestrowania:](xref:fundamentals/logging/index)
  * Konsola
  * Debugowanie
  * EventSource
  * EventLog (tylko w przypadku pracy w systemie Windows)
* Włącza [sprawdzanie poprawności zakresu](xref:fundamentals/dependency-injection#scope-validation) i sprawdzania poprawności [zależności,](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) gdy środowisko jest rozwój.

Metoda: `ConfigureWebHostDefaults`

* Ładuje konfigurację hosta ze zmiennych środowiskowych poprzedzonych programem `ASPNETCORE_`.
* Ustawia serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci web i konfiguruje go przy użyciu dostawców konfiguracji hostingu aplikacji. Aby zapoznać się z domyślnymi <xref:fundamentals/servers/kestrel#kestrel-options>opcjami serwera Kestrel, zobacz .
* Dodaje [oprogramowanie pośredniczące filtrowania hosta](xref:fundamentals/servers/kestrel#host-filtering).
* Dodaje [oprogramowanie pośredniczące nagłówków przesyłanych dalej,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest `true`równe .
* Umożliwia integrację z usługiami IIS. Aby zapoznać się z <xref:host-and-deploy/iis/index#iis-options>domyślnymi opcjami iis, zobacz .

W sekcji [Ustawienia dla wszystkich typów aplikacji](#settings-for-all-app-types) i Ustawienia aplikacji sieci [Web](#settings-for-web-apps) w dalszej części tego artykułu pokazano, jak zastąpić domyślne ustawienia konstruktora.

## <a name="framework-provided-services"></a>Usługi świadczone w ramach ram

Następujące usługi są rejestrowane automatycznie:

* [IHostApplicationLifetime](#ihostapplicationlifetime)
* [IHostLifetime (czas pracy)](#ihostlifetime)
* [IHostEnvironment / IWebHostŚrodowicielenie](#ihostenvironment)

Aby uzyskać więcej informacji na <xref:fundamentals/dependency-injection#framework-provided-services>temat usług świadczonych w ramach ram, zobacz .

## <a name="ihostapplicationlifetime"></a>IHostApplicationLifetime

<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Wstrzyknąć `IApplicationLifetime`(dawniej) usługi do dowolnej klasy do obsługi zadań po starcie i wdzięku zamknięcia. Trzy właściwości w interfejsie są tokeny anulowania używane do rejestrowania uruchamiania aplikacji i metody obsługi zdarzeń zatrzymania aplikacji. Interfejs zawiera również `StopApplication` metodę.

Poniższy przykład `IHostedService` jest implementacją, która rejestruje `IHostApplicationLifetime` zdarzenia:

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime (czas pracy)

Implementacja <xref:Microsoft.Extensions.Hosting.IHostLifetime> kontroluje, kiedy host uruchamia się i kiedy zatrzymuje. Używana jest ostatnia zarejestrowana implementacja.

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`jest domyślną `IHostLifetime` implementacją. `ConsoleLifetime`:

* Nasłuchuje <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM i wywołuje, <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> aby rozpocząć proces zamykania.
* Odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).

## <a name="ihostenvironment"></a>IHostŚrodowienie

Wstrzyknąć <xref:Microsoft.Extensions.Hosting.IHostEnvironment> usługę do klasy, aby uzyskać informacje o następujących ustawieniach:

* [Applicationname](#applicationname)
* [Nazwa środowiska](#environmentname)
* [Ścieżka contentrootpath](#contentroot)

Aplikacje sieci `IWebHostEnvironment` Web implementują `IHostEnvironment` interfejs, który dziedziczy i dodaje [WebRootPath](#webroot).

## <a name="host-configuration"></a>Konfiguracja hosta

Konfiguracja hosta jest używana <xref:Microsoft.Extensions.Hosting.IHostEnvironment> dla właściwości implementacji.

Konfiguracja hosta jest dostępna w <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>pliku [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) wewnątrz . Po `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , jest zastępowany config aplikacji.

Aby dodać konfigurację <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`hosta, zadzwoń . `ConfigureHostConfiguration`można nazwać wiele razy z addytywnymi wynikami. Host używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.

Dostawca zmiennych środowiskowych `DOTNET_` z prefiksem i `CreateDefaultBuilder`argumentami wiersza polecenia są uwzględniane przez program . W przypadku aplikacji sieci web dodawany jest dostawca zmiennych środowiskowych z prefiksem. `ASPNETCORE_` Prefiks jest usuwany podczas odczytu zmiennych środowiskowych. Na przykład wartość zmiennej `ASPNETCORE_ENVIRONMENT` środowiskowej dla staje `environment` się wartością konfiguracji hosta dla klucza.

W poniższym przykładzie tworzy konfigurację hosta:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>Konfiguracja aplikacji

Konfiguracja aplikacji jest <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder`tworzona przez wywołanie . `ConfigureAppConfiguration`można nazwać wiele razy z addytywnymi wynikami. Aplikacja używa dowolnej opcji ustawia wartość ostatnio na danym kluczu. 

Konfiguracja utworzona `ConfigureAppConfiguration` przez jest dostępna w [witrynie HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) dla kolejnych operacji i jako usługa z DI. Konfiguracja hosta jest również dodawana do konfiguracji aplikacji.

Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).

## <a name="settings-for-all-app-types"></a>Ustawienia dla wszystkich typów aplikacji

W tej sekcji wymieniono ustawienia hosta, które dotyczą zarówno obciążeń HTTP, jak i innych niż HTTP. Domyślnie zmienne środowiskowe używane do konfigurowania `DOTNET_` `ASPNETCORE_` tych ustawień mogą mieć lub prefiks.

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

[Właściwość IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) jest ustawiana z konfiguracji hosta podczas konstruowaniu hosta.

**Klucz:**`applicationName`  
**Typ**:`string`  
**Domyślnie**: Nazwa zestawu zawierającego punkt wejścia aplikacji.  
**Zmienna środowiskowa:**`<PREFIX_>APPLICATIONNAME`

Aby ustawić tę wartość, należy użyć zmiennej środowiskowej. 

### <a name="contentroot"></a>ContentRoot (Typ treści)

[Właściwość IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) określa, gdzie host rozpoczyna wyszukiwanie plików zawartości. Jeśli ścieżka nie istnieje, host nie uruchamia się.

**Klucz:**`contentRoot`  
**Typ**:`string`  
**Domyślnie**: Folder, w którym znajduje się zestaw aplikacji.  
**Zmienna środowiskowa:**`<PREFIX_>CONTENTROOT`

Aby ustawić tę wartość, należy `UseContentRoot` użyć `IHostBuilder`zmiennej środowiskowej lub wywołać na:

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

Aby uzyskać więcej informacji, zobacz:

* [Podstawy: Katalog główny treści](xref:fundamentals/index#content-root)
* [Webroot](#webroot)

### <a name="environmentname"></a>Nazwa środowiska

[Właściwość IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) można ustawić na dowolną wartość. Wartości zdefiniowane `Development`przez `Staging`ramy `Production`obejmują , i . W wartościach nie rozróżnia się wielkość liter.

**Klucz:**`environment`  
**Typ**:`string`  
**Domyślnie**:`Production`  
**Zmienna środowiskowa:**`<PREFIX_>ENVIRONMENT`

Aby ustawić tę wartość, należy `UseEnvironment` użyć `IHostBuilder`zmiennej środowiskowej lub wywołać na:

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>Shutdowntimeout

[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) ustawia limit <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>czasu dla . Wartość domyślna to pięć sekund.  W okresie limitu czasu host:

* Wyzwala [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).
* Próbuje zatrzymać hostowane usługi, rejestrowanie błędów dla usług, które nie mogą się zatrzymać.

Jeśli limit czasu wygaśnie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji. Usługi zatrzymują się nawet wtedy, gdy nie zakończyły przetwarzania. Jeśli usługi wymagają dodatkowego czasu, aby zatrzymać, zwiększyć limit czasu.

**Klucz:**`shutdownTimeoutSeconds`  
**Typ**:`int`  
**Domyślnie**: 5 sekund  
**Zmienna środowiskowa:**`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

Aby ustawić tę wartość, użyj zmiennej środowiskowej lub skonfiguruj `HostOptions`. W poniższym przykładzie ustawia limit czasu na 20 sekund:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a>Wyłącz ponowne ładowanie konfiguracji aplikacji przy zmianie

[Domyślnie](xref:fundamentals/configuration/index#default), *appsettings.json* i *appsettings.{ Środowisko}.json* są ponownie ładowane po zmianie pliku. Aby wyłączyć to zachowanie przeładowania w ASP.NET Core 5.0 Preview `hostBuilder:reloadConfigOnChange` 3 lub nowszym, ustaw klawisz na `false`.

**Klucz:**`hostBuilder:reloadConfigOnChange`  
**Typ** `bool` :`true` `1`( lub )  
**Domyślnie**:`true`  
**Argument wiersza polecenia:**`hostBuilder:reloadConfigOnChange`  
**Zmienna środowiskowa:**`<PREFIX_>hostBuilder:reloadConfigOnChange`

> [!WARNING]
> Separator`:`dwukropka ( ) nie działa z kluczami hierarchicznymi zmiennych środowiskowych na wszystkich platformach. Aby uzyskać więcej informacji, zobacz [Zmienne środowiskowe](xref:fundamentals/configuration/index#environment-variables).

## <a name="settings-for-web-apps"></a>Ustawienia aplikacji internetowych

Niektóre ustawienia hosta dotyczą tylko obciążeń HTTP. Domyślnie zmienne środowiskowe używane do konfigurowania `DOTNET_` `ASPNETCORE_` tych ustawień mogą mieć lub prefiks.

Dostępne `IWebHostBuilder` są metody rozszerzenia dla tych ustawień. Przykłady kodu, które pokazują, jak `webBuilder` wywołać `IWebHostBuilder`metody rozszerzenia zakłada, że jest wystąpieniem , jak w poniższym przykładzie:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>CaptureStartupErrors

Kiedy `false`, błędy podczas uruchamiania spowodować wyjście hosta. Gdy `true`host przechwytuje wyjątki podczas uruchamiania i próbuje uruchomić serwer.

**Klucz:**`captureStartupErrors`  
**Typ** `bool` :`true` `1`( lub )  
**Domyślnie:** `false` Domyślnie, chyba że aplikacja działa z Kestrel `true`za IIS, gdzie domyślnie jest .  
**Zmienna środowiskowa:**`<PREFIX_>CAPTURESTARTUPERRORS`

Aby ustawić tę wartość, `CaptureStartupErrors`użyj konfiguracji lub wywołania:

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>Szczegółoweerrory

Po włączeniu lub gdy `Development`środowisko jest , aplikacja przechwytuje szczegółowe błędy.

**Klucz:**`detailedErrors`  
**Typ** `bool` :`true` `1`( lub )  
**Domyślnie**:`false`  
**Zmienna środowiskowa:**`<PREFIX_>_DETAILEDERRORS`

Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do załadowania podczas uruchamiania. Mimo że wartość konfiguracji domyślnie pusty ciąg, zestawy uruchamiania hostingu zawsze zawierają zestaw aplikacji. Po udostępnieniu zestawów uruchamiania hostingu są one dodawane do zestawu aplikacji do ładowania, gdy aplikacja tworzy swoje typowe usługi podczas uruchamiania.

**Klucz:**`hostingStartupAssemblies`  
**Typ**:`string`  
**Domyślnie**: Pusty ciąg  
**Zmienna środowiskowa:**`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeAssemblies

Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do wykluczenia podczas uruchamiania.

**Klucz:**`hostingStartupExcludeAssemblies`  
**Typ**:`string`  
**Domyślnie**: Pusty ciąg  
**Zmienna środowiskowa:**`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

Port przekierowania HTTPS. Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).

**Klucz:**`https_port`  
**Typ**:`string`  
**Domyślnie:** Wartość domyślna nie jest ustawiona.  
**Zmienna środowiskowa:**`<PREFIX_>HTTPS_PORT`

Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>PreferHostingUrls (PreferHostingUrls)

Wskazuje, czy host powinien nasłuchiwania adresów URL skonfigurowanych przy 200 000 `IWebHostBuilder` adresów URL skonfigurowanych z `IServer` implementacją.

**Klucz:**`preferHostingUrls`  
**Typ** `bool` :`true` `1`( lub )  
**Domyślnie**:`true`  
**Zmienna środowiskowa:**`<PREFIX_>_PREFERHOSTINGURLS`

Aby ustawić tę wartość, użyj `PreferHostingUrls`zmiennej środowiskowej lub wywołania:

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>PreventHostingStartup

Zapobiega automatycznemu obciążaniu zestawów uruchamiania hostingu, w tym zestawów uruchamiania hostingu skonfigurowanych przez zestaw aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.

**Klucz:**`preventHostingStartup`  
**Typ** `bool` :`true` `1`( lub )  
**Domyślnie**:`false`  
**Zmienna środowiskowa:**`<PREFIX_>_PREVENTHOSTINGSTARTUP`

Aby ustawić tę wartość, użyj `UseSetting` zmiennej środowiskowej lub wywołania:

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>StartupAssembly

Zestaw do wyszukiwania `Startup` klasy.

**Klucz:**`startupAssembly`  
**Typ**:`string`  
**Domyślnie**: Zespół aplikacji  
**Zmienna środowiskowa:**`<PREFIX_>STARTUPASSEMBLY`

Aby ustawić tę wartość, należy `UseStartup`użyć zmiennej środowiskowej lub wywołania . `UseStartup`może przyjąć nazwę`string`zestawu ( )`TStartup`lub typ ( ). Jeśli `UseStartup` wywoływanych jest wiele metod, pierwszeństwo ma ostatnia.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>Adresy URL

Lista adresów IP lub adresów hostów rozdzielanych średnikami z portami i protokołami, na których serwer powinien nasłuchiwał żądań. Na przykład `http://localhost:123`. Użyj\*" ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwach hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000`). Protokół (`http://` `https://`lub ) musi być dołączony do każdego adresu URL. Obsługiwane formaty różnią się w zależności od serwera.

**Klucz:**`urls`  
**Typ**:`string`  
**Domyślnie**: `http://localhost:5000` i`https://localhost:5001`  
**Zmienna środowiskowa:**`<PREFIX_>URLS`

Aby ustawić tę wartość, użyj `UseUrls`zmiennej środowiskowej lub wywołania:

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Pustułka ma własny interfejs API konfiguracji punktu końcowego. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="webroot"></a>Webroot

[Właściwość IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) określa względną ścieżkę do statycznych zasobów aplikacji. Jeśli ścieżka nie istnieje, używany jest dostawca plików bez operacji.  

**Klucz:**`webroot`  
**Typ**:`string`  
**Domyślnie**: `wwwroot`Wartość domyślna to . Musi istnieć ścieżka do *{content root}/wwwroot.*  
**Zmienna środowiskowa:**`<PREFIX_>WEBROOT`

Aby ustawić tę wartość, należy `UseWebRoot` użyć `IWebHostBuilder`zmiennej środowiskowej lub wywołać na:

```csharp
webBuilder.UseWebRoot("public");
```

Aby uzyskać więcej informacji, zobacz:

* [Podstawy: Korzeń sieci Web](xref:fundamentals/index#web-root)
* [ContentRoot (Typ treści)](#contentroot)

## <a name="manage-the-host-lifetime"></a>Zarządzanie okresem istnienia hosta

Wywołaj metody <xref:Microsoft.Extensions.Hosting.IHost> na wbudowanej implementacji, aby uruchomić i zatrzymać aplikację. Te metody <xref:Microsoft.Extensions.Hosting.IHostedService> mają wpływ na wszystkie implementacje, które są zarejestrowane w kontenerze usługi.

### <a name="run"></a>Run

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący, dopóki host nie zostanie zamknięty.

### <a name="runasync"></a>RunAsync ( RunAsync )

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia systemu.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>umożliwia obsługę konsoli, buduje i uruchamia hosta i czeka na <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM, aby zamknąć.

### <a name="start"></a>Uruchamianie

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.

### <a name="startasync"></a>StartAsync (StartAsync)

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia hosta i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na `StartAsync`początku , który czeka, aż zakończy się przed kontynuowaniem. Może to służyć do opóźnienia uruchamiania, dopóki nie zostanie zasygnaligozowana przez zdarzenie zewnętrzne.

### <a name="stopasync"></a>StopAsync (StopAsync)

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w podanym limit czasu.

### <a name="waitforshutdown"></a>WaitForShutdown (WaitForShutdown)

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje wątek wywołujący, dopóki zamknięcie zostanie wywołane przez IHostLifetime, na przykład przez <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task> zwraca, który kończy się po zamknięciu jest <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>wyzwalany za pośrednictwem danego tokenu i wywołania .

### <a name="external-control"></a>Sterowanie zewnętrzne

Bezpośrednią kontrolę okresu istnienia hosta można osiągnąć za pomocą metod, które można wywołać zewnętrznie:

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/host/hosted-services>
