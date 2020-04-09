---
title: ASP.NET Core Web Host
author: rick-anderson
description: Dowiedz się więcej o hostze internetowym w ASP.NET Core, który odpowiada za uruchamianie aplikacji i zarządzanie dożywotnim okresem.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/host/web-host
ms.openlocfilehash: e02d6efcb3aec1329469b8654e66ba845870421a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666713"
---
# <a name="aspnet-core-web-host"></a>ASP.NET Core Web Host

ASP.NET aplikacje Core konfigurują i uruchamiają *hosta.* Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia. Co najmniej host konfiguruje serwer i potok przetwarzania żądań. Host można również skonfigurować rejestrowanie, iniekcji zależności i konfiguracji.

::: moniker range=">= aspnetcore-3.0"

W tym artykule omówiono hosta sieci Web, który pozostaje dostępny tylko dla zgodności z powrotem. [Host ogólny](xref:fundamentals/host/generic-host) jest zalecany dla wszystkich typów aplikacji.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ten artykuł obejmuje hosta sieci Web, który służy do hostowania aplikacji sieci Web. W przypadku innych rodzajów aplikacji użyj [hosta ogólnego](xref:fundamentals/host/generic-host).

::: moniker-end

## <a name="set-up-a-host"></a>Konfigurowanie hosta

Utwórz hosta przy użyciu wystąpienia [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder). Jest to zazwyczaj wykonywane w punkcie wejścia `Main` aplikacji, metoda.

W szablonach projektu `Main` znajduje się w *Program.cs*. Typowa aplikacja wywołuje [CreateDefaultBuilder,](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) aby rozpocząć konfigurowanie hosta:

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

Kod, który `CreateDefaultBuilder` wywołuje jest `CreateWebHostBuilder`w metodzie o nazwie `Main` , `Run` która oddziela go od kodu w tym wywołuje na builder obiektu. Ta separacja jest wymagana w przypadku korzystania z [narzędzi Entity Framework Core](/ef/core/miscellaneous/cli/). Narzędzia oczekują, aby `CreateWebHostBuilder` znaleźć metodę, którą można wywołać w czasie projektowania, aby skonfigurować hosta bez uruchamiania aplikacji. Alternatywą jest `IDesignTimeDbContextFactory`wdrożenie . Aby uzyskać więcej informacji, zobacz [Tworzenie dbcontext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).

Metoda `CreateDefaultBuilder` wykonuje następujące zadania:

* Konfiguruje serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci web przy użyciu dostawców konfiguracji hostingu aplikacji. Aby zapoznać się z domyślnymi <xref:fundamentals/servers/kestrel#kestrel-options>opcjami serwera Kestrel, zobacz .
* Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na ścieżkę zwróconą przez [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).
* Ładuje [konfigurację hosta](#host-configuration-values) z:
  * Zmienne środowiskowe poprzedzone `ASPNETCORE_` (na przykład). `ASPNETCORE_ENVIRONMENT`
  * Argumenty wiersza polecenia.
* Ładuje konfigurację aplikacji w następującej kolejności od:
  * *appsettings.json*.
  * *appsettings. {Środowisko}.json*.
  * [Tajny menedżer,](xref:security/app-secrets) gdy aplikacja `Development` działa w środowisku przy użyciu zestawu wejścia.
  * Zmienne środowiskowe.
  * Argumenty wiersza polecenia.
* Konfiguruje [rejestrowanie](xref:fundamentals/logging/index) dla danych wyjściowych konsoli i debugowania. Rejestrowanie obejmuje [reguły filtrowania dziennika](xref:fundamentals/logging/index#log-filtering) określone w sekcji konfiguracji rejestrowania *pliku appsettings.json* lub *appsettings.{ Środowisko}.json.*
* Gdy działa za IIS z ASP.NET Core `CreateDefaultBuilder` [Module](xref:host-and-deploy/aspnet-core-module), umożliwia [integrację IIS](xref:host-and-deploy/iis/index), który konfiguruje adres podstawowy aplikacji i portu. Integracja iis konfiguruje również aplikację do [przechwytywania błędów uruchamiania](#capture-startup-errors). Aby zapoznać się z <xref:host-and-deploy/iis/index#iis-options>domyślnymi opcjami iis, zobacz .
* Ustawia [ServiceProviderOptions.ValidateScopes,](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) `true` jeśli środowisko aplikacji jest rozwój. Aby uzyskać więcej informacji, zobacz [Sprawdzanie poprawności zakresu](#scope-validation).

Konfiguracja `CreateDefaultBuilder` zdefiniowana przez może zostać zastąpiona i rozszerzona przez [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)i inne metody i metody rozszerzenia [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder). Kilka przykładów następują:

* [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) służy do `IConfiguration` określenia dodatkowych dla aplikacji. Poniższe `ConfigureAppConfiguration` wywołanie dodaje pełnomocnika do uwzględnienia konfiguracji aplikacji w pliku *appsettings.xml.* `ConfigureAppConfiguration`można nazwać wiele razy. Należy zauważyć, że ta konfiguracja nie ma zastosowania do hosta (na przykład adresów URL serwera lub środowiska). Zobacz sekcję [Wartości konfiguracji hosta.](#host-configuration-values)

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* Następujące `ConfigureLogging` wywołanie dodaje pełnomocnika, aby skonfigurować minimalny poziom rejestrowania[(SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) do [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel). To ustawienie zastępuje ustawienia w *ustawieniach aplikacji. Development.json* `LogLevel.Debug`( ) i *appsettings. Production.json* `LogLevel.Error`( ) `CreateDefaultBuilder`skonfigurowany przez . `ConfigureLogging`można nazwać wiele razy.

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* Następujące wywołanie `ConfigureKestrel` zastępowania domyślnego [rozmiaru Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30,000,000 bajtów ustanowionych `CreateDefaultBuilder`podczas konfigurowania Kestrel przez:

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Następujące wywołanie [usekestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) zastępuje [domyślny Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30,000,000 bajtów ustanowiony, gdy `CreateDefaultBuilder`Kestrel został skonfigurowany przez:

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

[Katalog główny zawartości](xref:fundamentals/index#content-root) określa, gdzie host wyszukuje pliki zawartości, takie jak pliki widoku MVC. Po uruchomieniu aplikacji z folderu głównego projektu folder główny projektu jest używany jako katalog główny zawartości. Jest to wartość domyślna używana w [programie Visual Studio](https://visualstudio.microsoft.com) i nowych [szablonach dotnet](/dotnet/core/tools/dotnet-new).

Aby uzyskać więcej informacji <xref:fundamentals/configuration/index>na temat konfiguracji aplikacji, zobacz .

> [!NOTE]
> Jako alternatywę dla korzystania `CreateDefaultBuilder` z metody statycznej, tworzenie hosta z [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) jest obsługiwane podejście z ASP.NET Core 2.x.

Podczas konfigurowania hosta można [konfigurować](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) i [konfigurować metody usług.](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) Jeśli `Startup` klasa jest określona, `Configure` musi zdefiniować metodę. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>. Wiele wywołań do `ConfigureServices` dołączenia do siebie. Wiele wywołań `UseStartup` do `WebHostBuilder` `Configure` lub na zastąp poprzednie ustawienia.

## <a name="host-configuration-values"></a>Wartości konfiguracji hosta

[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) opiera się na następujących podejściach, aby ustawić wartości konfiguracji hosta:

* Konfiguracja konstruktora hostów, która `ASPNETCORE_{configurationKey}`zawiera zmienne środowiskowe w formacie . Na przykład `ASPNETCORE_ENVIRONMENT`.
* Rozszerzenia, takie jak [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) i [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (zobacz sekcję [Zastąp konfigurację).](#override-configuration)
* [Użyjstawienie](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) i skojarzony klucz. Podczas ustawiania `UseSetting`wartości z , wartość jest ustawiana jako ciąg niezależnie od typu.

Host używa dowolnej opcji ustawia wartość last. Aby uzyskać więcej informacji, zobacz [Zastępowanie konfiguracji](#override-configuration) w następnej sekcji.

### <a name="application-key-name"></a>Klucz aplikacji (nazwa)

::: moniker range=">= aspnetcore-3.0"

Właściwość `IWebHostEnvironment.ApplicationName` jest automatycznie ustawiana, gdy [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) lub [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) jest wywoływana podczas budowy hosta. Wartość jest ustawiona na nazwę zestawu zawierającego punkt wejścia aplikacji. Aby ustawić wartość jawnie, użyj [WebHostDefaults.ApplicationKey:](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Właściwość IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) jest automatycznie ustawiana, gdy [usestartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) lub [configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) jest wywoływana podczas budowy hosta. Wartość jest ustawiona na nazwę zestawu zawierającego punkt wejścia aplikacji. Aby ustawić wartość jawnie, użyj [WebHostDefaults.ApplicationKey:](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)

::: moniker-end

**Klucz:** nazwa aplikacji  
**Typ**: *ciąg znaków*  
**Domyślnie**: Nazwa zestawu zawierającego punkt wejścia aplikacji.  
**Zestaw za pomocą**:`UseSetting`  
**Zmienna środowiskowa:**`ASPNETCORE_APPLICATIONNAME`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a>Przechwytywanie błędów uruchamiania

To ustawienie steruje przechwytywaniem błędów uruchamiania.

**Klucz:** captureStartupErrors  
**Typ**: *bool* (`true` lub `1`)  
**Domyślnie:** `false` Domyślnie, chyba że aplikacja działa z Kestrel `true`za IIS, gdzie domyślnie jest .  
**Zestaw za pomocą**:`CaptureStartupErrors`  
**Zmienna środowiskowa:**`ASPNETCORE_CAPTURESTARTUPERRORS`

Kiedy `false`, błędy podczas uruchamiania spowodować wyjście hosta. Gdy `true`host przechwytuje wyjątki podczas uruchamiania i próbuje uruchomić serwer.

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a>Katalog główny zawartości

To ustawienie określa, gdzie ASP.NET Core rozpoczyna wyszukiwanie plików zawartości.

**Klucz:** contentRoot  
**Typ**: *ciąg znaków*  
**Domyślnie**: Domyślnie folder, w którym znajduje się zestaw aplikacji.  
**Zestaw za pomocą**:`UseContentRoot`  
**Zmienna środowiskowa:**`ASPNETCORE_CONTENTROOT`

Katalog główny zawartości jest również używany jako ścieżka podstawowa dla [katalogu głównego sieci Web](xref:fundamentals/index#web-root). Jeśli ścieżka katalogu głównego zawartości nie istnieje, uruchomienie hosta nie powiedzie się.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

Aby uzyskać więcej informacji, zobacz:

* [Podstawy: Katalog główny treści](xref:fundamentals/index#content-root)
* [Katalog główny sieci Web](#web-root)

### <a name="detailed-errors"></a>Szczegółowe błędy

Określa, czy szczegółowe błędy powinny zostać przechwycone.

**Klucz:** detailedErrors  
**Typ**: *bool* (`true` lub `1`)  
**Domyślnie**: false  
**Zestaw za pomocą**:`UseSetting`  
**Zmienna środowiskowa:**`ASPNETCORE_DETAILEDERRORS`

Po włączeniu (lub <a href="#environment">Environment</a> gdy środowisko `Development`jest ustawione na), aplikacja przechwytuje szczegółowe wyjątki.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a>Środowisko

Ustawia środowisko aplikacji.

**Klucz:** środowisko  
**Typ**: *ciąg znaków*  
**Domyślnie**: Produkcja  
**Zestaw za pomocą**:`UseEnvironment`  
**Zmienna środowiskowa:**`ASPNETCORE_ENVIRONMENT`

Środowisko można ustawić na dowolną wartość. Wartości zdefiniowane `Development`przez `Staging`ramy `Production`obejmują , i . W wartościach nie rozróżnia się wielkość liter. Domyślnie *środowisko* jest odczytywane ze zmiennej środowiskowej. `ASPNETCORE_ENVIRONMENT` Podczas korzystania z [programu Visual Studio](https://visualstudio.microsoft.com)zmienne środowiskowe mogą być ustawiane w pliku *launchSettings.json.* Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a>Hosting zestawów startowych

Ustawia zestawy uruchamiania hostingu aplikacji.

**Klucz:** hostingStartupAssemblies  
**Typ**: *ciąg znaków*  
**Domyślnie**: Pusty ciąg  
**Zestaw za pomocą**:`UseSetting`  
**Zmienna środowiskowa:**`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`

Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do załadowania podczas uruchamiania.

Mimo że wartość konfiguracji domyślnie pusty ciąg, zestawy uruchamiania hostingu zawsze zawierają zestaw aplikacji. Po udostępnieniu zestawów uruchamiania hostingu są one dodawane do zestawu aplikacji do ładowania, gdy aplikacja tworzy swoje typowe usługi podczas uruchamiania.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a>HTTPS Port

Ustaw port przekierowania HTTPS. Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).

**Klucz:** https_port **Typ:** *ciąg*
**Domyślny:** Wartość domyślna nie jest ustawiona.
**Zestaw**za `UseSetting` 
pomocą : **Zmienna środowiskowa**:`ASPNETCORE_HTTPS_PORT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a>Zestawy wykluczeń uruchamiania hostingu

Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do wykluczenia podczas uruchamiania.

**Klucz:** hostingStartupExcludeAssemblies  
**Typ**: *ciąg znaków*  
**Domyślnie**: Pusty ciąg  
**Zestaw za pomocą**:`UseSetting`  
**Zmienna środowiskowa:**`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a>Preferuj hostowanie adresów URL

Wskazuje, czy host powinien nasłuchiwania na `WebHostBuilder` adresach URL skonfigurowanych `IServer` z zamiast tych skonfigurowanych z implementacją.

**Klucz**: preferHostingUrls  
**Typ**: *bool* (`true` lub `1`)  
**Domyślnie**: prawda  
**Zestaw za pomocą**:`PreferHostingUrls`  
**Zmienna środowiskowa:**`ASPNETCORE_PREFERHOSTINGURLS`

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a>Zapobieganie uruchamianiu hostingu

Zapobiega automatycznemu obciążaniu zestawów uruchamiania hostingu, w tym zestawów uruchamiania hostingu skonfigurowanych przez zestaw aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.

**Klucz:** preventHostingStartup  
**Typ**: *bool* (`true` lub `1`)  
**Domyślnie**: false  
**Zestaw za pomocą**:`UseSetting`  
**Zmienna środowiskowa:**`ASPNETCORE_PREVENTHOSTINGSTARTUP`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a>Adresy URL serwera

Wskazuje adresy IP lub adresy hostów z portami i protokołami, na których serwer powinien nasłuchiwania żądań.

**Klucz**: adresy URL  
**Typ**: *ciąg znaków*  
**Domyślnie**:http://localhost:5000  
**Zestaw za pomocą**:`UseUrls`  
**Zmienna środowiskowa:**`ASPNETCORE_URLS`

Zestaw do rozdzielonego średnikiem (;) lista prefiksów adresów URL, na które serwer powinien odpowiedzieć. Na przykład `http://localhost:123`. Użyj\*" ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwach hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000`). Protokół (`http://` `https://`lub ) musi być dołączony do każdego adresu URL. Obsługiwane formaty różnią się w zależności od serwera.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

Pustułka ma własny interfejs API konfiguracji punktu końcowego. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="shutdown-timeout"></a>Limit czasu zamknięcia

Określa czas oczekiwania na zamknięcie hosta sieci Web.

**Klucz:** shutdownTimeoutSeconds  
**Typ**: *int*  
**Domyślnie**: 5  
**Zestaw za pomocą**:`UseShutdownTimeout`  
**Zmienna środowiskowa:**`ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`

Chociaż klucz akceptuje *int* `UseSetting` z (na `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`przykład), [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) metoda rozszerzenia trwa [TimeSpan](/dotnet/api/system.timespan).

W okresie limitu czasu hosting:

* Wyzwala [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).
* Próbuje zatrzymać hostowane usługi, rejestrując wszelkie błędy dla usług, które nie mogą się zatrzymać.

Jeśli limit czasu wygaśnie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji. Usługi zatrzymują się nawet wtedy, gdy nie zakończyły przetwarzania. Jeśli usługi wymagają dodatkowego czasu, aby zatrzymać, zwiększyć limit czasu.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a>Zestaw startowy

Określa zestaw do wyszukiwania `Startup` klasy.

**Klucz:** startupAssembly  
**Typ**: *ciąg znaków*  
**Domyślnie**: Zespół aplikacji  
**Zestaw za pomocą**:`UseStartup`  
**Zmienna środowiskowa:**`ASPNETCORE_STARTUPASSEMBLY`

Można odwoływać`string`się do`TStartup`złożenia według nazwy ( ) lub typu ( ) . Jeśli `UseStartup` wywoływanych jest wiele metod, pierwszeństwo ma ostatnia.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a>Katalog główny sieci Web

Ustawia ścieżkę względną do zasobów statycznych aplikacji.

**Klucz**: webroot  
**Typ**: *ciąg znaków*  
**Domyślnie**: `wwwroot`Wartość domyślna to . Musi istnieć ścieżka do *{content root}/wwwroot.* Jeśli ścieżka nie istnieje, używany jest dostawca plików bez operacji.  
**Zestaw za pomocą**:`UseWebRoot`  
**Zmienna środowiskowa:**`ASPNETCORE_WEBROOT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

Aby uzyskać więcej informacji, zobacz:

* [Podstawy: Korzeń sieci Web](xref:fundamentals/index#web-root)
* [Katalog główny zawartości](#content-root)

## <a name="override-configuration"></a>Zastępowanie konfiguracji

Konfigurowanie hosta sieci Web za pomocą [funkcji Konfiguracji.](xref:fundamentals/configuration/index) W poniższym przykładzie konfiguracja hosta jest opcjonalnie określona w pliku *hostsettings.json.* Każda konfiguracja załadowana z pliku *hostsettings.json* może zostać zastąpiona argumentami wiersza polecenia. Wbudowana konfiguracja `config`(w) służy do konfigurowania hosta za pomocą [funkcji UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration). `IWebHostBuilder`konfiguracja jest dodawana do konfiguracji aplikacji, ale&mdash; `ConfigureAppConfiguration` odwrotność nie `IWebHostBuilder` jest prawdziwa, nie wpływa na konfigurację.

Zastępowanie konfiguracji dostarczanej `UseUrls` przez *hostettings.json* config first, command-line argument config second:

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

*hostsettings.json*:

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) kopiuje tylko `IConfiguration` klucze z dostarczonej do konfiguracji konstruktora hosta. W związku `reloadOnChange: true` z tym ustawienie plików ustawień JSON, INI i XML nie ma wpływu.

Aby określić hosta uruchamianego na określonym adresie URL, żądana wartość może być przekazywana z wiersza polecenia podczas wykonywania [dotnet run](/dotnet/core/tools/dotnet-run). Argument wiersza polecenia zastępuje `urls` wartość z pliku *hostsettings.json,* a serwer nasłuchuje na porcie 8080:

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a>Zarządzanie hostem

**Run**

Metoda `Run` uruchamia aplikację sieci web i blokuje wątek wywołujący, dopóki host nie zostanie zamknięty:

```csharp
host.Run();
```

**Początek**

Uruchom hosta w sposób nieblokujący, wywołując jego `Start` metodę:

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

Jeśli lista adresów URL jest `Start` przekazywana do metody, nasłuchuje na określonych adresach URL:

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

Aplikacja może zainicjować i uruchomić nowego hosta przy użyciu `CreateDefaultBuilder` wstępnie skonfigurowanych ustawień domyślnych przy użyciu statycznej metody wygody. Metody te uruchamiają serwer bez wyjścia konsoli i z [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) czekać na przerwę (Ctrl-C/SIGINT lub SIGTERM):

**Start(Aplikacja RequestDelegate)**

Zacznij `RequestDelegate`od:

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Złożyć wniosek w `http://localhost:5000` przeglądarce, aby otrzymać odpowiedź "Hello World!" `WaitForShutdown`do momentu wydania przerwy (Ctrl-C/SIGINT lub SIGTERM). Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na wyjście naciśnięcia klawisza.

**Start(adres URL ciągu, aplikacja RequestDelegate)**

Zacznij od adresu `RequestDelegate`URL i:

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Daje taki sam wynik jak **Start(RequestDelegate app),** z `http://localhost:8080`wyjątkiem aplikacji odpowiada na .

**Start(Akcja\<IRouteBuilder> routeBuilder)**

Użyj wystąpienia `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) do używania oprogramowania pośredniczącego routingu:

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

Użyj następujących żądań przeglądarki w przykładzie:

| Żądanie                                    | Odpowiedź                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | Cześć, Martin!                           |
| `http://localhost:5000/buenosdias/Catrina` | Buenos dias, Catrina!                    |
| `http://localhost:5000/throw/ooops!`       | Zgłasza wyjątek z ciągiem "ooops!" |
| `http://localhost:5000/throw`              | Zgłasza wyjątek z napisem "Uh oh!" |
| `http://localhost:5000/Sante/Kevin`        | Sante, Kevin!                            |
| `http://localhost:5000`                    | Hello World!                             |

`WaitForShutdown`do momentu wydania przerwy (Ctrl-C/SIGINT lub SIGTERM). Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na wyjście naciśnięcia klawisza.

**Start(adres URL\<ciągu, Action IRouteBuilder> routeBuilder)**

Użyj adresu URL i `IRouteBuilder`wystąpienia:

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

Daje taki sam wynik jak **Start(Action\<IRouteBuilder> routeBuilder),** z `http://localhost:8080`wyjątkiem aplikacji odpowiada na .

**StartWith(Aplikacja\<Action IApplicationBuilder>)**

Podaj pełnomocnika, `IApplicationBuilder`aby skonfigurować:

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

Złożyć wniosek w `http://localhost:5000` przeglądarce, aby otrzymać odpowiedź "Hello World!" `WaitForShutdown`do momentu wydania przerwy (Ctrl-C/SIGINT lub SIGTERM). Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na wyjście naciśnięcia klawisza.

**StartWith(adres URL\<ciągu, Action IApplicationBuilder> aplikacji)**

Podaj adres URL i `IApplicationBuilder`pełnomocnika, aby skonfigurować :

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

Daje taki sam wynik jak **StartWith(Action\<IApplicationBuilder> app)**, `http://localhost:8080`z wyjątkiem aplikacji odpowiada na .

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a>Interfejs IWebHostEnvironment

Interfejs `IWebHostEnvironment` zawiera informacje o środowisku hostingu sieci Web aplikacji. Użyj [iniekcji konstruktora,](xref:fundamentals/dependency-injection) `IWebHostEnvironment` aby uzyskać w celu wykorzystania jego właściwości i metody rozszerzenia:

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

[Podejście oparte na konwencji](xref:fundamentals/environments#environment-based-startup-class-and-methods) może służyć do konfigurowania aplikacji podczas uruchamiania na podstawie środowiska. Alternatywnie, wstrzyknąć `IWebHostEnvironment` do konstruktora `Startup` do użytku w: `ConfigureServices`

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
> Oprócz metody `IsDevelopment` rozszerzenia, `IWebHostEnvironment` oferuje `IsStaging` `IsProduction`, `IsEnvironment(string environmentName)` i metody. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

Usługa `IWebHostEnvironment` może być również wstrzyknięta bezpośrednio do `Configure` metody konfigurowania rurociągu przetwarzania:

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

`IWebHostEnvironment`można wstrzyknąć do `Invoke` metody podczas tworzenia niestandardowego [oprogramowania pośredniczącego:](xref:fundamentals/middleware/write)

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

## <a name="ihostingenvironment-interface"></a>Interfejs IHostingEnvironment

[Interfejs IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) zawiera informacje o środowisku hostingu sieci Web aplikacji. Użyj [iniekcji konstruktora,](xref:fundamentals/dependency-injection) `IHostingEnvironment` aby uzyskać w celu wykorzystania jego właściwości i metody rozszerzenia:

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

[Podejście oparte na konwencji](xref:fundamentals/environments#environment-based-startup-class-and-methods) może służyć do konfigurowania aplikacji podczas uruchamiania na podstawie środowiska. Alternatywnie, wstrzyknąć `IHostingEnvironment` do konstruktora `Startup` do użytku w: `ConfigureServices`

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
> Oprócz metody `IsDevelopment` rozszerzenia, `IHostingEnvironment` oferuje `IsStaging` `IsProduction`, `IsEnvironment(string environmentName)` i metody. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.

Usługa `IHostingEnvironment` może być również wstrzyknięta bezpośrednio do `Configure` metody konfigurowania rurociągu przetwarzania:

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

`IHostingEnvironment`można wstrzyknąć do `Invoke` metody podczas tworzenia niestandardowego [oprogramowania pośredniczącego:](xref:fundamentals/middleware/write)

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

## <a name="ihostapplicationlifetime-interface"></a>Interfejs IHostApplicationLifetime

`IHostApplicationLifetime`umożliwia działania po starcie i zamknięciu systemu. Trzy właściwości interfejsu są tokeny anulowania `Action` używane do rejestrowania metod, które definiują zdarzenia uruchamiania i zamykania.

| Token anulowania    | Wyzwalane podczas&#8230; |
| --------------------- | --------------------- |
| `ApplicationStarted`  | Gospodarz w pełni się rozpoczął. |
| `ApplicationStopped`  | Host kończy wdzięczne zamknięcie. Wszystkie wnioski powinny być przetwarzane. Zamykanie bloków, dopóki to zdarzenie nie zostanie zakończone. |
| `ApplicationStopping` | Host wykonuje wdzięku zamknięcia. Żądania mogą nadal być przetwarzane. Zamykanie bloków, dopóki to zdarzenie nie zostanie zakończone. |

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

`StopApplication`żąda rozwiązania aplikacji. Następująca klasa `StopApplication` używa do bezpiecznie zamknąć aplikację, `Shutdown` gdy wywoływana jest metoda klasy:

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

## <a name="iapplicationlifetime-interface"></a>Interfejs IApplicationLifetime

[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) pozwala na działania po starcie i zamknięciu. Trzy właściwości interfejsu są tokeny anulowania `Action` używane do rejestrowania metod, które definiują zdarzenia uruchamiania i zamykania.

| Token anulowania    | Wyzwalane podczas&#8230; |
| --------------------- | --------------------- |
| [Aplikacja Rozpoczęta](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | Gospodarz w pełni się rozpoczął. |
| [Aplikacje Zwieńczone](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | Host kończy wdzięczne zamknięcie. Wszystkie wnioski powinny być przetwarzane. Zamykanie bloków, dopóki to zdarzenie nie zostanie zakończone. |
| [Stopowanie aplikacji](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | Host wykonuje wdzięku zamknięcia. Żądania mogą nadal być przetwarzane. Zamykanie bloków, dopóki to zdarzenie nie zostanie zakończone. |

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

[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) żąda zakończenia aplikacji. Następująca klasa `StopApplication` używa do bezpiecznie zamknąć aplikację, `Shutdown` gdy wywoływana jest metoda klasy:

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

## <a name="scope-validation"></a>Sprawdzanie poprawności zakresu

[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) ustawia [ServiceProviderOptions.ValidateScopes,](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) `true` jeśli środowisko aplikacji jest rozwój.

Gdy `ValidateScopes` jest `true`ustawiona na , domyślny dostawca usług wykonuje kontrole, aby sprawdzić, czy:

* Usługi o określonym zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez głównego dostawcę usług.
* Usługi o określonym zakresie nie są bezpośrednio lub pośrednio wstrzykuje się do singletons.

Główny dostawca usług jest tworzony, gdy wywoływany jest [program BuildServiceProvider.](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) Okres istnienia głównego dostawcy usług odpowiada okresowi istnienia aplikacji/serwera, gdy dostawca uruchamia się z aplikacją i jest usuwany po zamknięciu aplikacji.

Usługi o określonym zakresie są usuwane przez kontener, który je utworzył. Jeśli usługa o określonym zakresie jest tworzona w kontenerze głównym, okres istnienia usługi jest skutecznie promowany do singleton, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest zamknięty. Sprawdzanie poprawności zakresów usługi łapie te sytuacje, gdy `BuildServiceProvider` jest wywoływana.

Aby zawsze sprawdzać poprawność zakresów, w tym w środowisku produkcyjnym, należy skonfigurować [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) z [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) w konstruktorze hosta:

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
