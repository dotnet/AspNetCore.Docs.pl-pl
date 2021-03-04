---
title: ASP.NET Core hosta sieci Web
author: rick-anderson
description: Dowiedz się więcej o hoście sieci Web w ASP.NET Core, który jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
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
uid: fundamentals/host/web-host
ms.openlocfilehash: fa9b1941d6dcda30855a4729dfa1cd78f897d9b6
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109978"
---
# <a name="aspnet-core-web-host"></a>ASP.NET Core hosta sieci Web

ASP.NET Core aplikacje konfigurują i uruchamiają *hosta*. Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia. Host konfiguruje co najmniej serwer i potok przetwarzania żądań. Na hoście można także skonfigurować rejestrowanie, iniekcję zależności i konfigurację.

::: moniker range=">= aspnetcore-3.0"

W tym artykule omówiono hosta sieci Web, który jest dostępny tylko w celu zapewnienia zgodności z poprzednimi wersjami. Szablony ASP.NET Core tworzą [hosta ogólnego platformy .NET](<xref:fundamentals/host/generic-host>), który jest zalecany dla wszystkich typów aplikacji.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

W tym artykule omówiono hosta sieci Web, który służy do hostowania aplikacji sieci Web. W przypadku innych rodzajów aplikacji należy użyć [hosta ogólnego](xref:fundamentals/host/generic-host).

::: moniker-end

## <a name="set-up-a-host"></a>Konfigurowanie hosta

Utwórz hosta przy użyciu wystąpienia [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder). Jest to zwykle wykonywane w punkcie wejścia aplikacji, w `Main` metodzie.

W szablonach projektu `Main` znajduje się w *program.cs*. Typowe wywołania aplikacji [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) do rozpoczęcia konfigurowania hosta:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

Kod, który wywołuje `CreateDefaultBuilder` , znajduje się w metodzie o nazwie `CreateWebHostBuilder` , która oddziela ją od kodu w ramach `Main` tego wywołania `Run` w obiekcie konstruktora. Ta separacja jest wymagana, jeśli używasz [narzędzi Entity Framework Core](/ef/core/miscellaneous/cli/). Narzędzia oczekują na znalezienie `CreateWebHostBuilder` metody, którą mogą wywołać w czasie projektowania, aby skonfigurować hosta bez uruchamiania aplikacji. Alternatywą jest zaimplementowanie `IDesignTimeDbContextFactory` . Aby uzyskać więcej informacji, zobacz [Tworzenie DbContext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).

Metoda `CreateDefaultBuilder` wykonuje następujące zadania:

::: moniker range=">= aspnetcore-5.0"
* Konfiguruje serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci Web przy użyciu dostawców konfiguracji hostingu aplikacji. Aby poznać domyślne opcje serwera Kestrel, zobacz <xref:fundamentals/servers/kestrel/options> .
::: moniker-end
::: moniker range="< aspnetcore-5.0"
* Konfiguruje serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci Web przy użyciu dostawców konfiguracji hostingu aplikacji. Aby poznać domyślne opcje serwera Kestrel, zobacz <xref:fundamentals/servers/kestrel#kestrel-options> .
::: moniker-end
* Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) dla ścieżki zwróconej przez [katalog. GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).
* Ładuje [konfigurację hosta](#host-configuration-values) z:
  * Zmienne środowiskowe poprzedzone prefiksem `ASPNETCORE_` (na przykład `ASPNETCORE_ENVIRONMENT` ).
  * Argumenty wiersza polecenia.
* Ładuje konfigurację aplikacji w następującej kolejności:
  * *appsettings.json*.
  * *appSettings. {Environment}. JSON*.
  * [Klucze tajne użytkownika](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku przy użyciu zestawu wpisów.
  * Zmienne środowiskowe.
  * Argumenty wiersza polecenia.
* Konfiguruje [Rejestrowanie](xref:fundamentals/logging/index) dla konsoli i danych wyjściowych debugowania. Rejestrowanie obejmuje reguły [filtrowania dzienników](xref:fundamentals/logging/index#log-filtering) określone w sekcji konfiguracyjnej rejestrowania *appsettings.json* lub *appSettings. { Environment} plik JSON* .
* Po uruchomieniu usług IIS za pomocą [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module)program `CreateDefaultBuilder` włącza [integrację usług IIS](xref:host-and-deploy/iis/index), która konfiguruje podstawowy adres i port aplikacji. Integracja usług IIS konfiguruje również aplikację do [przechwytywania błędów uruchamiania](#capture-startup-errors). Aby poznać domyślne opcje usług IIS, zobacz <xref:host-and-deploy/iis/index#iis-options> .
* Ustawia [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true` , jeśli środowisko aplikacji jest opracowywane. Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](#scope-validation).

Konfiguracja zdefiniowana przez `CreateDefaultBuilder` można zastąpić i rozszerzyć przez [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)i inne metody i metody rozszerzające [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder). Poniżej przedstawiono kilka przykładów:

* [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) służy do określania dodatkowych `IConfiguration` aplikacji. Następujące `ConfigureAppConfiguration` wywołanie dodaje delegata w celu uwzględnienia konfiguracji aplikacji w pliku *appsettings.xml* . `ConfigureAppConfiguration` może być wywoływana wiele razy. Należy zauważyć, że ta konfiguracja nie ma zastosowania do hosta (na przykład adresów URL serwera lub środowiska). Zapoznaj się z sekcją [wartości konfiguracji hosta](#host-configuration-values) .

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* Następujące `ConfigureLogging` wywołanie dodaje delegata w celu skonfigurowania minimalnego poziomu rejestrowania ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) na wartość [LogLevel. Warning](/dotnet/api/microsoft.extensions.logging.loglevel). To ustawienie zastępuje ustawienia w *appsettings.Development.json* ( `LogLevel.Debug` ) i *appsettings.Production.json* ( `LogLevel.Error` ) skonfigurowany przez `CreateDefaultBuilder` . `ConfigureLogging` może być wywoływana wiele razy.

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* Następujące wywołanie `ConfigureKestrel` przesłania domyślne [limity. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30 000 000 bajtów ustanowionych podczas konfigurowania Kestrel przez `CreateDefaultBuilder` :

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Następujące wywołanie [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) zastępuje domyślne [limity. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30 000 000 bajtów ustanowionych podczas konfigurowania Kestrel przez `CreateDefaultBuilder` :

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

[Katalog główny zawartości](xref:fundamentals/index#content-root) określa, gdzie host wyszukuje pliki zawartości, takie jak pliki widoku MVC. Gdy aplikacja jest uruchamiana z folderu głównego projektu, folder główny projektu jest używany jako katalog główny zawartości. Jest to ustawienie domyślne używane w programie [Visual Studio](https://visualstudio.microsoft.com) i w [nowych szablonach dotnet](/dotnet/core/tools/dotnet-new).

Aby uzyskać więcej informacji na temat konfiguracji aplikacji, zobacz <xref:fundamentals/configuration/index> .

> [!NOTE]
> Alternatywą dla użycia metody statycznej `CreateDefaultBuilder` jest utworzenie hosta z [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) jest obsługiwane podejście ASP.NET Core 2. x.

Podczas konfigurowania hosta można dostarczyć metody [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) i [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) . Jeśli `Startup` Klasa jest określona, musi definiować `Configure` metodę. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>. Wiele wywołań do `ConfigureServices` dołączenia do siebie nawzajem. Wiele wywołań `Configure` lub `UseStartup` `WebHostBuilder` Zastąp poprzednie ustawienia.

## <a name="host-configuration-values"></a>Wartości konfiguracji hosta

[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) opiera się na następujących podejściach do ustawiania wartości konfiguracji hosta:

* Konfiguracja konstruktora hostów, która zawiera zmienne środowiskowe w formacie `ASPNETCORE_{configurationKey}` . Na przykład `ASPNETCORE_ENVIRONMENT`.
* Rozszerzenia, takie jak [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) i [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (zobacz sekcję [Konfiguracja zastąpień](#override-configuration) ).
* [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) i skojarzony klucz. Podczas ustawiania wartości za pomocą `UseSetting` , wartość jest ustawiana jako ciąg, niezależnie od typu.

Host używa dowolnej opcji ustawia wartość Last. Aby uzyskać więcej informacji, zobacz [Przesłoń konfigurację](#override-configuration) w następnej sekcji.

### <a name="application-key-name"></a>Klucz aplikacji (nazwa)

::: moniker range=">= aspnetcore-3.0"

`IWebHostEnvironment.ApplicationName`Właściwość jest ustawiana automatycznie, gdy [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) lub [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) jest wywoływana podczas konstruowania hosta. Wartość jest ustawiana na nazwę zestawu zawierającego punkt wejścia aplikacji. Aby jawnie ustawić wartość, użyj [WebHostDefaults. ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Właściwość [IHostingEnvironment. ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) jest ustawiana automatycznie, gdy [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) lub [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) jest wywoływana podczas konstruowania hosta. Wartość jest ustawiana na nazwę zestawu zawierającego punkt wejścia aplikacji. Aby jawnie ustawić wartość, użyj [WebHostDefaults. ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):

::: moniker-end

**Klucz**: ApplicationName  
**Typ**: *ciąg*  
**Wartość domyślna**: Nazwa zestawu zawierającego punkt wejścia aplikacji.  
**Ustaw przy użyciu**: `UseSetting`  
**Zmienna środowiskowa**: `ASPNETCORE_APPLICATIONNAME`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a>Błędy uruchamiania przechwytywania

To ustawienie steruje przechwytywaniem błędów uruchamiania.

**Klucz**: captureStartupErrors  
**Typ**: *bool* ( `true` or `1` )  
**Domyślnie**: wartość domyślna to, `false` chyba że aplikacja działa z Kestrel za usługami IIS, gdzie wartość domyślna to `true` .  
**Ustaw przy użyciu**: `CaptureStartupErrors`  
**Zmienna środowiskowa**: `ASPNETCORE_CAPTURESTARTUPERRORS`

Gdy `false` , błędy podczas uruchamiania, kończy się hostem. Gdy `true` host przechwytuje wyjątki podczas uruchamiania, a następnie próbuje uruchomić serwer.

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a>Katalog główny zawartości

To ustawienie określa, gdzie ASP.NET Core rozpoczyna wyszukiwanie plików zawartości.

**Klucz**: contentRoot  
**Typ**: *ciąg*  
**Domyślnie**: Domyślnie folder, w którym znajduje się zestaw aplikacji.  
**Ustaw przy użyciu**: `UseContentRoot`  
**Zmienna środowiskowa**: `ASPNETCORE_CONTENTROOT`

Katalog główny zawartości jest również używany jako ścieżka podstawowa dla [katalogu głównego sieci Web](xref:fundamentals/index#web-root). Jeśli ścieżka katalogu głównego zawartości nie istnieje, uruchomienie hosta nie powiedzie się.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

Aby uzyskać więcej informacji, zobacz:

* [Podstawy: zawartość główna](xref:fundamentals/index#content-root)
* [Katalog główny sieci Web](#web-root)

### <a name="detailed-errors"></a>Szczegóły błędów

Określa, czy mają być przechwytywane szczegółowe błędy.

**Klucz**: detailedErrors  
**Typ**: *bool* ( `true` or `1` )  
**Wartość domyślna**: FAŁSZ  
**Ustaw przy użyciu**: `UseSetting`  
**Zmienna środowiskowa**: `ASPNETCORE_DETAILEDERRORS`

Po włączeniu (lub gdy <a href="#environment">środowisko</a> jest ustawione na `Development` ), aplikacja przechwytuje szczegółowe wyjątki.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a>Środowisko

Ustawia środowisko aplikacji.

**Klucz**: środowisko  
**Typ**: *ciąg*  
**Wartość domyślna**: produkcja  
**Ustaw przy użyciu**: `UseEnvironment`  
**Zmienna środowiskowa**: `ASPNETCORE_ENVIRONMENT`

Dla środowiska można ustawić dowolną wartość. Wartości zdefiniowane przez platformę obejmują `Development` , `Staging` , i `Production` . W wartościach nie jest rozróżniana wielkość liter. Domyślnie *środowisko* jest odczytywane ze `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej. W przypadku korzystania z [programu Visual Studio](https://visualstudio.microsoft.com)zmienne środowiskowe można ustawić w *launchSettings.js* pliku. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a>Hostowanie zestawów startowych

Ustawia zestawy uruchomieniowe obsługujące aplikację.

**Klucz**: hostingStartupAssemblies  
**Typ**: *ciąg*  
**Wartość domyślna**: pusty ciąg  
**Ustaw przy użyciu**: `UseSetting`  
**Zmienna środowiskowa**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`

Rozdzielany średnikami ciąg początkowych zestawów startowych do załadowania podczas uruchamiania.

Mimo że wartość konfiguracji jest domyślnie pustym ciągiem, zestaw startowy obsługujący zawsze zawiera zestaw aplikacji. W przypadku udostępniania zestawów startowych są one dodawane do zestawu aplikacji do załadowania, gdy aplikacja kompiluje swoje popularne usługi podczas uruchamiania.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a>Port HTTPS

Ustaw port przekierowania HTTPS. Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).

**Klucz**: https_port **Typ**: *ciąg* 
 **domyślny**: nie ustawiono wartości domyślnej.
**Ustaw przy użyciu**: `UseSetting` 
 **zmienna środowiskowa**:`ASPNETCORE_HTTPS_PORT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a>Hosting — wykluczanie zestawów

Rozdzielany średnikami ciąg początkowych zestawów uruchamiania, który ma zostać wykluczony podczas uruchamiania.

**Klucz**: hostingStartupExcludeAssemblies  
**Typ**: *ciąg*  
**Wartość domyślna**: pusty ciąg  
**Ustaw przy użyciu**: `UseSetting`  
**Zmienna środowiskowa**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a>Preferuj adresy URL hostingu

Wskazuje, czy host powinien nasłuchiwać adresów URL skonfigurowanych przy użyciu `WebHostBuilder` zamiast tych skonfigurowanych przy użyciu `IServer` implementacji.

**Klucz**: preferHostingUrls  
**Typ**: *bool* ( `true` or `1` )  
Wartość **Domyślna**: prawda  
**Ustaw przy użyciu**: `PreferHostingUrls`  
**Zmienna środowiskowa**: `ASPNETCORE_PREFERHOSTINGURLS`

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a>Zapobiegaj uruchamianiu hostingu

Zapobiega automatycznemu ładowaniu zestawów startowych hostingu, w tym hostingu zestawów startowych skonfigurowanych przez zestaw aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.

**Klucz**: preventHostingStartup  
**Typ**: *bool* ( `true` or `1` )  
**Wartość domyślna**: FAŁSZ  
**Ustaw przy użyciu**: `UseSetting`  
**Zmienna środowiskowa**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a>Adresy URL serwera

Wskazuje adresy IP lub adresy hosta z portami i protokołami, na których serwer powinien nasłuchiwać żądań.

**Klucz**: adresy URL  
**Typ**: *ciąg*  
**Wartość domyślna**: http://localhost:5000  
**Ustaw przy użyciu**: `UseUrls`  
**Zmienna środowiskowa**: `ASPNETCORE_URLS`

Ustaw na rozdzieloną średnikami (;) Lista prefiksów adresów URL, do których serwer powinien reagować. Na przykład `http://localhost:123`. Użyj " \* ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwie hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000` ). Protokół ( `http://` lub `https://` ) musi być dołączony do każdego adresu URL. Obsługiwane formaty różnią się między serwerami.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

::: moniker range=">= aspnetcore-5.0"
Kestrel ma własny interfejs API konfiguracji punktu końcowego. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel/endpoints>.
::: moniker-end
::: moniker range="< aspnetcore-5.0"
Kestrel ma własny interfejs API konfiguracji punktu końcowego. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.
::: moniker-end

### <a name="shutdown-timeout"></a>Limit czasu zamykania

Określa czas oczekiwania na zamknięcie hosta sieci Web.

**Klucz**: shutdownTimeoutSeconds  
**Typ**: *int*  
**Wartość domyślna**: 5  
**Ustaw przy użyciu**: `UseShutdownTimeout`  
**Zmienna środowiskowa**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`

Chociaż klucz akceptuje *int* z `UseSetting` (na przykład `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")` ), Metoda rozszerzenia [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) przyjmuje wartość [TimeSpan](/dotnet/api/system.timespan).

Podczas okresu przekroczenia limitu czasu hostowanie:

* Wyzwala [IApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).
* Próbuje zatrzymać usługi hostowane, rejestrując błędy dla usług, których nie można zatrzymać.

Jeśli limit czasu upłynie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji. Usługi są zatrzymane nawet wtedy, gdy nie zakończyły przetwarzania. Jeśli usługi wymagają dodatkowego czasu na zatrzymanie, zwiększ limit czasu.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a>Zestaw startowy

Określa zestaw, który ma być wyszukiwany dla `Startup` klasy.

**Klucz**: startupAssembly  
**Typ**: *ciąg*  
**Domyślnie**: zestaw aplikacji  
**Ustaw przy użyciu**: `UseStartup`  
**Zmienna środowiskowa**: `ASPNETCORE_STARTUPASSEMBLY`

`string`Można odwołać się do zestawu według nazwy () lub typu ( `TStartup` ). Jeśli `UseStartup` wywoływana jest wiele metod, pierwszeństwo ma Ostatnia.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a>Katalog główny sieci Web

Ustawia ścieżkę względną do statycznych zasobów aplikacji.

**Klucz**: Webroot  
**Typ**: *ciąg*  
**Wartość domyślna**: wartość domyślna to `wwwroot` . Ścieżka do *elementu {content root}/wwwroot* musi istnieć. Jeśli ścieżka nie istnieje, jest używany dostawca plików No-op.  
**Ustaw przy użyciu**: `UseWebRoot`  
**Zmienna środowiskowa**: `ASPNETCORE_WEBROOT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

Aby uzyskać więcej informacji, zobacz:

* [Podstawy: katalog główny sieci Web](xref:fundamentals/index#web-root)
* [Katalog główny zawartości](#content-root)

## <a name="override-configuration"></a>Zastąp konfigurację

Skonfiguruj hosta sieci Web za pomocą [konfiguracji](xref:fundamentals/configuration/index) . W poniższym przykładzie Konfiguracja hosta jest opcjonalnie określona w *hostsettings.js* pliku. Każda konfiguracja załadowana z *hostsettings.jsw* pliku może zostać przesłonięta przez argumenty wiersza polecenia. Utworzona konfiguracja (w programie `config` ) służy do konfigurowania hosta z [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration). `IWebHostBuilder`Konfiguracja jest dodawana do konfiguracji aplikacji, ale nie ma ona &mdash; `ConfigureAppConfiguration` wpływu na `IWebHostBuilder` konfigurację.

Zastępowanie konfiguracji podanej przy `UseUrls` użyciu *hostsettings.jsnajpierw w* konfiguracji, argument wiersza polecenia config sekund:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

*hostsettings.js*:

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) kopiuje tylko klucze z dostarczonej `IConfiguration` do konfiguracji konstruktora hostów. W związku z tym `reloadOnChange: true` nie ma żadnego wpływu na ustawienia plików JSON, ini i XML.

Aby określić hosta na określonym adresie URL, wymagana wartość może zostać przeniesiona z wiersza polecenia podczas wykonywania [przebiegu dotnet](/dotnet/core/tools/dotnet-run). Argument wiersza polecenia zastępuje `urls` wartość z *hostsettings.jsw* pliku, a serwer nasłuchuje na porcie 8080:

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a>Zarządzanie hostem

**Uruchom**

`Run`Metoda uruchamia aplikację sieci Web i blokuje wątek wywołujący do momentu wyłączenia hosta:

```csharp
host.Run();
```

**Początek**

Uruchom hosta w sposób nieblokowany przez wywołanie jego `Start` metody:

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

Jeśli lista adresów URL jest przenoszona do `Start` metody, nasłuchuje na określonych adresach URL:

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

Aplikacja może inicjować i uruchamiać nowy host przy użyciu wstępnie skonfigurowanych ustawień domyślnych `CreateDefaultBuilder` przy użyciu statycznej metody wygodnej. Metody te służą do uruchamiania serwera bez danych wyjściowych konsoli i [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) poczekać na przerwanie (Ctrl-C/SIGINT lub SIGTERM):

**Rozpocznij (aplikacja RequestDelegate)**

Zacznij od `RequestDelegate` :

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Wykonaj żądanie w przeglądarce, aby `http://localhost:5000` otrzymać odpowiedź "Hello World!" `WaitForShutdown` bloki do momentu wystawienia przerwy (Ctrl-C/SIGINT lub SIGTERM). Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na zamknięcie naciśnięcia.

**Rozpocznij (adres URL ciągu, aplikacja RequestDelegate)**

Zacznij od adresu URL i `RequestDelegate` :

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Daje ten sam wynik jako **Start (RequestDelegate App)**, z wyjątkiem tego, że aplikacja reaguje na `http://localhost:8080` .

**Uruchom (Akcja \<IRouteBuilder> routeBuilder)**

Użyj wystąpienia programu `IRouteBuilder` ([Microsoft. AspNetCore. Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)), aby użyć oprogramowania pośredniczącego routingu:

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Użyj poniższego żądania przeglądarki z przykładem:

| Żądanie                                    | Reakcja                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | Witaj, Martin!                           |
| `http://localhost:5000/buenosdias/Catrina` | Buenos Dias, Catrina!                    |
| `http://localhost:5000/throw/ooops!`       | Zgłasza wyjątek z ciągiem "ooops!" |
| `http://localhost:5000/throw`              | Zgłasza wyjątek z ciągiem "zapomniano" |
| `http://localhost:5000/Sante/Kevin`        | Sante, Jan!                            |
| `http://localhost:5000`                    | Hello World!                             |

`WaitForShutdown` bloki do momentu wystawienia przerwy (Ctrl-C/SIGINT lub SIGTERM). Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na zamknięcie naciśnięcia.

**Rozpocznij (ciąg URL: Action \<IRouteBuilder> routeBuilder)**

Użyj adresu URL i wystąpienia `IRouteBuilder` :

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Daje ten sam wynik jako **początek (Akcja \<IRouteBuilder> routeBuilder)**, z wyjątkiem tego, że aplikacja reaguje na `http://localhost:8080` .

**StartWith ( \<IApplicationBuilder> aplikacja działania)**

Podaj delegata, aby skonfigurować `IApplicationBuilder` :

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Wykonaj żądanie w przeglądarce, aby `http://localhost:5000` otrzymać odpowiedź "Hello World!" `WaitForShutdown` bloki do momentu wystawienia przerwy (Ctrl-C/SIGINT lub SIGTERM). Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na zamknięcie naciśnięcia.

**StartWith (adres URL ciągu, \<IApplicationBuilder> aplikacja akcji)**

Podaj adres URL i delegata, aby skonfigurować `IApplicationBuilder` :

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Daje ten sam wynik jako **StartWith ( \<IApplicationBuilder> aplikacja działania)**, z wyjątkiem tego, że aplikacja reaguje na `http://localhost:8080` .

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a>IWebHostEnvironment, interfejs

`IWebHostEnvironment`Interfejs zawiera informacje o środowisku hostingu aplikacji sieci Web. Użyj [iniekcji konstruktorów](xref:fundamentals/dependency-injection) , aby uzyskać `IWebHostEnvironment` w celu użycia jej właściwości i metod rozszerzających:

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

[Podejście oparte na Konwencji](xref:fundamentals/environments#environment-based-startup-class-and-methods) może służyć do konfigurowania aplikacji podczas uruchamiania na podstawie środowiska. Alternatywnie można wstrzyknąć `IWebHostEnvironment` do `Startup` konstruktora do użycia w `ConfigureServices` :

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> Oprócz `IsDevelopment` metody rozszerzenia, `IWebHostEnvironment` oferty `IsStaging` , `IsProduction` i `IsEnvironment(string environmentName)` metody. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

`IWebHostEnvironment`Usługę można również wstrzyknąć bezpośrednio do `Configure` metody w celu skonfigurowania potoku przetwarzania:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

`IWebHostEnvironment` można wstrzyknąć do `Invoke` metody podczas tworzenia niestandardowego [oprogramowania pośredniczącego](xref:fundamentals/middleware/write):

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a>IHostingEnvironment, interfejs

[Interfejs IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) zawiera informacje o środowisku hostingu aplikacji sieci Web. Użyj [iniekcji konstruktorów](xref:fundamentals/dependency-injection) , aby uzyskać `IHostingEnvironment` w celu użycia jej właściwości i metod rozszerzających:

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

[Podejście oparte na Konwencji](xref:fundamentals/environments#environment-based-startup-class-and-methods) może służyć do konfigurowania aplikacji podczas uruchamiania na podstawie środowiska. Alternatywnie można wstrzyknąć `IHostingEnvironment` do `Startup` konstruktora do użycia w `ConfigureServices` :

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> Oprócz `IsDevelopment` metody rozszerzenia, `IHostingEnvironment` oferty `IsStaging` , `IsProduction` i `IsEnvironment(string environmentName)` metody. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

`IHostingEnvironment`Usługę można również wstrzyknąć bezpośrednio do `Configure` metody w celu skonfigurowania potoku przetwarzania:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

`IHostingEnvironment` można wstrzyknąć do `Invoke` metody podczas tworzenia niestandardowego [oprogramowania pośredniczącego](xref:fundamentals/middleware/write):

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a>IHostApplicationLifetime, interfejs

`IHostApplicationLifetime` zezwala na działania po uruchomieniu i zamknięcia. Trzy właściwości interfejsu są tokenami anulowania używanymi do rejestrowania `Action` metod, które definiują zdarzenia uruchamiania i zamykania.

| Token anulowania    | Wyzwalane, gdy&#8230; |
| --------------------- | --------------------- |
| `ApplicationStarted`  | Host został w pełni uruchomiony. |
| `ApplicationStopped`  | Host kończy bezpieczne zamknięcie. Wszystkie żądania powinny być przetwarzane. Bloki zamknięcia do momentu zakończenia tego zdarzenia. |
| `ApplicationStopping` | Host wykonuje bezpieczne zamknięcie. Żądania mogą nadal być przetwarzane. Bloki zamknięcia do momentu zakończenia tego zdarzenia. |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

`StopApplication` żąda zakończenia aplikacji. Następująca Klasa służy `StopApplication` do bezpiecznego zamykania aplikacji w przypadku `Shutdown` wywołania metody klasy:

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
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

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a>IApplicationLifetime, interfejs

[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) umożliwia działanie po uruchomieniu i zamknięciu. Trzy właściwości interfejsu są tokenami anulowania używanymi do rejestrowania `Action` metod, które definiują zdarzenia uruchamiania i zamykania.

| Token anulowania    | Wyzwalane, gdy&#8230; |
| --------------------- | --------------------- |
| [ApplicationStarted](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | Host został w pełni uruchomiony. |
| [ApplicationStopped](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | Host kończy bezpieczne zamknięcie. Wszystkie żądania powinny być przetwarzane. Bloki zamknięcia do momentu zakończenia tego zdarzenia. |
| [ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | Host wykonuje bezpieczne zamknięcie. Żądania mogą nadal być przetwarzane. Bloki zamknięcia do momentu zakończenia tego zdarzenia. |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) żądania zakończenia aplikacji. Następująca Klasa służy `StopApplication` do bezpiecznego zamykania aplikacji w przypadku `Shutdown` wywołania metody klasy:

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

## <a name="scope-validation"></a>Weryfikacja zakresu

[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) ustawia [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true` Jeśli środowisko aplikacji jest opracowywane.

Gdy `ValidateScopes` jest ustawiona na `true` , domyślny dostawca usług sprawdza, czy:

* Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.
* Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.

Główny dostawca usług jest tworzony, gdy wywoływana jest [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) . Okres istnienia dostawcy usług głównych odnosi się do okresu istnienia aplikacji/serwera, gdy dostawca zaczyna się od aplikacji i jest usuwany po zamknięciu aplikacji.

Usługi o określonym zakresie są usuwane przez kontener, który go utworzył. Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest wyłączony. Sprawdzanie poprawności zakresów usług przechwytuje te sytuacje, gdy `BuildServiceProvider` jest wywoływana.

Aby zawsze weryfikować zakresy, w tym w środowisku produkcyjnym, należy skonfigurować [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) z [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) na konstruktorze hosta:

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
