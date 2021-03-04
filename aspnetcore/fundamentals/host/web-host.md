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
# <a name="aspnet-core-web-host"></a><span data-ttu-id="77857-103">ASP.NET Core hosta sieci Web</span><span class="sxs-lookup"><span data-stu-id="77857-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="77857-104">ASP.NET Core aplikacje konfigurują i uruchamiają *hosta*.</span><span class="sxs-lookup"><span data-stu-id="77857-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="77857-105">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="77857-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="77857-106">Host konfiguruje co najmniej serwer i potok przetwarzania żądań.</span><span class="sxs-lookup"><span data-stu-id="77857-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="77857-107">Na hoście można także skonfigurować rejestrowanie, iniekcję zależności i konfigurację.</span><span class="sxs-lookup"><span data-stu-id="77857-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="77857-108">W tym artykule omówiono hosta sieci Web, który jest dostępny tylko w celu zapewnienia zgodności z poprzednimi wersjami.</span><span class="sxs-lookup"><span data-stu-id="77857-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="77857-109">Szablony ASP.NET Core tworzą [hosta ogólnego platformy .NET](<xref:fundamentals/host/generic-host>), który jest zalecany dla wszystkich typów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-109">The ASP.NET Core templates create a [.NET Generic Host](<xref:fundamentals/host/generic-host>), which is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="77857-110">W tym artykule omówiono hosta sieci Web, który służy do hostowania aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="77857-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="77857-111">W przypadku innych rodzajów aplikacji należy użyć [hosta ogólnego](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="77857-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="77857-112">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="77857-112">Set up a host</span></span>

<span data-ttu-id="77857-113">Utwórz hosta przy użyciu wystąpienia [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="77857-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="77857-114">Jest to zwykle wykonywane w punkcie wejścia aplikacji, w `Main` metodzie.</span><span class="sxs-lookup"><span data-stu-id="77857-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="77857-115">W szablonach projektu `Main` znajduje się w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="77857-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="77857-116">Typowe wywołania aplikacji [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) do rozpoczęcia konfigurowania hosta:</span><span class="sxs-lookup"><span data-stu-id="77857-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

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

<span data-ttu-id="77857-117">Kod, który wywołuje `CreateDefaultBuilder` , znajduje się w metodzie o nazwie `CreateWebHostBuilder` , która oddziela ją od kodu w ramach `Main` tego wywołania `Run` w obiekcie konstruktora.</span><span class="sxs-lookup"><span data-stu-id="77857-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="77857-118">Ta separacja jest wymagana, jeśli używasz [narzędzi Entity Framework Core](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="77857-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="77857-119">Narzędzia oczekują na znalezienie `CreateWebHostBuilder` metody, którą mogą wywołać w czasie projektowania, aby skonfigurować hosta bez uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="77857-120">Alternatywą jest zaimplementowanie `IDesignTimeDbContextFactory` .</span><span class="sxs-lookup"><span data-stu-id="77857-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="77857-121">Aby uzyskać więcej informacji, zobacz [Tworzenie DbContext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="77857-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="77857-122">Metoda `CreateDefaultBuilder` wykonuje następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="77857-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

::: moniker range=">= aspnetcore-5.0"
* <span data-ttu-id="77857-123">Konfiguruje serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci Web przy użyciu dostawców konfiguracji hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="77857-124">Aby poznać domyślne opcje serwera Kestrel, zobacz <xref:fundamentals/servers/kestrel/options> .</span><span class="sxs-lookup"><span data-stu-id="77857-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel/options>.</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0"
* <span data-ttu-id="77857-125">Konfiguruje serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci Web przy użyciu dostawców konfiguracji hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-125">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="77857-126">Aby poznać domyślne opcje serwera Kestrel, zobacz <xref:fundamentals/servers/kestrel#kestrel-options> .</span><span class="sxs-lookup"><span data-stu-id="77857-126">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
::: moniker-end
* <span data-ttu-id="77857-127">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) dla ścieżki zwróconej przez [katalog. GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="77857-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="77857-128">Ładuje [konfigurację hosta](#host-configuration-values) z:</span><span class="sxs-lookup"><span data-stu-id="77857-128">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="77857-129">Zmienne środowiskowe poprzedzone prefiksem `ASPNETCORE_` (na przykład `ASPNETCORE_ENVIRONMENT` ).</span><span class="sxs-lookup"><span data-stu-id="77857-129">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="77857-130">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="77857-130">Command-line arguments.</span></span>
* <span data-ttu-id="77857-131">Ładuje konfigurację aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="77857-131">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="77857-132">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="77857-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="77857-133">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="77857-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="77857-134">[Klucze tajne użytkownika](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku przy użyciu zestawu wpisów.</span><span class="sxs-lookup"><span data-stu-id="77857-134">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="77857-135">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="77857-135">Environment variables.</span></span>
  * <span data-ttu-id="77857-136">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="77857-136">Command-line arguments.</span></span>
* <span data-ttu-id="77857-137">Konfiguruje [Rejestrowanie](xref:fundamentals/logging/index) dla konsoli i danych wyjściowych debugowania.</span><span class="sxs-lookup"><span data-stu-id="77857-137">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="77857-138">Rejestrowanie obejmuje reguły [filtrowania dzienników](xref:fundamentals/logging/index#log-filtering) określone w sekcji konfiguracyjnej rejestrowania *appsettings.json* lub *appSettings. { Environment} plik JSON* .</span><span class="sxs-lookup"><span data-stu-id="77857-138">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="77857-139">Po uruchomieniu usług IIS za pomocą [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module)program `CreateDefaultBuilder` włącza [integrację usług IIS](xref:host-and-deploy/iis/index), która konfiguruje podstawowy adres i port aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-139">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="77857-140">Integracja usług IIS konfiguruje również aplikację do [przechwytywania błędów uruchamiania](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="77857-140">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="77857-141">Aby poznać domyślne opcje usług IIS, zobacz <xref:host-and-deploy/iis/index#iis-options> .</span><span class="sxs-lookup"><span data-stu-id="77857-141">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="77857-142">Ustawia [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true` , jeśli środowisko aplikacji jest opracowywane.</span><span class="sxs-lookup"><span data-stu-id="77857-142">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="77857-143">Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="77857-143">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="77857-144">Konfiguracja zdefiniowana przez `CreateDefaultBuilder` można zastąpić i rozszerzyć przez [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)i inne metody i metody rozszerzające [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="77857-144">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="77857-145">Poniżej przedstawiono kilka przykładów:</span><span class="sxs-lookup"><span data-stu-id="77857-145">A few examples follow:</span></span>

* <span data-ttu-id="77857-146">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) służy do określania dodatkowych `IConfiguration` aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-146">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="77857-147">Następujące `ConfigureAppConfiguration` wywołanie dodaje delegata w celu uwzględnienia konfiguracji aplikacji w pliku *appsettings.xml* .</span><span class="sxs-lookup"><span data-stu-id="77857-147">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="77857-148">`ConfigureAppConfiguration` może być wywoływana wiele razy.</span><span class="sxs-lookup"><span data-stu-id="77857-148">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="77857-149">Należy zauważyć, że ta konfiguracja nie ma zastosowania do hosta (na przykład adresów URL serwera lub środowiska).</span><span class="sxs-lookup"><span data-stu-id="77857-149">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="77857-150">Zapoznaj się z sekcją [wartości konfiguracji hosta](#host-configuration-values) .</span><span class="sxs-lookup"><span data-stu-id="77857-150">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="77857-151">Następujące `ConfigureLogging` wywołanie dodaje delegata w celu skonfigurowania minimalnego poziomu rejestrowania ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) na wartość [LogLevel. Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="77857-151">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="77857-152">To ustawienie zastępuje ustawienia w *appsettings.Development.json* ( `LogLevel.Debug` ) i *appsettings.Production.json* ( `LogLevel.Error` ) skonfigurowany przez `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="77857-152">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="77857-153">`ConfigureLogging` może być wywoływana wiele razy.</span><span class="sxs-lookup"><span data-stu-id="77857-153">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="77857-154">Następujące wywołanie `ConfigureKestrel` przesłania domyślne [limity. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30 000 000 bajtów ustanowionych podczas konfigurowania Kestrel przez `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="77857-154">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="77857-155">Następujące wywołanie [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) zastępuje domyślne [limity. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30 000 000 bajtów ustanowionych podczas konfigurowania Kestrel przez `CreateDefaultBuilder` :</span><span class="sxs-lookup"><span data-stu-id="77857-155">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="77857-156">[Katalog główny zawartości](xref:fundamentals/index#content-root) określa, gdzie host wyszukuje pliki zawartości, takie jak pliki widoku MVC.</span><span class="sxs-lookup"><span data-stu-id="77857-156">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="77857-157">Gdy aplikacja jest uruchamiana z folderu głównego projektu, folder główny projektu jest używany jako katalog główny zawartości.</span><span class="sxs-lookup"><span data-stu-id="77857-157">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="77857-158">Jest to ustawienie domyślne używane w programie [Visual Studio](https://visualstudio.microsoft.com) i w [nowych szablonach dotnet](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="77857-158">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="77857-159">Aby uzyskać więcej informacji na temat konfiguracji aplikacji, zobacz <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="77857-159">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="77857-160">Alternatywą dla użycia metody statycznej `CreateDefaultBuilder` jest utworzenie hosta z [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) jest obsługiwane podejście ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="77857-160">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="77857-161">Podczas konfigurowania hosta można dostarczyć metody [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) i [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) .</span><span class="sxs-lookup"><span data-stu-id="77857-161">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="77857-162">Jeśli `Startup` Klasa jest określona, musi definiować `Configure` metodę.</span><span class="sxs-lookup"><span data-stu-id="77857-162">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="77857-163">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="77857-163">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="77857-164">Wiele wywołań do `ConfigureServices` dołączenia do siebie nawzajem.</span><span class="sxs-lookup"><span data-stu-id="77857-164">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="77857-165">Wiele wywołań `Configure` lub `UseStartup` `WebHostBuilder` Zastąp poprzednie ustawienia.</span><span class="sxs-lookup"><span data-stu-id="77857-165">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="77857-166">Wartości konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="77857-166">Host configuration values</span></span>

<span data-ttu-id="77857-167">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) opiera się na następujących podejściach do ustawiania wartości konfiguracji hosta:</span><span class="sxs-lookup"><span data-stu-id="77857-167">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="77857-168">Konfiguracja konstruktora hostów, która zawiera zmienne środowiskowe w formacie `ASPNETCORE_{configurationKey}` .</span><span class="sxs-lookup"><span data-stu-id="77857-168">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="77857-169">Na przykład `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="77857-169">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="77857-170">Rozszerzenia, takie jak [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) i [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (zobacz sekcję [Konfiguracja zastąpień](#override-configuration) ).</span><span class="sxs-lookup"><span data-stu-id="77857-170">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="77857-171">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) i skojarzony klucz.</span><span class="sxs-lookup"><span data-stu-id="77857-171">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="77857-172">Podczas ustawiania wartości za pomocą `UseSetting` , wartość jest ustawiana jako ciąg, niezależnie od typu.</span><span class="sxs-lookup"><span data-stu-id="77857-172">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="77857-173">Host używa dowolnej opcji ustawia wartość Last.</span><span class="sxs-lookup"><span data-stu-id="77857-173">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="77857-174">Aby uzyskać więcej informacji, zobacz [Przesłoń konfigurację](#override-configuration) w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="77857-174">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="77857-175">Klucz aplikacji (nazwa)</span><span class="sxs-lookup"><span data-stu-id="77857-175">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="77857-176">`IWebHostEnvironment.ApplicationName`Właściwość jest ustawiana automatycznie, gdy [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) lub [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) jest wywoływana podczas konstruowania hosta.</span><span class="sxs-lookup"><span data-stu-id="77857-176">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="77857-177">Wartość jest ustawiana na nazwę zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-177">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="77857-178">Aby jawnie ustawić wartość, użyj [WebHostDefaults. ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="77857-178">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="77857-179">Właściwość [IHostingEnvironment. ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) jest ustawiana automatycznie, gdy [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) lub [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) jest wywoływana podczas konstruowania hosta.</span><span class="sxs-lookup"><span data-stu-id="77857-179">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="77857-180">Wartość jest ustawiana na nazwę zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-180">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="77857-181">Aby jawnie ustawić wartość, użyj [WebHostDefaults. ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="77857-181">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="77857-182">**Klucz**: ApplicationName</span><span class="sxs-lookup"><span data-stu-id="77857-182">**Key**: applicationName</span></span>  
<span data-ttu-id="77857-183">**Typ**: *ciąg*</span><span class="sxs-lookup"><span data-stu-id="77857-183">**Type**: *string*</span></span>  
<span data-ttu-id="77857-184">**Wartość domyślna**: Nazwa zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-184">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="77857-185">**Ustaw przy użyciu**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="77857-185">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="77857-186">**Zmienna środowiskowa**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="77857-186">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="77857-187">Błędy uruchamiania przechwytywania</span><span class="sxs-lookup"><span data-stu-id="77857-187">Capture Startup Errors</span></span>

<span data-ttu-id="77857-188">To ustawienie steruje przechwytywaniem błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="77857-188">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="77857-189">**Klucz**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="77857-189">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="77857-190">**Typ**: *bool* ( `true` or `1` )</span><span class="sxs-lookup"><span data-stu-id="77857-190">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="77857-191">**Domyślnie**: wartość domyślna to, `false` chyba że aplikacja działa z Kestrel za usługami IIS, gdzie wartość domyślna to `true` .</span><span class="sxs-lookup"><span data-stu-id="77857-191">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="77857-192">**Ustaw przy użyciu**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="77857-192">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="77857-193">**Zmienna środowiskowa**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="77857-193">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="77857-194">Gdy `false` , błędy podczas uruchamiania, kończy się hostem.</span><span class="sxs-lookup"><span data-stu-id="77857-194">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="77857-195">Gdy `true` host przechwytuje wyjątki podczas uruchamiania, a następnie próbuje uruchomić serwer.</span><span class="sxs-lookup"><span data-stu-id="77857-195">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="77857-196">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="77857-196">Content root</span></span>

<span data-ttu-id="77857-197">To ustawienie określa, gdzie ASP.NET Core rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="77857-197">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="77857-198">**Klucz**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="77857-198">**Key**: contentRoot</span></span>  
<span data-ttu-id="77857-199">**Typ**: *ciąg*</span><span class="sxs-lookup"><span data-stu-id="77857-199">**Type**: *string*</span></span>  
<span data-ttu-id="77857-200">**Domyślnie**: Domyślnie folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-200">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="77857-201">**Ustaw przy użyciu**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="77857-201">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="77857-202">**Zmienna środowiskowa**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="77857-202">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="77857-203">Katalog główny zawartości jest również używany jako ścieżka podstawowa dla [katalogu głównego sieci Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="77857-203">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="77857-204">Jeśli ścieżka katalogu głównego zawartości nie istnieje, uruchomienie hosta nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="77857-204">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="77857-205">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="77857-205">For more information, see:</span></span>

* [<span data-ttu-id="77857-206">Podstawy: zawartość główna</span><span class="sxs-lookup"><span data-stu-id="77857-206">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="77857-207">Katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="77857-207">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="77857-208">Szczegóły błędów</span><span class="sxs-lookup"><span data-stu-id="77857-208">Detailed Errors</span></span>

<span data-ttu-id="77857-209">Określa, czy mają być przechwytywane szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="77857-209">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="77857-210">**Klucz**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="77857-210">**Key**: detailedErrors</span></span>  
<span data-ttu-id="77857-211">**Typ**: *bool* ( `true` or `1` )</span><span class="sxs-lookup"><span data-stu-id="77857-211">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="77857-212">**Wartość domyślna**: FAŁSZ</span><span class="sxs-lookup"><span data-stu-id="77857-212">**Default**: false</span></span>  
<span data-ttu-id="77857-213">**Ustaw przy użyciu**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="77857-213">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="77857-214">**Zmienna środowiskowa**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="77857-214">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="77857-215">Po włączeniu (lub gdy <a href="#environment">środowisko</a> jest ustawione na `Development` ), aplikacja przechwytuje szczegółowe wyjątki.</span><span class="sxs-lookup"><span data-stu-id="77857-215">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="77857-216">Środowisko</span><span class="sxs-lookup"><span data-stu-id="77857-216">Environment</span></span>

<span data-ttu-id="77857-217">Ustawia środowisko aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-217">Sets the app's environment.</span></span>

<span data-ttu-id="77857-218">**Klucz**: środowisko</span><span class="sxs-lookup"><span data-stu-id="77857-218">**Key**: environment</span></span>  
<span data-ttu-id="77857-219">**Typ**: *ciąg*</span><span class="sxs-lookup"><span data-stu-id="77857-219">**Type**: *string*</span></span>  
<span data-ttu-id="77857-220">**Wartość domyślna**: produkcja</span><span class="sxs-lookup"><span data-stu-id="77857-220">**Default**: Production</span></span>  
<span data-ttu-id="77857-221">**Ustaw przy użyciu**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="77857-221">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="77857-222">**Zmienna środowiskowa**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="77857-222">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="77857-223">Dla środowiska można ustawić dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="77857-223">The environment can be set to any value.</span></span> <span data-ttu-id="77857-224">Wartości zdefiniowane przez platformę obejmują `Development` , `Staging` , i `Production` .</span><span class="sxs-lookup"><span data-stu-id="77857-224">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="77857-225">W wartościach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="77857-225">Values aren't case sensitive.</span></span> <span data-ttu-id="77857-226">Domyślnie *środowisko* jest odczytywane ze `ASPNETCORE_ENVIRONMENT` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="77857-226">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="77857-227">W przypadku korzystania z [programu Visual Studio](https://visualstudio.microsoft.com)zmienne środowiskowe można ustawić w *launchSettings.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="77857-227">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="77857-228">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="77857-228">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="77857-229">Hostowanie zestawów startowych</span><span class="sxs-lookup"><span data-stu-id="77857-229">Hosting Startup Assemblies</span></span>

<span data-ttu-id="77857-230">Ustawia zestawy uruchomieniowe obsługujące aplikację.</span><span class="sxs-lookup"><span data-stu-id="77857-230">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="77857-231">**Klucz**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="77857-231">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="77857-232">**Typ**: *ciąg*</span><span class="sxs-lookup"><span data-stu-id="77857-232">**Type**: *string*</span></span>  
<span data-ttu-id="77857-233">**Wartość domyślna**: pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="77857-233">**Default**: Empty string</span></span>  
<span data-ttu-id="77857-234">**Ustaw przy użyciu**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="77857-234">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="77857-235">**Zmienna środowiskowa**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="77857-235">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="77857-236">Rozdzielany średnikami ciąg początkowych zestawów startowych do załadowania podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="77857-236">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="77857-237">Mimo że wartość konfiguracji jest domyślnie pustym ciągiem, zestaw startowy obsługujący zawsze zawiera zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-237">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="77857-238">W przypadku udostępniania zestawów startowych są one dodawane do zestawu aplikacji do załadowania, gdy aplikacja kompiluje swoje popularne usługi podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="77857-238">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="77857-239">Port HTTPS</span><span class="sxs-lookup"><span data-stu-id="77857-239">HTTPS Port</span></span>

<span data-ttu-id="77857-240">Ustaw port przekierowania HTTPS.</span><span class="sxs-lookup"><span data-stu-id="77857-240">Set the HTTPS redirect port.</span></span> <span data-ttu-id="77857-241">Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="77857-241">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="77857-242">**Klucz**: https_port **Typ**: *ciąg* 
 **domyślny**: nie ustawiono wartości domyślnej.</span><span class="sxs-lookup"><span data-stu-id="77857-242">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="77857-243">**Ustaw przy użyciu**: `UseSetting` 
 **zmienna środowiskowa**:`ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="77857-243">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="77857-244">Hosting — wykluczanie zestawów</span><span class="sxs-lookup"><span data-stu-id="77857-244">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="77857-245">Rozdzielany średnikami ciąg początkowych zestawów uruchamiania, który ma zostać wykluczony podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="77857-245">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="77857-246">**Klucz**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="77857-246">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="77857-247">**Typ**: *ciąg*</span><span class="sxs-lookup"><span data-stu-id="77857-247">**Type**: *string*</span></span>  
<span data-ttu-id="77857-248">**Wartość domyślna**: pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="77857-248">**Default**: Empty string</span></span>  
<span data-ttu-id="77857-249">**Ustaw przy użyciu**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="77857-249">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="77857-250">**Zmienna środowiskowa**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="77857-250">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="77857-251">Preferuj adresy URL hostingu</span><span class="sxs-lookup"><span data-stu-id="77857-251">Prefer Hosting URLs</span></span>

<span data-ttu-id="77857-252">Wskazuje, czy host powinien nasłuchiwać adresów URL skonfigurowanych przy użyciu `WebHostBuilder` zamiast tych skonfigurowanych przy użyciu `IServer` implementacji.</span><span class="sxs-lookup"><span data-stu-id="77857-252">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="77857-253">**Klucz**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="77857-253">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="77857-254">**Typ**: *bool* ( `true` or `1` )</span><span class="sxs-lookup"><span data-stu-id="77857-254">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="77857-255">Wartość **Domyślna**: prawda</span><span class="sxs-lookup"><span data-stu-id="77857-255">**Default**: true</span></span>  
<span data-ttu-id="77857-256">**Ustaw przy użyciu**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="77857-256">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="77857-257">**Zmienna środowiskowa**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="77857-257">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="77857-258">Zapobiegaj uruchamianiu hostingu</span><span class="sxs-lookup"><span data-stu-id="77857-258">Prevent Hosting Startup</span></span>

<span data-ttu-id="77857-259">Zapobiega automatycznemu ładowaniu zestawów startowych hostingu, w tym hostingu zestawów startowych skonfigurowanych przez zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-259">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="77857-260">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="77857-260">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="77857-261">**Klucz**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="77857-261">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="77857-262">**Typ**: *bool* ( `true` or `1` )</span><span class="sxs-lookup"><span data-stu-id="77857-262">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="77857-263">**Wartość domyślna**: FAŁSZ</span><span class="sxs-lookup"><span data-stu-id="77857-263">**Default**: false</span></span>  
<span data-ttu-id="77857-264">**Ustaw przy użyciu**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="77857-264">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="77857-265">**Zmienna środowiskowa**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="77857-265">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="77857-266">Adresy URL serwera</span><span class="sxs-lookup"><span data-stu-id="77857-266">Server URLs</span></span>

<span data-ttu-id="77857-267">Wskazuje adresy IP lub adresy hosta z portami i protokołami, na których serwer powinien nasłuchiwać żądań.</span><span class="sxs-lookup"><span data-stu-id="77857-267">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="77857-268">**Klucz**: adresy URL</span><span class="sxs-lookup"><span data-stu-id="77857-268">**Key**: urls</span></span>  
<span data-ttu-id="77857-269">**Typ**: *ciąg*</span><span class="sxs-lookup"><span data-stu-id="77857-269">**Type**: *string*</span></span>  
<span data-ttu-id="77857-270">**Wartość domyślna**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="77857-270">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="77857-271">**Ustaw przy użyciu**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="77857-271">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="77857-272">**Zmienna środowiskowa**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="77857-272">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="77857-273">Ustaw na rozdzieloną średnikami (;) Lista prefiksów adresów URL, do których serwer powinien reagować.</span><span class="sxs-lookup"><span data-stu-id="77857-273">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="77857-274">Na przykład `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="77857-274">For example, `http://localhost:123`.</span></span> <span data-ttu-id="77857-275">Użyj " \* ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwie hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000` ).</span><span class="sxs-lookup"><span data-stu-id="77857-275">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="77857-276">Protokół ( `http://` lub `https://` ) musi być dołączony do każdego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="77857-276">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="77857-277">Obsługiwane formaty różnią się między serwerami.</span><span class="sxs-lookup"><span data-stu-id="77857-277">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="77857-278">Kestrel ma własny interfejs API konfiguracji punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="77857-278">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="77857-279">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel/endpoints>.</span><span class="sxs-lookup"><span data-stu-id="77857-279">For more information, see <xref:fundamentals/servers/kestrel/endpoints>.</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0"
<span data-ttu-id="77857-280">Kestrel ma własny interfejs API konfiguracji punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="77857-280">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="77857-281">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="77857-281">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>
::: moniker-end

### <a name="shutdown-timeout"></a><span data-ttu-id="77857-282">Limit czasu zamykania</span><span class="sxs-lookup"><span data-stu-id="77857-282">Shutdown Timeout</span></span>

<span data-ttu-id="77857-283">Określa czas oczekiwania na zamknięcie hosta sieci Web.</span><span class="sxs-lookup"><span data-stu-id="77857-283">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="77857-284">**Klucz**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="77857-284">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="77857-285">**Typ**: *int*</span><span class="sxs-lookup"><span data-stu-id="77857-285">**Type**: *int*</span></span>  
<span data-ttu-id="77857-286">**Wartość domyślna**: 5</span><span class="sxs-lookup"><span data-stu-id="77857-286">**Default**: 5</span></span>  
<span data-ttu-id="77857-287">**Ustaw przy użyciu**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="77857-287">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="77857-288">**Zmienna środowiskowa**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="77857-288">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="77857-289">Chociaż klucz akceptuje *int* z `UseSetting` (na przykład `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")` ), Metoda rozszerzenia [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) przyjmuje wartość [TimeSpan](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="77857-289">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="77857-290">Podczas okresu przekroczenia limitu czasu hostowanie:</span><span class="sxs-lookup"><span data-stu-id="77857-290">During the timeout period, hosting:</span></span>

* <span data-ttu-id="77857-291">Wyzwala [IApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="77857-291">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="77857-292">Próbuje zatrzymać usługi hostowane, rejestrując błędy dla usług, których nie można zatrzymać.</span><span class="sxs-lookup"><span data-stu-id="77857-292">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="77857-293">Jeśli limit czasu upłynie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-293">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="77857-294">Usługi są zatrzymane nawet wtedy, gdy nie zakończyły przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="77857-294">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="77857-295">Jeśli usługi wymagają dodatkowego czasu na zatrzymanie, zwiększ limit czasu.</span><span class="sxs-lookup"><span data-stu-id="77857-295">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="77857-296">Zestaw startowy</span><span class="sxs-lookup"><span data-stu-id="77857-296">Startup Assembly</span></span>

<span data-ttu-id="77857-297">Określa zestaw, który ma być wyszukiwany dla `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="77857-297">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="77857-298">**Klucz**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="77857-298">**Key**: startupAssembly</span></span>  
<span data-ttu-id="77857-299">**Typ**: *ciąg*</span><span class="sxs-lookup"><span data-stu-id="77857-299">**Type**: *string*</span></span>  
<span data-ttu-id="77857-300">**Domyślnie**: zestaw aplikacji</span><span class="sxs-lookup"><span data-stu-id="77857-300">**Default**: The app's assembly</span></span>  
<span data-ttu-id="77857-301">**Ustaw przy użyciu**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="77857-301">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="77857-302">**Zmienna środowiskowa**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="77857-302">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="77857-303">`string`Można odwołać się do zestawu według nazwy () lub typu ( `TStartup` ).</span><span class="sxs-lookup"><span data-stu-id="77857-303">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="77857-304">Jeśli `UseStartup` wywoływana jest wiele metod, pierwszeństwo ma Ostatnia.</span><span class="sxs-lookup"><span data-stu-id="77857-304">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="77857-305">Katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="77857-305">Web root</span></span>

<span data-ttu-id="77857-306">Ustawia ścieżkę względną do statycznych zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-306">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="77857-307">**Klucz**: Webroot</span><span class="sxs-lookup"><span data-stu-id="77857-307">**Key**: webroot</span></span>  
<span data-ttu-id="77857-308">**Typ**: *ciąg*</span><span class="sxs-lookup"><span data-stu-id="77857-308">**Type**: *string*</span></span>  
<span data-ttu-id="77857-309">**Wartość domyślna**: wartość domyślna to `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="77857-309">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="77857-310">Ścieżka do *elementu {content root}/wwwroot* musi istnieć.</span><span class="sxs-lookup"><span data-stu-id="77857-310">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="77857-311">Jeśli ścieżka nie istnieje, jest używany dostawca plików No-op.</span><span class="sxs-lookup"><span data-stu-id="77857-311">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="77857-312">**Ustaw przy użyciu**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="77857-312">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="77857-313">**Zmienna środowiskowa**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="77857-313">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="77857-314">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="77857-314">For more information, see:</span></span>

* [<span data-ttu-id="77857-315">Podstawy: katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="77857-315">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="77857-316">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="77857-316">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="77857-317">Zastąp konfigurację</span><span class="sxs-lookup"><span data-stu-id="77857-317">Override configuration</span></span>

<span data-ttu-id="77857-318">Skonfiguruj hosta sieci Web za pomocą [konfiguracji](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="77857-318">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="77857-319">W poniższym przykładzie Konfiguracja hosta jest opcjonalnie określona w *hostsettings.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="77857-319">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="77857-320">Każda konfiguracja załadowana z *hostsettings.jsw* pliku może zostać przesłonięta przez argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="77857-320">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="77857-321">Utworzona konfiguracja (w programie `config` ) służy do konfigurowania hosta z [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="77857-321">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="77857-322">`IWebHostBuilder`Konfiguracja jest dodawana do konfiguracji aplikacji, ale nie ma ona &mdash; `ConfigureAppConfiguration` wpływu na `IWebHostBuilder` konfigurację.</span><span class="sxs-lookup"><span data-stu-id="77857-322">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="77857-323">Zastępowanie konfiguracji podanej przy `UseUrls` użyciu *hostsettings.jsnajpierw w* konfiguracji, argument wiersza polecenia config sekund:</span><span class="sxs-lookup"><span data-stu-id="77857-323">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

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

<span data-ttu-id="77857-324">*hostsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="77857-324">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="77857-325">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) kopiuje tylko klucze z dostarczonej `IConfiguration` do konfiguracji konstruktora hostów.</span><span class="sxs-lookup"><span data-stu-id="77857-325">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="77857-326">W związku z tym `reloadOnChange: true` nie ma żadnego wpływu na ustawienia plików JSON, ini i XML.</span><span class="sxs-lookup"><span data-stu-id="77857-326">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="77857-327">Aby określić hosta na określonym adresie URL, wymagana wartość może zostać przeniesiona z wiersza polecenia podczas wykonywania [przebiegu dotnet](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="77857-327">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="77857-328">Argument wiersza polecenia zastępuje `urls` wartość z *hostsettings.jsw* pliku, a serwer nasłuchuje na porcie 8080:</span><span class="sxs-lookup"><span data-stu-id="77857-328">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="77857-329">Zarządzanie hostem</span><span class="sxs-lookup"><span data-stu-id="77857-329">Manage the host</span></span>

<span data-ttu-id="77857-330">**Uruchom**</span><span class="sxs-lookup"><span data-stu-id="77857-330">**Run**</span></span>

<span data-ttu-id="77857-331">`Run`Metoda uruchamia aplikację sieci Web i blokuje wątek wywołujący do momentu wyłączenia hosta:</span><span class="sxs-lookup"><span data-stu-id="77857-331">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="77857-332">**Początek**</span><span class="sxs-lookup"><span data-stu-id="77857-332">**Start**</span></span>

<span data-ttu-id="77857-333">Uruchom hosta w sposób nieblokowany przez wywołanie jego `Start` metody:</span><span class="sxs-lookup"><span data-stu-id="77857-333">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="77857-334">Jeśli lista adresów URL jest przenoszona do `Start` metody, nasłuchuje na określonych adresach URL:</span><span class="sxs-lookup"><span data-stu-id="77857-334">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

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

<span data-ttu-id="77857-335">Aplikacja może inicjować i uruchamiać nowy host przy użyciu wstępnie skonfigurowanych ustawień domyślnych `CreateDefaultBuilder` przy użyciu statycznej metody wygodnej.</span><span class="sxs-lookup"><span data-stu-id="77857-335">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="77857-336">Metody te służą do uruchamiania serwera bez danych wyjściowych konsoli i [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) poczekać na przerwanie (Ctrl-C/SIGINT lub SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="77857-336">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="77857-337">**Rozpocznij (aplikacja RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="77857-337">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="77857-338">Zacznij od `RequestDelegate` :</span><span class="sxs-lookup"><span data-stu-id="77857-338">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="77857-339">Wykonaj żądanie w przeglądarce, aby `http://localhost:5000` otrzymać odpowiedź "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="77857-339">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="77857-340">`WaitForShutdown` bloki do momentu wystawienia przerwy (Ctrl-C/SIGINT lub SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="77857-340">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="77857-341">Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na zamknięcie naciśnięcia.</span><span class="sxs-lookup"><span data-stu-id="77857-341">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="77857-342">**Rozpocznij (adres URL ciągu, aplikacja RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="77857-342">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="77857-343">Zacznij od adresu URL i `RequestDelegate` :</span><span class="sxs-lookup"><span data-stu-id="77857-343">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="77857-344">Daje ten sam wynik jako **Start (RequestDelegate App)**, z wyjątkiem tego, że aplikacja reaguje na `http://localhost:8080` .</span><span class="sxs-lookup"><span data-stu-id="77857-344">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="77857-345">**Uruchom (Akcja \<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="77857-345">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="77857-346">Użyj wystąpienia programu `IRouteBuilder` ([Microsoft. AspNetCore. Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)), aby użyć oprogramowania pośredniczącego routingu:</span><span class="sxs-lookup"><span data-stu-id="77857-346">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

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

<span data-ttu-id="77857-347">Użyj poniższego żądania przeglądarki z przykładem:</span><span class="sxs-lookup"><span data-stu-id="77857-347">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="77857-348">Żądanie</span><span class="sxs-lookup"><span data-stu-id="77857-348">Request</span></span>                                    | <span data-ttu-id="77857-349">Reakcja</span><span class="sxs-lookup"><span data-stu-id="77857-349">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="77857-350">Witaj, Martin!</span><span class="sxs-lookup"><span data-stu-id="77857-350">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="77857-351">Buenos Dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="77857-351">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="77857-352">Zgłasza wyjątek z ciągiem "ooops!"</span><span class="sxs-lookup"><span data-stu-id="77857-352">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="77857-353">Zgłasza wyjątek z ciągiem "zapomniano"</span><span class="sxs-lookup"><span data-stu-id="77857-353">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="77857-354">Sante, Jan!</span><span class="sxs-lookup"><span data-stu-id="77857-354">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="77857-355">Hello World!</span><span class="sxs-lookup"><span data-stu-id="77857-355">Hello World!</span></span>                             |

<span data-ttu-id="77857-356">`WaitForShutdown` bloki do momentu wystawienia przerwy (Ctrl-C/SIGINT lub SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="77857-356">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="77857-357">Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na zamknięcie naciśnięcia.</span><span class="sxs-lookup"><span data-stu-id="77857-357">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="77857-358">**Rozpocznij (ciąg URL: Action \<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="77857-358">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="77857-359">Użyj adresu URL i wystąpienia `IRouteBuilder` :</span><span class="sxs-lookup"><span data-stu-id="77857-359">Use a URL and an instance of `IRouteBuilder`:</span></span>

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

<span data-ttu-id="77857-360">Daje ten sam wynik jako **początek (Akcja \<IRouteBuilder> routeBuilder)**, z wyjątkiem tego, że aplikacja reaguje na `http://localhost:8080` .</span><span class="sxs-lookup"><span data-stu-id="77857-360">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="77857-361">**StartWith ( \<IApplicationBuilder> aplikacja działania)**</span><span class="sxs-lookup"><span data-stu-id="77857-361">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="77857-362">Podaj delegata, aby skonfigurować `IApplicationBuilder` :</span><span class="sxs-lookup"><span data-stu-id="77857-362">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="77857-363">Wykonaj żądanie w przeglądarce, aby `http://localhost:5000` otrzymać odpowiedź "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="77857-363">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="77857-364">`WaitForShutdown` bloki do momentu wystawienia przerwy (Ctrl-C/SIGINT lub SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="77857-364">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="77857-365">Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na zamknięcie naciśnięcia.</span><span class="sxs-lookup"><span data-stu-id="77857-365">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="77857-366">**StartWith (adres URL ciągu, \<IApplicationBuilder> aplikacja akcji)**</span><span class="sxs-lookup"><span data-stu-id="77857-366">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="77857-367">Podaj adres URL i delegata, aby skonfigurować `IApplicationBuilder` :</span><span class="sxs-lookup"><span data-stu-id="77857-367">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="77857-368">Daje ten sam wynik jako **StartWith ( \<IApplicationBuilder> aplikacja działania)**, z wyjątkiem tego, że aplikacja reaguje na `http://localhost:8080` .</span><span class="sxs-lookup"><span data-stu-id="77857-368">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="77857-369">IWebHostEnvironment, interfejs</span><span class="sxs-lookup"><span data-stu-id="77857-369">IWebHostEnvironment interface</span></span>

<span data-ttu-id="77857-370">`IWebHostEnvironment`Interfejs zawiera informacje o środowisku hostingu aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="77857-370">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="77857-371">Użyj [iniekcji konstruktorów](xref:fundamentals/dependency-injection) , aby uzyskać `IWebHostEnvironment` w celu użycia jej właściwości i metod rozszerzających:</span><span class="sxs-lookup"><span data-stu-id="77857-371">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="77857-372">[Podejście oparte na Konwencji](xref:fundamentals/environments#environment-based-startup-class-and-methods) może służyć do konfigurowania aplikacji podczas uruchamiania na podstawie środowiska.</span><span class="sxs-lookup"><span data-stu-id="77857-372">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="77857-373">Alternatywnie można wstrzyknąć `IWebHostEnvironment` do `Startup` konstruktora do użycia w `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="77857-373">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="77857-374">Oprócz `IsDevelopment` metody rozszerzenia, `IWebHostEnvironment` oferty `IsStaging` , `IsProduction` i `IsEnvironment(string environmentName)` metody.</span><span class="sxs-lookup"><span data-stu-id="77857-374">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="77857-375">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="77857-375">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="77857-376">`IWebHostEnvironment`Usługę można również wstrzyknąć bezpośrednio do `Configure` metody w celu skonfigurowania potoku przetwarzania:</span><span class="sxs-lookup"><span data-stu-id="77857-376">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="77857-377">`IWebHostEnvironment` można wstrzyknąć do `Invoke` metody podczas tworzenia niestandardowego [oprogramowania pośredniczącego](xref:fundamentals/middleware/write):</span><span class="sxs-lookup"><span data-stu-id="77857-377">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="77857-378">IHostingEnvironment, interfejs</span><span class="sxs-lookup"><span data-stu-id="77857-378">IHostingEnvironment interface</span></span>

<span data-ttu-id="77857-379">[Interfejs IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) zawiera informacje o środowisku hostingu aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="77857-379">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="77857-380">Użyj [iniekcji konstruktorów](xref:fundamentals/dependency-injection) , aby uzyskać `IHostingEnvironment` w celu użycia jej właściwości i metod rozszerzających:</span><span class="sxs-lookup"><span data-stu-id="77857-380">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="77857-381">[Podejście oparte na Konwencji](xref:fundamentals/environments#environment-based-startup-class-and-methods) może służyć do konfigurowania aplikacji podczas uruchamiania na podstawie środowiska.</span><span class="sxs-lookup"><span data-stu-id="77857-381">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="77857-382">Alternatywnie można wstrzyknąć `IHostingEnvironment` do `Startup` konstruktora do użycia w `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="77857-382">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="77857-383">Oprócz `IsDevelopment` metody rozszerzenia, `IHostingEnvironment` oferty `IsStaging` , `IsProduction` i `IsEnvironment(string environmentName)` metody.</span><span class="sxs-lookup"><span data-stu-id="77857-383">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="77857-384">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="77857-384">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="77857-385">`IHostingEnvironment`Usługę można również wstrzyknąć bezpośrednio do `Configure` metody w celu skonfigurowania potoku przetwarzania:</span><span class="sxs-lookup"><span data-stu-id="77857-385">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="77857-386">`IHostingEnvironment` można wstrzyknąć do `Invoke` metody podczas tworzenia niestandardowego [oprogramowania pośredniczącego](xref:fundamentals/middleware/write):</span><span class="sxs-lookup"><span data-stu-id="77857-386">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="77857-387">IHostApplicationLifetime, interfejs</span><span class="sxs-lookup"><span data-stu-id="77857-387">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="77857-388">`IHostApplicationLifetime` zezwala na działania po uruchomieniu i zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="77857-388">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="77857-389">Trzy właściwości interfejsu są tokenami anulowania używanymi do rejestrowania `Action` metod, które definiują zdarzenia uruchamiania i zamykania.</span><span class="sxs-lookup"><span data-stu-id="77857-389">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="77857-390">Token anulowania</span><span class="sxs-lookup"><span data-stu-id="77857-390">Cancellation Token</span></span>    | <span data-ttu-id="77857-391">Wyzwalane, gdy&#8230;</span><span class="sxs-lookup"><span data-stu-id="77857-391">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="77857-392">Host został w pełni uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="77857-392">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="77857-393">Host kończy bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="77857-393">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="77857-394">Wszystkie żądania powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="77857-394">All requests should be processed.</span></span> <span data-ttu-id="77857-395">Bloki zamknięcia do momentu zakończenia tego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="77857-395">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="77857-396">Host wykonuje bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="77857-396">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="77857-397">Żądania mogą nadal być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="77857-397">Requests may still be processing.</span></span> <span data-ttu-id="77857-398">Bloki zamknięcia do momentu zakończenia tego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="77857-398">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="77857-399">`StopApplication` żąda zakończenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-399">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="77857-400">Następująca Klasa służy `StopApplication` do bezpiecznego zamykania aplikacji w przypadku `Shutdown` wywołania metody klasy:</span><span class="sxs-lookup"><span data-stu-id="77857-400">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="77857-401">IApplicationLifetime, interfejs</span><span class="sxs-lookup"><span data-stu-id="77857-401">IApplicationLifetime interface</span></span>

<span data-ttu-id="77857-402">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) umożliwia działanie po uruchomieniu i zamknięciu.</span><span class="sxs-lookup"><span data-stu-id="77857-402">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="77857-403">Trzy właściwości interfejsu są tokenami anulowania używanymi do rejestrowania `Action` metod, które definiują zdarzenia uruchamiania i zamykania.</span><span class="sxs-lookup"><span data-stu-id="77857-403">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="77857-404">Token anulowania</span><span class="sxs-lookup"><span data-stu-id="77857-404">Cancellation Token</span></span>    | <span data-ttu-id="77857-405">Wyzwalane, gdy&#8230;</span><span class="sxs-lookup"><span data-stu-id="77857-405">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="77857-406">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="77857-406">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="77857-407">Host został w pełni uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="77857-407">The host has fully started.</span></span> |
| [<span data-ttu-id="77857-408">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="77857-408">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="77857-409">Host kończy bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="77857-409">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="77857-410">Wszystkie żądania powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="77857-410">All requests should be processed.</span></span> <span data-ttu-id="77857-411">Bloki zamknięcia do momentu zakończenia tego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="77857-411">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="77857-412">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="77857-412">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="77857-413">Host wykonuje bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="77857-413">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="77857-414">Żądania mogą nadal być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="77857-414">Requests may still be processing.</span></span> <span data-ttu-id="77857-415">Bloki zamknięcia do momentu zakończenia tego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="77857-415">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="77857-416">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) żądania zakończenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-416">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="77857-417">Następująca Klasa służy `StopApplication` do bezpiecznego zamykania aplikacji w przypadku `Shutdown` wywołania metody klasy:</span><span class="sxs-lookup"><span data-stu-id="77857-417">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="77857-418">Weryfikacja zakresu</span><span class="sxs-lookup"><span data-stu-id="77857-418">Scope validation</span></span>

<span data-ttu-id="77857-419">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) ustawia [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true` Jeśli środowisko aplikacji jest opracowywane.</span><span class="sxs-lookup"><span data-stu-id="77857-419">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="77857-420">Gdy `ValidateScopes` jest ustawiona na `true` , domyślny dostawca usług sprawdza, czy:</span><span class="sxs-lookup"><span data-stu-id="77857-420">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="77857-421">Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.</span><span class="sxs-lookup"><span data-stu-id="77857-421">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="77857-422">Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="77857-422">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="77857-423">Główny dostawca usług jest tworzony, gdy wywoływana jest [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) .</span><span class="sxs-lookup"><span data-stu-id="77857-423">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="77857-424">Okres istnienia dostawcy usług głównych odnosi się do okresu istnienia aplikacji/serwera, gdy dostawca zaczyna się od aplikacji i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="77857-424">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="77857-425">Usługi o określonym zakresie są usuwane przez kontener, który go utworzył.</span><span class="sxs-lookup"><span data-stu-id="77857-425">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="77857-426">Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest wyłączony.</span><span class="sxs-lookup"><span data-stu-id="77857-426">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="77857-427">Sprawdzanie poprawności zakresów usług przechwytuje te sytuacje, gdy `BuildServiceProvider` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="77857-427">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="77857-428">Aby zawsze weryfikować zakresy, w tym w środowisku produkcyjnym, należy skonfigurować [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) z [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) na konstruktorze hosta:</span><span class="sxs-lookup"><span data-stu-id="77857-428">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="77857-429">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="77857-429">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
