---
title: ASP.NET Core hosta sieci Web
author: rick-anderson
description: Dowiedz się więcej o hoście sieci Web w ASP.NET Core, który jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/web-host
ms.openlocfilehash: 71bca4c0987059efa0e4ff35f25fe7cdb75641d5
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773994"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="d7916-103">ASP.NET Core hosta sieci Web</span><span class="sxs-lookup"><span data-stu-id="d7916-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="d7916-104">ASP.NET Core aplikacje konfigurują i uruchamiają *hosta*.</span><span class="sxs-lookup"><span data-stu-id="d7916-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="d7916-105">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="d7916-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="d7916-106">Host konfiguruje co najmniej serwer i potok przetwarzania żądań.</span><span class="sxs-lookup"><span data-stu-id="d7916-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="d7916-107">Na hoście można także skonfigurować rejestrowanie, iniekcję zależności i konfigurację.</span><span class="sxs-lookup"><span data-stu-id="d7916-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d7916-108">W tym artykule omówiono hosta sieci Web, który jest dostępny tylko w celu zapewnienia zgodności z poprzednimi wersjami.</span><span class="sxs-lookup"><span data-stu-id="d7916-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="d7916-109">[Host ogólny](xref:fundamentals/host/generic-host) jest zalecany dla wszystkich typów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d7916-110">W tym artykule omówiono hosta sieci Web, który służy do hostowania aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="d7916-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="d7916-111">W przypadku innych rodzajów aplikacji należy użyć [hosta ogólnego](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="d7916-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="d7916-112">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="d7916-112">Set up a host</span></span>

<span data-ttu-id="d7916-113">Utwórz hosta przy użyciu wystąpienia [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="d7916-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="d7916-114">Jest to zwykle wykonywane w punkcie wejścia aplikacji, w `Main` metodzie.</span><span class="sxs-lookup"><span data-stu-id="d7916-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="d7916-115">W szablonach `Main` projektu znajduje się w *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="d7916-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="d7916-116">Typowe wywołania aplikacji [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) do rozpoczęcia konfigurowania hosta:</span><span class="sxs-lookup"><span data-stu-id="d7916-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

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

<span data-ttu-id="d7916-117">Kod, który wywołuje `CreateDefaultBuilder` , znajduje się w metodzie o nazwie `CreateWebHostBuilder`, która oddziela ją od kodu `Main` w ramach `Run` tego wywołania w obiekcie konstruktora.</span><span class="sxs-lookup"><span data-stu-id="d7916-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="d7916-118">Ta separacja jest wymagana, jeśli używasz [narzędzi Entity Framework Core](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="d7916-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="d7916-119">Narzędzia oczekują na znalezienie `CreateWebHostBuilder` metody, którą mogą wywołać w czasie projektowania, aby skonfigurować hosta bez uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="d7916-120">Alternatywą jest zaimplementowanie `IDesignTimeDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="d7916-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="d7916-121">Aby uzyskać więcej informacji, zobacz [Tworzenie DbContext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="d7916-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="d7916-122">Metoda `CreateDefaultBuilder` wykonuje następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="d7916-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="d7916-123">Konfiguruje serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci Web przy użyciu dostawców konfiguracji hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="d7916-124">Aby poznać domyślne opcje serwera Kestrel, zobacz <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="d7916-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="d7916-125">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) dla ścieżki zwróconej przez [katalog. GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="d7916-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="d7916-126">Ładuje [konfigurację hosta](#host-configuration-values) z:</span><span class="sxs-lookup"><span data-stu-id="d7916-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="d7916-127">Zmienne środowiskowe poprzedzone `ASPNETCORE_` prefiksem (na `ASPNETCORE_ENVIRONMENT`przykład).</span><span class="sxs-lookup"><span data-stu-id="d7916-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="d7916-128">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="d7916-128">Command-line arguments.</span></span>
* <span data-ttu-id="d7916-129">Ładuje konfigurację aplikacji w następującej kolejności:</span><span class="sxs-lookup"><span data-stu-id="d7916-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="d7916-130">*appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="d7916-130">*appsettings.json*.</span></span>
  * <span data-ttu-id="d7916-131">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="d7916-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="d7916-132">[Secret Manager](xref:security/app-secrets) , gdy aplikacja jest uruchamiana `Development` w środowisku przy użyciu zestawu wpisów.</span><span class="sxs-lookup"><span data-stu-id="d7916-132">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="d7916-133">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="d7916-133">Environment variables.</span></span>
  * <span data-ttu-id="d7916-134">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="d7916-134">Command-line arguments.</span></span>
* <span data-ttu-id="d7916-135">Konfiguruje [Rejestrowanie](xref:fundamentals/logging/index) dla konsoli i danych wyjściowych debugowania.</span><span class="sxs-lookup"><span data-stu-id="d7916-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="d7916-136">Rejestrowanie obejmuje reguły [filtrowania dzienników](xref:fundamentals/logging/index#log-filtering) określone w sekcji konfiguracja rejestrowania w pliku *appSettings. JSON* lub *appSettings. { Environment} plik JSON* .</span><span class="sxs-lookup"><span data-stu-id="d7916-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="d7916-137">Po uruchomieniu usług IIS za pomocą [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module)program `CreateDefaultBuilder` włącza [integrację usług IIS](xref:host-and-deploy/iis/index), która konfiguruje podstawowy adres i port aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="d7916-138">Integracja usług IIS konfiguruje również aplikację do [przechwytywania błędów uruchamiania](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="d7916-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="d7916-139">Aby poznać domyślne opcje usług IIS, <xref:host-and-deploy/iis/index#iis-options>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="d7916-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="d7916-140">Ustawia [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true` , jeśli środowisko aplikacji jest opracowywane.</span><span class="sxs-lookup"><span data-stu-id="d7916-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="d7916-141">Aby uzyskać więcej informacji, zobacz [Walidacja zakresu](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="d7916-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="d7916-142">`CreateDefaultBuilder` Konfiguracja zdefiniowana przez można zastąpić i rozszerzyć przez [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)i inne metody i metody rozszerzające [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="d7916-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="d7916-143">Poniżej przedstawiono kilka przykładów:</span><span class="sxs-lookup"><span data-stu-id="d7916-143">A few examples follow:</span></span>

* <span data-ttu-id="d7916-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) służy do określania dodatkowych `IConfiguration` aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="d7916-145">Następujące `ConfigureAppConfiguration` wywołanie dodaje delegata w celu uwzględnienia konfiguracji aplikacji w pliku *appSettings. XML* .</span><span class="sxs-lookup"><span data-stu-id="d7916-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="d7916-146">`ConfigureAppConfiguration`może być wywoływana wiele razy.</span><span class="sxs-lookup"><span data-stu-id="d7916-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="d7916-147">Należy zauważyć, że ta konfiguracja nie ma zastosowania do hosta (na przykład adresów URL serwera lub środowiska).</span><span class="sxs-lookup"><span data-stu-id="d7916-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="d7916-148">Zapoznaj się z sekcją [wartości konfiguracji hosta](#host-configuration-values) .</span><span class="sxs-lookup"><span data-stu-id="d7916-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="d7916-149">Następujące `ConfigureLogging` wywołanie dodaje delegata w celu skonfigurowania minimalnego poziomu rejestrowania ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) na wartość [LogLevel. Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="d7916-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="d7916-150">To ustawienie zastępuje ustawienia w *appSettings. Development. JSON* (`LogLevel.Debug`) i *appSettings. Production. JSON* (`LogLevel.Error`) skonfigurowany `CreateDefaultBuilder`przez.</span><span class="sxs-lookup"><span data-stu-id="d7916-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d7916-151">`ConfigureLogging`może być wywoływana wiele razy.</span><span class="sxs-lookup"><span data-stu-id="d7916-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d7916-152">Następujące wywołanie `ConfigureKestrel` przesłania domyślne [limity. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30 000 000 bajtów ustanowionych podczas konfigurowania Kestrel przez `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d7916-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="d7916-153">Następujące wywołanie [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) zastępuje domyślne [limity. MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30 000 000 bajtów ustanowionych podczas konfigurowania Kestrel przez `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d7916-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="d7916-154">[Katalog główny zawartości](xref:fundamentals/index#content-root) określa, gdzie host wyszukuje pliki zawartości, takie jak pliki widoku MVC.</span><span class="sxs-lookup"><span data-stu-id="d7916-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="d7916-155">Gdy aplikacja jest uruchamiana z folderu głównego projektu, folder główny projektu jest używany jako katalog główny zawartości.</span><span class="sxs-lookup"><span data-stu-id="d7916-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="d7916-156">Jest to ustawienie domyślne używane w programie [Visual Studio](https://visualstudio.microsoft.com) i w [nowych szablonach dotnet](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="d7916-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="d7916-157">Aby uzyskać więcej informacji na temat konfiguracji aplikacji <xref:fundamentals/configuration/index>, zobacz.</span><span class="sxs-lookup"><span data-stu-id="d7916-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="d7916-158">Alternatywą dla użycia metody statycznej `CreateDefaultBuilder` jest utworzenie hosta z [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) jest obsługiwane podejście ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="d7916-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="d7916-159">Podczas konfigurowania hosta można dostarczyć metody [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) i [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) .</span><span class="sxs-lookup"><span data-stu-id="d7916-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="d7916-160">Jeśli `Startup` Klasa jest określona, musi definiować `Configure` metodę.</span><span class="sxs-lookup"><span data-stu-id="d7916-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="d7916-161">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d7916-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="d7916-162">Wiele wywołań do `ConfigureServices` dołączenia do siebie nawzajem.</span><span class="sxs-lookup"><span data-stu-id="d7916-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="d7916-163">Wiele wywołań `Configure` lub `UseStartup` `WebHostBuilder` Zastąp poprzednie ustawienia.</span><span class="sxs-lookup"><span data-stu-id="d7916-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="d7916-164">Wartości konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="d7916-164">Host configuration values</span></span>

<span data-ttu-id="d7916-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) opiera się na następujących podejściach do ustawiania wartości konfiguracji hosta:</span><span class="sxs-lookup"><span data-stu-id="d7916-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="d7916-166">Konfiguracja konstruktora hostów, która zawiera zmienne środowiskowe w formacie `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="d7916-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="d7916-167">Na przykład `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="d7916-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="d7916-168">Rozszerzenia, takie jak [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) i [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (zobacz sekcję [Konfiguracja zastąpień](#override-configuration) ).</span><span class="sxs-lookup"><span data-stu-id="d7916-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="d7916-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) i skojarzony klucz.</span><span class="sxs-lookup"><span data-stu-id="d7916-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="d7916-170">Podczas ustawiania wartości za pomocą `UseSetting`, wartość jest ustawiana jako ciąg, niezależnie od typu.</span><span class="sxs-lookup"><span data-stu-id="d7916-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="d7916-171">Host używa dowolnej opcji ustawia wartość Last.</span><span class="sxs-lookup"><span data-stu-id="d7916-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="d7916-172">Aby uzyskać więcej informacji, zobacz [Przesłoń konfigurację](#override-configuration) w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="d7916-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="d7916-173">Klucz aplikacji (nazwa)</span><span class="sxs-lookup"><span data-stu-id="d7916-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d7916-174">Właściwość `IWebHostEnvironment.ApplicationName` jest ustawiana automatycznie, gdy [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) lub [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) jest wywoływana podczas konstruowania hosta.</span><span class="sxs-lookup"><span data-stu-id="d7916-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="d7916-175">Wartość jest ustawiana na nazwę zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="d7916-176">Aby jawnie ustawić wartość, użyj [WebHostDefaults. ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="d7916-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d7916-177">Właściwość [IHostingEnvironment. ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) jest ustawiana automatycznie, gdy [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) lub [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) jest wywoływana podczas konstruowania hosta.</span><span class="sxs-lookup"><span data-stu-id="d7916-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="d7916-178">Wartość jest ustawiana na nazwę zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="d7916-179">Aby jawnie ustawić wartość, użyj [WebHostDefaults. ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="d7916-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="d7916-180">**Klucz**: ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d7916-180">**Key**: applicationName</span></span>  
<span data-ttu-id="d7916-181">**Typ**: *ciąg*</span><span class="sxs-lookup"><span data-stu-id="d7916-181">**Type**: *string*</span></span>  
<span data-ttu-id="d7916-182">**Wartość domyślna**: Nazwa zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-182">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="d7916-183">**Ustaw przy użyciu**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="d7916-183">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="d7916-184">**Zmienna środowiskowa**:`ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="d7916-184">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="d7916-185">Błędy uruchamiania przechwytywania</span><span class="sxs-lookup"><span data-stu-id="d7916-185">Capture Startup Errors</span></span>

<span data-ttu-id="d7916-186">To ustawienie steruje przechwytywaniem błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="d7916-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="d7916-187">**Klucz**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="d7916-187">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="d7916-188">**Typ**: *bool* (`true` or `1`)</span><span class="sxs-lookup"><span data-stu-id="d7916-188">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="d7916-189">**Domyślnie**: wartość domyślna `false` to, chyba że aplikacja działa z Kestrel za usługami IIS, gdzie `true`wartość domyślna to.</span><span class="sxs-lookup"><span data-stu-id="d7916-189">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="d7916-190">**Ustaw przy użyciu**:`CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="d7916-190">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="d7916-191">**Zmienna środowiskowa**:`ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="d7916-191">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="d7916-192">Gdy `false`, błędy podczas uruchamiania, kończy się hostem.</span><span class="sxs-lookup"><span data-stu-id="d7916-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="d7916-193">Gdy `true`Host przechwytuje wyjątki podczas uruchamiania, a następnie próbuje uruchomić serwer.</span><span class="sxs-lookup"><span data-stu-id="d7916-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="d7916-194">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="d7916-194">Content root</span></span>

<span data-ttu-id="d7916-195">To ustawienie określa, gdzie ASP.NET Core rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="d7916-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="d7916-196">**Klucz**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="d7916-196">**Key**: contentRoot</span></span>  
<span data-ttu-id="d7916-197">**Typ**: *ciąg*</span><span class="sxs-lookup"><span data-stu-id="d7916-197">**Type**: *string*</span></span>  
<span data-ttu-id="d7916-198">**Domyślnie**: Domyślnie folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-198">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="d7916-199">**Ustaw przy użyciu**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="d7916-199">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="d7916-200">**Zmienna środowiskowa**:`ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="d7916-200">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="d7916-201">Katalog główny zawartości jest również używany jako ścieżka podstawowa dla [katalogu głównego sieci Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="d7916-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="d7916-202">Jeśli ścieżka katalogu głównego zawartości nie istnieje, uruchomienie hosta nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="d7916-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="d7916-203">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="d7916-203">For more information, see:</span></span>

* [<span data-ttu-id="d7916-204">Podstawy: zawartość główna</span><span class="sxs-lookup"><span data-stu-id="d7916-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="d7916-205">Katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="d7916-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="d7916-206">Szczegóły błędów</span><span class="sxs-lookup"><span data-stu-id="d7916-206">Detailed Errors</span></span>

<span data-ttu-id="d7916-207">Określa, czy mają być przechwytywane szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="d7916-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="d7916-208">**Klucz**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="d7916-208">**Key**: detailedErrors</span></span>  
<span data-ttu-id="d7916-209">**Typ**: *bool* (`true` or `1`)</span><span class="sxs-lookup"><span data-stu-id="d7916-209">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="d7916-210">**Wartość domyślna**: FAŁSZ</span><span class="sxs-lookup"><span data-stu-id="d7916-210">**Default**: false</span></span>  
<span data-ttu-id="d7916-211">**Ustaw przy użyciu**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="d7916-211">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="d7916-212">**Zmienna środowiskowa**:`ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="d7916-212">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="d7916-213">Po włączeniu (lub gdy <a href="#environment">środowisko</a> jest ustawione na `Development`), aplikacja przechwytuje szczegółowe wyjątki.</span><span class="sxs-lookup"><span data-stu-id="d7916-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="d7916-214">Środowisko</span><span class="sxs-lookup"><span data-stu-id="d7916-214">Environment</span></span>

<span data-ttu-id="d7916-215">Ustawia środowisko aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-215">Sets the app's environment.</span></span>

<span data-ttu-id="d7916-216">**Klucz**: środowisko</span><span class="sxs-lookup"><span data-stu-id="d7916-216">**Key**: environment</span></span>  
<span data-ttu-id="d7916-217">**Typ**: *ciąg*</span><span class="sxs-lookup"><span data-stu-id="d7916-217">**Type**: *string*</span></span>  
<span data-ttu-id="d7916-218">**Wartość domyślna**: produkcja</span><span class="sxs-lookup"><span data-stu-id="d7916-218">**Default**: Production</span></span>  
<span data-ttu-id="d7916-219">**Ustaw przy użyciu**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="d7916-219">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="d7916-220">**Zmienna środowiskowa**:`ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="d7916-220">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="d7916-221">Dla środowiska można ustawić dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="d7916-221">The environment can be set to any value.</span></span> <span data-ttu-id="d7916-222">Wartości zdefiniowane przez platformę `Development`obejmują `Staging`,, `Production`i.</span><span class="sxs-lookup"><span data-stu-id="d7916-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d7916-223">W wartościach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="d7916-223">Values aren't case sensitive.</span></span> <span data-ttu-id="d7916-224">Domyślnie *środowisko* jest odczytywane ze zmiennej `ASPNETCORE_ENVIRONMENT` środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="d7916-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="d7916-225">W przypadku korzystania z [programu Visual Studio](https://visualstudio.microsoft.com)zmienne środowiskowe można ustawić w pliku *profilu launchsettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d7916-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="d7916-226">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d7916-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="d7916-227">Hostowanie zestawów startowych</span><span class="sxs-lookup"><span data-stu-id="d7916-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="d7916-228">Ustawia zestawy uruchomieniowe obsługujące aplikację.</span><span class="sxs-lookup"><span data-stu-id="d7916-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="d7916-229">**Klucz**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="d7916-229">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="d7916-230">**Typ**: *ciąg*</span><span class="sxs-lookup"><span data-stu-id="d7916-230">**Type**: *string*</span></span>  
<span data-ttu-id="d7916-231">**Wartość domyślna**: pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="d7916-231">**Default**: Empty string</span></span>  
<span data-ttu-id="d7916-232">**Ustaw przy użyciu**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="d7916-232">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="d7916-233">**Zmienna środowiskowa**:`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d7916-233">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="d7916-234">Rozdzielany średnikami ciąg początkowych zestawów startowych do załadowania podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="d7916-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="d7916-235">Mimo że wartość konfiguracji jest domyślnie pustym ciągiem, zestaw startowy obsługujący zawsze zawiera zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="d7916-236">W przypadku udostępniania zestawów startowych są one dodawane do zestawu aplikacji do załadowania, gdy aplikacja kompiluje swoje popularne usługi podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="d7916-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="d7916-237">Port HTTPS</span><span class="sxs-lookup"><span data-stu-id="d7916-237">HTTPS Port</span></span>

<span data-ttu-id="d7916-238">Ustaw port przekierowania HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d7916-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="d7916-239">Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="d7916-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d7916-240">**Klucz**: https_port **Typ**: *ciąg*
**domyślny**: nie ustawiono wartości domyślnej.</span><span class="sxs-lookup"><span data-stu-id="d7916-240">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="d7916-241">**Ustaw przy użyciu**: `UseSetting` 
 **zmienna środowiskowa**:`ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="d7916-241">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="d7916-242">Hosting — wykluczanie zestawów</span><span class="sxs-lookup"><span data-stu-id="d7916-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="d7916-243">Rozdzielany średnikami ciąg początkowych zestawów uruchamiania, który ma zostać wykluczony podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="d7916-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="d7916-244">**Klucz**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="d7916-244">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="d7916-245">**Typ**: *ciąg*</span><span class="sxs-lookup"><span data-stu-id="d7916-245">**Type**: *string*</span></span>  
<span data-ttu-id="d7916-246">**Wartość domyślna**: pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="d7916-246">**Default**: Empty string</span></span>  
<span data-ttu-id="d7916-247">**Ustaw przy użyciu**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="d7916-247">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="d7916-248">**Zmienna środowiskowa**:`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d7916-248">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="d7916-249">Preferuj adresy URL hostingu</span><span class="sxs-lookup"><span data-stu-id="d7916-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="d7916-250">Wskazuje, czy host powinien nasłuchiwać adresów URL skonfigurowanych przy `WebHostBuilder` użyciu zamiast tych skonfigurowanych przy `IServer` użyciu implementacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="d7916-251">**Klucz**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="d7916-251">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="d7916-252">**Typ**: *bool* (`true` or `1`)</span><span class="sxs-lookup"><span data-stu-id="d7916-252">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="d7916-253">Wartość **Domyślna**: prawda</span><span class="sxs-lookup"><span data-stu-id="d7916-253">**Default**: true</span></span>  
<span data-ttu-id="d7916-254">**Ustaw przy użyciu**:`PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="d7916-254">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="d7916-255">**Zmienna środowiskowa**:`ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="d7916-255">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="d7916-256">Zapobiegaj uruchamianiu hostingu</span><span class="sxs-lookup"><span data-stu-id="d7916-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="d7916-257">Zapobiega automatycznemu ładowaniu zestawów startowych hostingu, w tym hostingu zestawów startowych skonfigurowanych przez zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="d7916-258">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d7916-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="d7916-259">**Klucz**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="d7916-259">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="d7916-260">**Typ**: *bool* (`true` or `1`)</span><span class="sxs-lookup"><span data-stu-id="d7916-260">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="d7916-261">**Wartość domyślna**: FAŁSZ</span><span class="sxs-lookup"><span data-stu-id="d7916-261">**Default**: false</span></span>  
<span data-ttu-id="d7916-262">**Ustaw przy użyciu**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="d7916-262">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="d7916-263">**Zmienna środowiskowa**:`ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="d7916-263">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="d7916-264">Adresy URL serwera</span><span class="sxs-lookup"><span data-stu-id="d7916-264">Server URLs</span></span>

<span data-ttu-id="d7916-265">Wskazuje adresy IP lub adresy hosta z portami i protokołami, na których serwer powinien nasłuchiwać żądań.</span><span class="sxs-lookup"><span data-stu-id="d7916-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="d7916-266">**Klucz**: adresy URL</span><span class="sxs-lookup"><span data-stu-id="d7916-266">**Key**: urls</span></span>  
<span data-ttu-id="d7916-267">**Typ**: *ciąg*</span><span class="sxs-lookup"><span data-stu-id="d7916-267">**Type**: *string*</span></span>  
<span data-ttu-id="d7916-268">**Wartość domyślna**:http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="d7916-268">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="d7916-269">**Ustaw przy użyciu**:`UseUrls`</span><span class="sxs-lookup"><span data-stu-id="d7916-269">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="d7916-270">**Zmienna środowiskowa**:`ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="d7916-270">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="d7916-271">Ustaw na rozdzieloną średnikami (;) Lista prefiksów adresów URL, do których serwer powinien reagować.</span><span class="sxs-lookup"><span data-stu-id="d7916-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="d7916-272">Na przykład `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="d7916-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="d7916-273">Użyj "\*", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwie hosta przy użyciu określonego portu i protokołu ( `http://*:5000`na przykład).</span><span class="sxs-lookup"><span data-stu-id="d7916-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="d7916-274">Protokół (`http://` lub `https://`) musi być dołączony do każdego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="d7916-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="d7916-275">Obsługiwane formaty różnią się między serwerami.</span><span class="sxs-lookup"><span data-stu-id="d7916-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="d7916-276">Kestrel ma własny interfejs API konfiguracji punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="d7916-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="d7916-277">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d7916-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="d7916-278">Limit czasu zamykania</span><span class="sxs-lookup"><span data-stu-id="d7916-278">Shutdown Timeout</span></span>

<span data-ttu-id="d7916-279">Określa czas oczekiwania na zamknięcie hosta sieci Web.</span><span class="sxs-lookup"><span data-stu-id="d7916-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="d7916-280">**Klucz**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="d7916-280">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="d7916-281">**Typ**: *int*</span><span class="sxs-lookup"><span data-stu-id="d7916-281">**Type**: *int*</span></span>  
<span data-ttu-id="d7916-282">**Wartość domyślna**: 5</span><span class="sxs-lookup"><span data-stu-id="d7916-282">**Default**: 5</span></span>  
<span data-ttu-id="d7916-283">**Ustaw przy użyciu**:`UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="d7916-283">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="d7916-284">**Zmienna środowiskowa**:`ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="d7916-284">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="d7916-285">Chociaż klucz akceptuje *int* z `UseSetting` (na przykład `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), Metoda rozszerzenia [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) przyjmuje wartość [TimeSpan](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="d7916-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="d7916-286">Podczas okresu przekroczenia limitu czasu hostowanie:</span><span class="sxs-lookup"><span data-stu-id="d7916-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="d7916-287">Wyzwala [IApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="d7916-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="d7916-288">Próbuje zatrzymać usługi hostowane, rejestrując błędy dla usług, których nie można zatrzymać.</span><span class="sxs-lookup"><span data-stu-id="d7916-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="d7916-289">Jeśli limit czasu upłynie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="d7916-290">Usługi są zatrzymane nawet wtedy, gdy nie zakończyły przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="d7916-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="d7916-291">Jeśli usługi wymagają dodatkowego czasu na zatrzymanie, zwiększ limit czasu.</span><span class="sxs-lookup"><span data-stu-id="d7916-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="d7916-292">Zestaw startowy</span><span class="sxs-lookup"><span data-stu-id="d7916-292">Startup Assembly</span></span>

<span data-ttu-id="d7916-293">Określa zestaw, który ma być wyszukiwany dla `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="d7916-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="d7916-294">**Klucz**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="d7916-294">**Key**: startupAssembly</span></span>  
<span data-ttu-id="d7916-295">**Typ**: *ciąg*</span><span class="sxs-lookup"><span data-stu-id="d7916-295">**Type**: *string*</span></span>  
<span data-ttu-id="d7916-296">**Domyślnie**: zestaw aplikacji</span><span class="sxs-lookup"><span data-stu-id="d7916-296">**Default**: The app's assembly</span></span>  
<span data-ttu-id="d7916-297">**Ustaw przy użyciu**:`UseStartup`</span><span class="sxs-lookup"><span data-stu-id="d7916-297">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="d7916-298">**Zmienna środowiskowa**:`ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="d7916-298">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="d7916-299">Można odwołać się do`string`zestawu według nazwy (`TStartup`) lub typu ().</span><span class="sxs-lookup"><span data-stu-id="d7916-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="d7916-300">Jeśli wywoływana `UseStartup` jest wiele metod, pierwszeństwo ma Ostatnia.</span><span class="sxs-lookup"><span data-stu-id="d7916-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="d7916-301">Katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="d7916-301">Web root</span></span>

<span data-ttu-id="d7916-302">Ustawia ścieżkę względną do statycznych zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="d7916-303">**Klucz**: Webroot</span><span class="sxs-lookup"><span data-stu-id="d7916-303">**Key**: webroot</span></span>  
<span data-ttu-id="d7916-304">**Typ**: *ciąg*</span><span class="sxs-lookup"><span data-stu-id="d7916-304">**Type**: *string*</span></span>  
<span data-ttu-id="d7916-305">**Wartość domyślna**: wartość domyślna `wwwroot`to.</span><span class="sxs-lookup"><span data-stu-id="d7916-305">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="d7916-306">Ścieżka do *elementu {content root}/wwwroot* musi istnieć.</span><span class="sxs-lookup"><span data-stu-id="d7916-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="d7916-307">Jeśli ścieżka nie istnieje, jest używany dostawca plików No-op.</span><span class="sxs-lookup"><span data-stu-id="d7916-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="d7916-308">**Ustaw przy użyciu**:`UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="d7916-308">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="d7916-309">**Zmienna środowiskowa**:`ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="d7916-309">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="d7916-310">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="d7916-310">For more information, see:</span></span>

* [<span data-ttu-id="d7916-311">Podstawy: katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="d7916-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="d7916-312">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="d7916-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="d7916-313">Zastąp konfigurację</span><span class="sxs-lookup"><span data-stu-id="d7916-313">Override configuration</span></span>

<span data-ttu-id="d7916-314">Skonfiguruj hosta sieci Web za pomocą [konfiguracji](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="d7916-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="d7916-315">W poniższym przykładzie Konfiguracja hosta jest opcjonalnie określona w pliku *HostSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d7916-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="d7916-316">Wszystkie konfiguracje załadowane z pliku *HostSettings. JSON* mogą zostać zastąpione przez argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="d7916-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="d7916-317">Utworzona konfiguracja (w programie `config`) służy do konfigurowania hosta z [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="d7916-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="d7916-318">`IWebHostBuilder`Konfiguracja jest dodawana do konfiguracji aplikacji, ale&mdash; `ConfigureAppConfiguration` nie ma ona wpływu na `IWebHostBuilder` konfigurację.</span><span class="sxs-lookup"><span data-stu-id="d7916-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="d7916-319">Zastępowanie konfiguracji podanej przy `UseUrls` użyciu pliku *HostSettings. JSON* należy najpierw skonfigurować argument wiersza polecenia Second:</span><span class="sxs-lookup"><span data-stu-id="d7916-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

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

<span data-ttu-id="d7916-320">*HostSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="d7916-320">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="d7916-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) kopiuje tylko klucze z dostarczonej `IConfiguration` do konfiguracji konstruktora hostów.</span><span class="sxs-lookup"><span data-stu-id="d7916-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="d7916-322">W związku z `reloadOnChange: true` tym nie ma żadnego wpływu na ustawienia plików JSON, ini i XML.</span><span class="sxs-lookup"><span data-stu-id="d7916-322">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="d7916-323">Aby określić hosta na określonym adresie URL, wymagana wartość może zostać przeniesiona z wiersza polecenia podczas wykonywania [przebiegu dotnet](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="d7916-323">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="d7916-324">Argument wiersza polecenia zastępuje `urls` wartość z pliku *HostSettings. JSON* , a serwer nasłuchuje na porcie 8080:</span><span class="sxs-lookup"><span data-stu-id="d7916-324">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="d7916-325">Zarządzanie hostem</span><span class="sxs-lookup"><span data-stu-id="d7916-325">Manage the host</span></span>

<span data-ttu-id="d7916-326">**Run**</span><span class="sxs-lookup"><span data-stu-id="d7916-326">**Run**</span></span>

<span data-ttu-id="d7916-327">`Run` Metoda uruchamia aplikację sieci Web i blokuje wątek wywołujący do momentu wyłączenia hosta:</span><span class="sxs-lookup"><span data-stu-id="d7916-327">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="d7916-328">**Początek**</span><span class="sxs-lookup"><span data-stu-id="d7916-328">**Start**</span></span>

<span data-ttu-id="d7916-329">Uruchom hosta w sposób nieblokowany przez wywołanie jego `Start` metody:</span><span class="sxs-lookup"><span data-stu-id="d7916-329">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="d7916-330">Jeśli lista adresów URL jest przenoszona do `Start` metody, nasłuchuje na określonych adresach URL:</span><span class="sxs-lookup"><span data-stu-id="d7916-330">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

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

<span data-ttu-id="d7916-331">Aplikacja może inicjować i uruchamiać nowy host przy użyciu wstępnie skonfigurowanych ustawień domyślnych `CreateDefaultBuilder` przy użyciu statycznej metody wygodnej.</span><span class="sxs-lookup"><span data-stu-id="d7916-331">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="d7916-332">Metody te służą do uruchamiania serwera bez danych wyjściowych konsoli i [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) poczekać na przerwanie (Ctrl-C/SIGINT lub SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="d7916-332">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="d7916-333">**Rozpocznij (aplikacja RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="d7916-333">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="d7916-334">Zacznij od `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="d7916-334">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="d7916-335">Wykonaj żądanie w przeglądarce, aby `http://localhost:5000` otrzymać odpowiedź "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="d7916-335">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="d7916-336">`WaitForShutdown`bloki do momentu wystawienia przerwy (Ctrl-C/SIGINT lub SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="d7916-336">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="d7916-337">Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na zamknięcie naciśnięcia.</span><span class="sxs-lookup"><span data-stu-id="d7916-337">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="d7916-338">**Rozpocznij (adres URL ciągu, aplikacja RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="d7916-338">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="d7916-339">Zacznij od adresu URL i `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="d7916-339">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="d7916-340">Daje ten sam wynik jako **Start (RequestDelegate App)**, z wyjątkiem tego, że aplikacja `http://localhost:8080`reaguje na.</span><span class="sxs-lookup"><span data-stu-id="d7916-340">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="d7916-341">**Uruchom (Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="d7916-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="d7916-342">Użyj wystąpienia programu `IRouteBuilder` ([Microsoft. AspNetCore. Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)), aby użyć oprogramowania pośredniczącego routingu:</span><span class="sxs-lookup"><span data-stu-id="d7916-342">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

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

<span data-ttu-id="d7916-343">Użyj poniższego żądania przeglądarki z przykładem:</span><span class="sxs-lookup"><span data-stu-id="d7916-343">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="d7916-344">Żądanie</span><span class="sxs-lookup"><span data-stu-id="d7916-344">Request</span></span>                                    | <span data-ttu-id="d7916-345">Odpowiedź</span><span class="sxs-lookup"><span data-stu-id="d7916-345">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="d7916-346">Witaj, Martin!</span><span class="sxs-lookup"><span data-stu-id="d7916-346">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="d7916-347">Buenos Dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="d7916-347">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="d7916-348">Zgłasza wyjątek z ciągiem "ooops!"</span><span class="sxs-lookup"><span data-stu-id="d7916-348">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="d7916-349">Zgłasza wyjątek z ciągiem "zapomniano"</span><span class="sxs-lookup"><span data-stu-id="d7916-349">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="d7916-350">Sante, Jan!</span><span class="sxs-lookup"><span data-stu-id="d7916-350">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="d7916-351">Hello World!</span><span class="sxs-lookup"><span data-stu-id="d7916-351">Hello World!</span></span>                             |

<span data-ttu-id="d7916-352">`WaitForShutdown`bloki do momentu wystawienia przerwy (Ctrl-C/SIGINT lub SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="d7916-352">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="d7916-353">Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na zamknięcie naciśnięcia.</span><span class="sxs-lookup"><span data-stu-id="d7916-353">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="d7916-354">**Rozpocznij (ciąg URL: Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="d7916-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="d7916-355">Użyj adresu URL i wystąpienia `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d7916-355">Use a URL and an instance of `IRouteBuilder`:</span></span>

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

<span data-ttu-id="d7916-356">Daje ten sam wynik jako **początkowy (Action\<IRouteBuilder> routeBuilder)**, z wyjątkiem tego, że aplikacja `http://localhost:8080`reaguje na.</span><span class="sxs-lookup"><span data-stu-id="d7916-356">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="d7916-357">**StartWith (Akcja\<IApplicationBuilder> aplikacji)**</span><span class="sxs-lookup"><span data-stu-id="d7916-357">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="d7916-358">Podaj delegata, aby skonfigurować `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d7916-358">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="d7916-359">Wykonaj żądanie w przeglądarce, aby `http://localhost:5000` otrzymać odpowiedź "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="d7916-359">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="d7916-360">`WaitForShutdown`bloki do momentu wystawienia przerwy (Ctrl-C/SIGINT lub SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="d7916-360">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="d7916-361">Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na zamknięcie naciśnięcia.</span><span class="sxs-lookup"><span data-stu-id="d7916-361">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="d7916-362">**StartWith (adres URL ciągu,\<akcja IApplicationBuilder> aplikacji)**</span><span class="sxs-lookup"><span data-stu-id="d7916-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="d7916-363">Podaj adres URL i delegata, aby skonfigurować `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d7916-363">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="d7916-364">Daje ten sam wynik jako **StartWith (Akcja\<IApplicationBuilder> aplikacji)**, z wyjątkiem tego, że aplikacja `http://localhost:8080`odpowiada.</span><span class="sxs-lookup"><span data-stu-id="d7916-364">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="d7916-365">IWebHostEnvironment, interfejs</span><span class="sxs-lookup"><span data-stu-id="d7916-365">IWebHostEnvironment interface</span></span>

<span data-ttu-id="d7916-366">`IWebHostEnvironment` Interfejs zawiera informacje o środowisku hostingu aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="d7916-366">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="d7916-367">Użyj [iniekcji konstruktorów](xref:fundamentals/dependency-injection) , `IWebHostEnvironment` Aby uzyskać w celu użycia jej właściwości i metod rozszerzających:</span><span class="sxs-lookup"><span data-stu-id="d7916-367">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="d7916-368">[Podejście oparte na Konwencji](xref:fundamentals/environments#environment-based-startup-class-and-methods) może służyć do konfigurowania aplikacji podczas uruchamiania na podstawie środowiska.</span><span class="sxs-lookup"><span data-stu-id="d7916-368">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="d7916-369">Alternatywnie można wstrzyknąć `IWebHostEnvironment` do `Startup` konstruktora do użycia w: `ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="d7916-369">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="d7916-370">`IsDevelopment` Oprócz metody rozszerzenia, `IWebHostEnvironment` oferty `IsStaging`, `IsProduction`i `IsEnvironment(string environmentName)` metody.</span><span class="sxs-lookup"><span data-stu-id="d7916-370">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="d7916-371">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d7916-371">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d7916-372">`IWebHostEnvironment` Usługę można również wstrzyknąć bezpośrednio do `Configure` metody w celu skonfigurowania potoku przetwarzania:</span><span class="sxs-lookup"><span data-stu-id="d7916-372">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="d7916-373">`IWebHostEnvironment`można wstrzyknąć do `Invoke` metody podczas tworzenia niestandardowego [oprogramowania pośredniczącego](xref:fundamentals/middleware/write):</span><span class="sxs-lookup"><span data-stu-id="d7916-373">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="d7916-374">IHostingEnvironment, interfejs</span><span class="sxs-lookup"><span data-stu-id="d7916-374">IHostingEnvironment interface</span></span>

<span data-ttu-id="d7916-375">[Interfejs IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) zawiera informacje o środowisku hostingu aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="d7916-375">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="d7916-376">Użyj [iniekcji konstruktorów](xref:fundamentals/dependency-injection) , `IHostingEnvironment` Aby uzyskać w celu użycia jej właściwości i metod rozszerzających:</span><span class="sxs-lookup"><span data-stu-id="d7916-376">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="d7916-377">[Podejście oparte na Konwencji](xref:fundamentals/environments#environment-based-startup-class-and-methods) może służyć do konfigurowania aplikacji podczas uruchamiania na podstawie środowiska.</span><span class="sxs-lookup"><span data-stu-id="d7916-377">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="d7916-378">Alternatywnie można wstrzyknąć `IHostingEnvironment` do `Startup` konstruktora do użycia w: `ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="d7916-378">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="d7916-379">`IsDevelopment` Oprócz metody rozszerzenia, `IHostingEnvironment` oferty `IsStaging`, `IsProduction`i `IsEnvironment(string environmentName)` metody.</span><span class="sxs-lookup"><span data-stu-id="d7916-379">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="d7916-380">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d7916-380">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d7916-381">`IHostingEnvironment` Usługę można również wstrzyknąć bezpośrednio do `Configure` metody w celu skonfigurowania potoku przetwarzania:</span><span class="sxs-lookup"><span data-stu-id="d7916-381">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="d7916-382">`IHostingEnvironment`można wstrzyknąć do `Invoke` metody podczas tworzenia niestandardowego [oprogramowania pośredniczącego](xref:fundamentals/middleware/write):</span><span class="sxs-lookup"><span data-stu-id="d7916-382">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="d7916-383">IHostApplicationLifetime, interfejs</span><span class="sxs-lookup"><span data-stu-id="d7916-383">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="d7916-384">`IHostApplicationLifetime`zezwala na działania po uruchomieniu i zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="d7916-384">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="d7916-385">Trzy właściwości interfejsu są tokenami anulowania używanymi do rejestrowania `Action` metod, które definiują zdarzenia uruchamiania i zamykania.</span><span class="sxs-lookup"><span data-stu-id="d7916-385">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="d7916-386">Token anulowania</span><span class="sxs-lookup"><span data-stu-id="d7916-386">Cancellation Token</span></span>    | <span data-ttu-id="d7916-387">Wyzwalane, gdy&#8230;</span><span class="sxs-lookup"><span data-stu-id="d7916-387">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="d7916-388">Host został w pełni uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="d7916-388">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="d7916-389">Host kończy bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="d7916-389">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="d7916-390">Wszystkie żądania powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="d7916-390">All requests should be processed.</span></span> <span data-ttu-id="d7916-391">Bloki zamknięcia do momentu zakończenia tego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="d7916-391">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="d7916-392">Host wykonuje bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="d7916-392">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="d7916-393">Żądania mogą nadal być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="d7916-393">Requests may still be processing.</span></span> <span data-ttu-id="d7916-394">Bloki zamknięcia do momentu zakończenia tego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="d7916-394">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="d7916-395">`StopApplication`żąda zakończenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-395">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="d7916-396">Następująca Klasa służy `StopApplication` do bezpiecznego zamykania aplikacji w przypadku wywołania `Shutdown` metody klasy:</span><span class="sxs-lookup"><span data-stu-id="d7916-396">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="d7916-397">IApplicationLifetime, interfejs</span><span class="sxs-lookup"><span data-stu-id="d7916-397">IApplicationLifetime interface</span></span>

<span data-ttu-id="d7916-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) umożliwia działanie po uruchomieniu i zamknięciu.</span><span class="sxs-lookup"><span data-stu-id="d7916-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="d7916-399">Trzy właściwości interfejsu są tokenami anulowania używanymi do rejestrowania `Action` metod, które definiują zdarzenia uruchamiania i zamykania.</span><span class="sxs-lookup"><span data-stu-id="d7916-399">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="d7916-400">Token anulowania</span><span class="sxs-lookup"><span data-stu-id="d7916-400">Cancellation Token</span></span>    | <span data-ttu-id="d7916-401">Wyzwalane, gdy&#8230;</span><span class="sxs-lookup"><span data-stu-id="d7916-401">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="d7916-402">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="d7916-402">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="d7916-403">Host został w pełni uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="d7916-403">The host has fully started.</span></span> |
| [<span data-ttu-id="d7916-404">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="d7916-404">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="d7916-405">Host kończy bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="d7916-405">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="d7916-406">Wszystkie żądania powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="d7916-406">All requests should be processed.</span></span> <span data-ttu-id="d7916-407">Bloki zamknięcia do momentu zakończenia tego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="d7916-407">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="d7916-408">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="d7916-408">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="d7916-409">Host wykonuje bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="d7916-409">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="d7916-410">Żądania mogą nadal być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="d7916-410">Requests may still be processing.</span></span> <span data-ttu-id="d7916-411">Bloki zamknięcia do momentu zakończenia tego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="d7916-411">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="d7916-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) żądania zakończenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="d7916-413">Następująca Klasa służy `StopApplication` do bezpiecznego zamykania aplikacji w przypadku wywołania `Shutdown` metody klasy:</span><span class="sxs-lookup"><span data-stu-id="d7916-413">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="d7916-414">Weryfikacja zakresu</span><span class="sxs-lookup"><span data-stu-id="d7916-414">Scope validation</span></span>

<span data-ttu-id="d7916-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) ustawia [ServiceProviderOptions. ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) na `true` Jeśli środowisko aplikacji jest opracowywane.</span><span class="sxs-lookup"><span data-stu-id="d7916-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="d7916-416">Gdy `ValidateScopes` jest ustawiona na `true`, domyślny dostawca usług sprawdza, czy:</span><span class="sxs-lookup"><span data-stu-id="d7916-416">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="d7916-417">Usługi w zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez dostawcę usług głównych.</span><span class="sxs-lookup"><span data-stu-id="d7916-417">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="d7916-418">Usługi w zakresie nie są bezpośrednio lub pośrednio wstrzykiwane do pojedynczych.</span><span class="sxs-lookup"><span data-stu-id="d7916-418">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="d7916-419">Główny dostawca usług jest tworzony, gdy wywoływana jest [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) .</span><span class="sxs-lookup"><span data-stu-id="d7916-419">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="d7916-420">Okres istnienia dostawcy usług głównych odnosi się do okresu istnienia aplikacji/serwera, gdy dostawca zaczyna się od aplikacji i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d7916-420">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="d7916-421">Usługi o określonym zakresie są usuwane przez kontener, który go utworzył.</span><span class="sxs-lookup"><span data-stu-id="d7916-421">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="d7916-422">Jeśli w kontenerze głównym zostanie utworzona usługa o określonym zakresie, okres istnienia usługi zostanie skutecznie podwyższony do pojedynczej, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest wyłączony.</span><span class="sxs-lookup"><span data-stu-id="d7916-422">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="d7916-423">Sprawdzanie poprawności zakresów usług przechwytuje te `BuildServiceProvider` sytuacje, gdy jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="d7916-423">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="d7916-424">Aby zawsze weryfikować zakresy, w tym w środowisku produkcyjnym, należy skonfigurować [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) z [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) na konstruktorze hosta:</span><span class="sxs-lookup"><span data-stu-id="d7916-424">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="d7916-425">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="d7916-425">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
