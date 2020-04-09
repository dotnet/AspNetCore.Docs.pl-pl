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
# <a name="aspnet-core-web-host"></a><span data-ttu-id="8c2df-103">ASP.NET Core Web Host</span><span class="sxs-lookup"><span data-stu-id="8c2df-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="8c2df-104">ASP.NET aplikacje Core konfigurują i uruchamiają *hosta.*</span><span class="sxs-lookup"><span data-stu-id="8c2df-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="8c2df-105">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="8c2df-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8c2df-106">Co najmniej host konfiguruje serwer i potok przetwarzania żądań.</span><span class="sxs-lookup"><span data-stu-id="8c2df-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="8c2df-107">Host można również skonfigurować rejestrowanie, iniekcji zależności i konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8c2df-108">W tym artykule omówiono hosta sieci Web, który pozostaje dostępny tylko dla zgodności z powrotem.</span><span class="sxs-lookup"><span data-stu-id="8c2df-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="8c2df-109">[Host ogólny](xref:fundamentals/host/generic-host) jest zalecany dla wszystkich typów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8c2df-110">Ten artykuł obejmuje hosta sieci Web, który służy do hostowania aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8c2df-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="8c2df-111">W przypadku innych rodzajów aplikacji użyj [hosta ogólnego](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="8c2df-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="8c2df-112">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="8c2df-112">Set up a host</span></span>

<span data-ttu-id="8c2df-113">Utwórz hosta przy użyciu wystąpienia [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="8c2df-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="8c2df-114">Jest to zazwyczaj wykonywane w punkcie wejścia `Main` aplikacji, metoda.</span><span class="sxs-lookup"><span data-stu-id="8c2df-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="8c2df-115">W szablonach projektu `Main` znajduje się w *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="8c2df-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="8c2df-116">Typowa aplikacja wywołuje [CreateDefaultBuilder,](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) aby rozpocząć konfigurowanie hosta:</span><span class="sxs-lookup"><span data-stu-id="8c2df-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

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

<span data-ttu-id="8c2df-117">Kod, który `CreateDefaultBuilder` wywołuje jest `CreateWebHostBuilder`w metodzie o nazwie `Main` , `Run` która oddziela go od kodu w tym wywołuje na builder obiektu.</span><span class="sxs-lookup"><span data-stu-id="8c2df-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="8c2df-118">Ta separacja jest wymagana w przypadku korzystania z [narzędzi Entity Framework Core](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="8c2df-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="8c2df-119">Narzędzia oczekują, aby `CreateWebHostBuilder` znaleźć metodę, którą można wywołać w czasie projektowania, aby skonfigurować hosta bez uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="8c2df-120">Alternatywą jest `IDesignTimeDbContextFactory`wdrożenie .</span><span class="sxs-lookup"><span data-stu-id="8c2df-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="8c2df-121">Aby uzyskać więcej informacji, zobacz [Tworzenie dbcontext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="8c2df-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="8c2df-122">Metoda `CreateDefaultBuilder` wykonuje następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="8c2df-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="8c2df-123">Konfiguruje serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci web przy użyciu dostawców konfiguracji hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="8c2df-124">Aby zapoznać się z domyślnymi <xref:fundamentals/servers/kestrel#kestrel-options>opcjami serwera Kestrel, zobacz .</span><span class="sxs-lookup"><span data-stu-id="8c2df-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="8c2df-125">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na ścieżkę zwróconą przez [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="8c2df-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="8c2df-126">Ładuje [konfigurację hosta](#host-configuration-values) z:</span><span class="sxs-lookup"><span data-stu-id="8c2df-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="8c2df-127">Zmienne środowiskowe poprzedzone `ASPNETCORE_` (na przykład). `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="8c2df-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="8c2df-128">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="8c2df-128">Command-line arguments.</span></span>
* <span data-ttu-id="8c2df-129">Ładuje konfigurację aplikacji w następującej kolejności od:</span><span class="sxs-lookup"><span data-stu-id="8c2df-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="8c2df-130">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8c2df-130">*appsettings.json*.</span></span>
  * <span data-ttu-id="8c2df-131">*appsettings. {Środowisko}.json*.</span><span class="sxs-lookup"><span data-stu-id="8c2df-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="8c2df-132">[Tajny menedżer,](xref:security/app-secrets) gdy aplikacja `Development` działa w środowisku przy użyciu zestawu wejścia.</span><span class="sxs-lookup"><span data-stu-id="8c2df-132">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="8c2df-133">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="8c2df-133">Environment variables.</span></span>
  * <span data-ttu-id="8c2df-134">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="8c2df-134">Command-line arguments.</span></span>
* <span data-ttu-id="8c2df-135">Konfiguruje [rejestrowanie](xref:fundamentals/logging/index) dla danych wyjściowych konsoli i debugowania.</span><span class="sxs-lookup"><span data-stu-id="8c2df-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="8c2df-136">Rejestrowanie obejmuje [reguły filtrowania dziennika](xref:fundamentals/logging/index#log-filtering) określone w sekcji konfiguracji rejestrowania *pliku appsettings.json* lub *appsettings.{ Środowisko}.json.*</span><span class="sxs-lookup"><span data-stu-id="8c2df-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="8c2df-137">Gdy działa za IIS z ASP.NET Core `CreateDefaultBuilder` [Module](xref:host-and-deploy/aspnet-core-module), umożliwia [integrację IIS](xref:host-and-deploy/iis/index), który konfiguruje adres podstawowy aplikacji i portu.</span><span class="sxs-lookup"><span data-stu-id="8c2df-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="8c2df-138">Integracja iis konfiguruje również aplikację do [przechwytywania błędów uruchamiania](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="8c2df-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="8c2df-139">Aby zapoznać się z <xref:host-and-deploy/iis/index#iis-options>domyślnymi opcjami iis, zobacz .</span><span class="sxs-lookup"><span data-stu-id="8c2df-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="8c2df-140">Ustawia [ServiceProviderOptions.ValidateScopes,](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) `true` jeśli środowisko aplikacji jest rozwój.</span><span class="sxs-lookup"><span data-stu-id="8c2df-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="8c2df-141">Aby uzyskać więcej informacji, zobacz [Sprawdzanie poprawności zakresu](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="8c2df-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="8c2df-142">Konfiguracja `CreateDefaultBuilder` zdefiniowana przez może zostać zastąpiona i rozszerzona przez [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)i inne metody i metody rozszerzenia [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="8c2df-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="8c2df-143">Kilka przykładów następują:</span><span class="sxs-lookup"><span data-stu-id="8c2df-143">A few examples follow:</span></span>

* <span data-ttu-id="8c2df-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) służy do `IConfiguration` określenia dodatkowych dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="8c2df-145">Poniższe `ConfigureAppConfiguration` wywołanie dodaje pełnomocnika do uwzględnienia konfiguracji aplikacji w pliku *appsettings.xml.*</span><span class="sxs-lookup"><span data-stu-id="8c2df-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="8c2df-146">`ConfigureAppConfiguration`można nazwać wiele razy.</span><span class="sxs-lookup"><span data-stu-id="8c2df-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="8c2df-147">Należy zauważyć, że ta konfiguracja nie ma zastosowania do hosta (na przykład adresów URL serwera lub środowiska).</span><span class="sxs-lookup"><span data-stu-id="8c2df-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="8c2df-148">Zobacz sekcję [Wartości konfiguracji hosta.](#host-configuration-values)</span><span class="sxs-lookup"><span data-stu-id="8c2df-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="8c2df-149">Następujące `ConfigureLogging` wywołanie dodaje pełnomocnika, aby skonfigurować minimalny poziom rejestrowania[(SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) do [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="8c2df-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="8c2df-150">To ustawienie zastępuje ustawienia w *ustawieniach aplikacji. Development.json* `LogLevel.Debug`( ) i *appsettings. Production.json* `LogLevel.Error`( ) `CreateDefaultBuilder`skonfigurowany przez .</span><span class="sxs-lookup"><span data-stu-id="8c2df-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8c2df-151">`ConfigureLogging`można nazwać wiele razy.</span><span class="sxs-lookup"><span data-stu-id="8c2df-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="8c2df-152">Następujące wywołanie `ConfigureKestrel` zastępowania domyślnego [rozmiaru Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30,000,000 bajtów ustanowionych `CreateDefaultBuilder`podczas konfigurowania Kestrel przez:</span><span class="sxs-lookup"><span data-stu-id="8c2df-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="8c2df-153">Następujące wywołanie [usekestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) zastępuje [domyślny Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30,000,000 bajtów ustanowiony, gdy `CreateDefaultBuilder`Kestrel został skonfigurowany przez:</span><span class="sxs-lookup"><span data-stu-id="8c2df-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="8c2df-154">[Katalog główny zawartości](xref:fundamentals/index#content-root) określa, gdzie host wyszukuje pliki zawartości, takie jak pliki widoku MVC.</span><span class="sxs-lookup"><span data-stu-id="8c2df-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="8c2df-155">Po uruchomieniu aplikacji z folderu głównego projektu folder główny projektu jest używany jako katalog główny zawartości.</span><span class="sxs-lookup"><span data-stu-id="8c2df-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="8c2df-156">Jest to wartość domyślna używana w [programie Visual Studio](https://visualstudio.microsoft.com) i nowych [szablonach dotnet](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="8c2df-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="8c2df-157">Aby uzyskać więcej informacji <xref:fundamentals/configuration/index>na temat konfiguracji aplikacji, zobacz .</span><span class="sxs-lookup"><span data-stu-id="8c2df-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="8c2df-158">Jako alternatywę dla korzystania `CreateDefaultBuilder` z metody statycznej, tworzenie hosta z [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) jest obsługiwane podejście z ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="8c2df-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="8c2df-159">Podczas konfigurowania hosta można [konfigurować](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) i [konfigurować metody usług.](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices)</span><span class="sxs-lookup"><span data-stu-id="8c2df-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="8c2df-160">Jeśli `Startup` klasa jest określona, `Configure` musi zdefiniować metodę.</span><span class="sxs-lookup"><span data-stu-id="8c2df-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="8c2df-161">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="8c2df-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="8c2df-162">Wiele wywołań do `ConfigureServices` dołączenia do siebie.</span><span class="sxs-lookup"><span data-stu-id="8c2df-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="8c2df-163">Wiele wywołań `UseStartup` do `WebHostBuilder` `Configure` lub na zastąp poprzednie ustawienia.</span><span class="sxs-lookup"><span data-stu-id="8c2df-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="8c2df-164">Wartości konfiguracji hosta</span><span class="sxs-lookup"><span data-stu-id="8c2df-164">Host configuration values</span></span>

<span data-ttu-id="8c2df-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) opiera się na następujących podejściach, aby ustawić wartości konfiguracji hosta:</span><span class="sxs-lookup"><span data-stu-id="8c2df-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="8c2df-166">Konfiguracja konstruktora hostów, która `ASPNETCORE_{configurationKey}`zawiera zmienne środowiskowe w formacie .</span><span class="sxs-lookup"><span data-stu-id="8c2df-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="8c2df-167">Na przykład `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="8c2df-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="8c2df-168">Rozszerzenia, takie jak [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) i [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (zobacz sekcję [Zastąp konfigurację).](#override-configuration)</span><span class="sxs-lookup"><span data-stu-id="8c2df-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="8c2df-169">[Użyjstawienie](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) i skojarzony klucz.</span><span class="sxs-lookup"><span data-stu-id="8c2df-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="8c2df-170">Podczas ustawiania `UseSetting`wartości z , wartość jest ustawiana jako ciąg niezależnie od typu.</span><span class="sxs-lookup"><span data-stu-id="8c2df-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="8c2df-171">Host używa dowolnej opcji ustawia wartość last.</span><span class="sxs-lookup"><span data-stu-id="8c2df-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="8c2df-172">Aby uzyskać więcej informacji, zobacz [Zastępowanie konfiguracji](#override-configuration) w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="8c2df-173">Klucz aplikacji (nazwa)</span><span class="sxs-lookup"><span data-stu-id="8c2df-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8c2df-174">Właściwość `IWebHostEnvironment.ApplicationName` jest automatycznie ustawiana, gdy [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) lub [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) jest wywoływana podczas budowy hosta.</span><span class="sxs-lookup"><span data-stu-id="8c2df-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="8c2df-175">Wartość jest ustawiona na nazwę zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="8c2df-176">Aby ustawić wartość jawnie, użyj [WebHostDefaults.ApplicationKey:](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)</span><span class="sxs-lookup"><span data-stu-id="8c2df-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8c2df-177">[Właściwość IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) jest automatycznie ustawiana, gdy [usestartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) lub [configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) jest wywoływana podczas budowy hosta.</span><span class="sxs-lookup"><span data-stu-id="8c2df-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="8c2df-178">Wartość jest ustawiona na nazwę zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="8c2df-179">Aby ustawić wartość jawnie, użyj [WebHostDefaults.ApplicationKey:](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)</span><span class="sxs-lookup"><span data-stu-id="8c2df-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="8c2df-180">**Klucz:** nazwa aplikacji</span><span class="sxs-lookup"><span data-stu-id="8c2df-180">**Key**: applicationName</span></span>  
<span data-ttu-id="8c2df-181">**Typ**: *ciąg znaków*</span><span class="sxs-lookup"><span data-stu-id="8c2df-181">**Type**: *string*</span></span>  
<span data-ttu-id="8c2df-182">**Domyślnie**: Nazwa zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-182">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="8c2df-183">**Zestaw za pomocą**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="8c2df-183">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="8c2df-184">**Zmienna środowiskowa:**`ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="8c2df-184">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="8c2df-185">Przechwytywanie błędów uruchamiania</span><span class="sxs-lookup"><span data-stu-id="8c2df-185">Capture Startup Errors</span></span>

<span data-ttu-id="8c2df-186">To ustawienie steruje przechwytywaniem błędów uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="8c2df-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="8c2df-187">**Klucz:** captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="8c2df-187">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="8c2df-188">**Typ**: *bool* (`true` lub `1`)</span><span class="sxs-lookup"><span data-stu-id="8c2df-188">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="8c2df-189">**Domyślnie:** `false` Domyślnie, chyba że aplikacja działa z Kestrel `true`za IIS, gdzie domyślnie jest .</span><span class="sxs-lookup"><span data-stu-id="8c2df-189">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="8c2df-190">**Zestaw za pomocą**:`CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="8c2df-190">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="8c2df-191">**Zmienna środowiskowa:**`ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="8c2df-191">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="8c2df-192">Kiedy `false`, błędy podczas uruchamiania spowodować wyjście hosta.</span><span class="sxs-lookup"><span data-stu-id="8c2df-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="8c2df-193">Gdy `true`host przechwytuje wyjątki podczas uruchamiania i próbuje uruchomić serwer.</span><span class="sxs-lookup"><span data-stu-id="8c2df-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="8c2df-194">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="8c2df-194">Content root</span></span>

<span data-ttu-id="8c2df-195">To ustawienie określa, gdzie ASP.NET Core rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="8c2df-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="8c2df-196">**Klucz:** contentRoot</span><span class="sxs-lookup"><span data-stu-id="8c2df-196">**Key**: contentRoot</span></span>  
<span data-ttu-id="8c2df-197">**Typ**: *ciąg znaków*</span><span class="sxs-lookup"><span data-stu-id="8c2df-197">**Type**: *string*</span></span>  
<span data-ttu-id="8c2df-198">**Domyślnie**: Domyślnie folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-198">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="8c2df-199">**Zestaw za pomocą**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="8c2df-199">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="8c2df-200">**Zmienna środowiskowa:**`ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="8c2df-200">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="8c2df-201">Katalog główny zawartości jest również używany jako ścieżka podstawowa dla [katalogu głównego sieci Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="8c2df-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="8c2df-202">Jeśli ścieżka katalogu głównego zawartości nie istnieje, uruchomienie hosta nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="8c2df-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="8c2df-203">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="8c2df-203">For more information, see:</span></span>

* [<span data-ttu-id="8c2df-204">Podstawy: Katalog główny treści</span><span class="sxs-lookup"><span data-stu-id="8c2df-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="8c2df-205">Katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="8c2df-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="8c2df-206">Szczegółowe błędy</span><span class="sxs-lookup"><span data-stu-id="8c2df-206">Detailed Errors</span></span>

<span data-ttu-id="8c2df-207">Określa, czy szczegółowe błędy powinny zostać przechwycone.</span><span class="sxs-lookup"><span data-stu-id="8c2df-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="8c2df-208">**Klucz:** detailedErrors</span><span class="sxs-lookup"><span data-stu-id="8c2df-208">**Key**: detailedErrors</span></span>  
<span data-ttu-id="8c2df-209">**Typ**: *bool* (`true` lub `1`)</span><span class="sxs-lookup"><span data-stu-id="8c2df-209">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="8c2df-210">**Domyślnie**: false</span><span class="sxs-lookup"><span data-stu-id="8c2df-210">**Default**: false</span></span>  
<span data-ttu-id="8c2df-211">**Zestaw za pomocą**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="8c2df-211">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="8c2df-212">**Zmienna środowiskowa:**`ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="8c2df-212">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="8c2df-213">Po włączeniu (lub <a href="#environment">Environment</a> gdy środowisko `Development`jest ustawione na), aplikacja przechwytuje szczegółowe wyjątki.</span><span class="sxs-lookup"><span data-stu-id="8c2df-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="8c2df-214">Środowisko</span><span class="sxs-lookup"><span data-stu-id="8c2df-214">Environment</span></span>

<span data-ttu-id="8c2df-215">Ustawia środowisko aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-215">Sets the app's environment.</span></span>

<span data-ttu-id="8c2df-216">**Klucz:** środowisko</span><span class="sxs-lookup"><span data-stu-id="8c2df-216">**Key**: environment</span></span>  
<span data-ttu-id="8c2df-217">**Typ**: *ciąg znaków*</span><span class="sxs-lookup"><span data-stu-id="8c2df-217">**Type**: *string*</span></span>  
<span data-ttu-id="8c2df-218">**Domyślnie**: Produkcja</span><span class="sxs-lookup"><span data-stu-id="8c2df-218">**Default**: Production</span></span>  
<span data-ttu-id="8c2df-219">**Zestaw za pomocą**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="8c2df-219">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="8c2df-220">**Zmienna środowiskowa:**`ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="8c2df-220">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="8c2df-221">Środowisko można ustawić na dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="8c2df-221">The environment can be set to any value.</span></span> <span data-ttu-id="8c2df-222">Wartości zdefiniowane `Development`przez `Staging`ramy `Production`obejmują , i .</span><span class="sxs-lookup"><span data-stu-id="8c2df-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="8c2df-223">W wartościach nie rozróżnia się wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="8c2df-223">Values aren't case sensitive.</span></span> <span data-ttu-id="8c2df-224">Domyślnie *środowisko* jest odczytywane ze zmiennej środowiskowej. `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="8c2df-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="8c2df-225">Podczas korzystania z [programu Visual Studio](https://visualstudio.microsoft.com)zmienne środowiskowe mogą być ustawiane w pliku *launchSettings.json.*</span><span class="sxs-lookup"><span data-stu-id="8c2df-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="8c2df-226">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="8c2df-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="8c2df-227">Hosting zestawów startowych</span><span class="sxs-lookup"><span data-stu-id="8c2df-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="8c2df-228">Ustawia zestawy uruchamiania hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="8c2df-229">**Klucz:** hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="8c2df-229">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="8c2df-230">**Typ**: *ciąg znaków*</span><span class="sxs-lookup"><span data-stu-id="8c2df-230">**Type**: *string*</span></span>  
<span data-ttu-id="8c2df-231">**Domyślnie**: Pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="8c2df-231">**Default**: Empty string</span></span>  
<span data-ttu-id="8c2df-232">**Zestaw za pomocą**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="8c2df-232">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="8c2df-233">**Zmienna środowiskowa:**`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="8c2df-233">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="8c2df-234">Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do załadowania podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="8c2df-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="8c2df-235">Mimo że wartość konfiguracji domyślnie pusty ciąg, zestawy uruchamiania hostingu zawsze zawierają zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="8c2df-236">Po udostępnieniu zestawów uruchamiania hostingu są one dodawane do zestawu aplikacji do ładowania, gdy aplikacja tworzy swoje typowe usługi podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="8c2df-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="8c2df-237">HTTPS Port</span><span class="sxs-lookup"><span data-stu-id="8c2df-237">HTTPS Port</span></span>

<span data-ttu-id="8c2df-238">Ustaw port przekierowania HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8c2df-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="8c2df-239">Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="8c2df-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="8c2df-240">**Klucz:** https_port **Typ:** *ciąg*
**Domyślny:** Wartość domyślna nie jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="8c2df-240">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="8c2df-241">**Zestaw**za `UseSetting` 
pomocą : **Zmienna środowiskowa**:`ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="8c2df-241">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="8c2df-242">Zestawy wykluczeń uruchamiania hostingu</span><span class="sxs-lookup"><span data-stu-id="8c2df-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="8c2df-243">Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do wykluczenia podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="8c2df-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="8c2df-244">**Klucz:** hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="8c2df-244">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="8c2df-245">**Typ**: *ciąg znaków*</span><span class="sxs-lookup"><span data-stu-id="8c2df-245">**Type**: *string*</span></span>  
<span data-ttu-id="8c2df-246">**Domyślnie**: Pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="8c2df-246">**Default**: Empty string</span></span>  
<span data-ttu-id="8c2df-247">**Zestaw za pomocą**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="8c2df-247">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="8c2df-248">**Zmienna środowiskowa:**`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="8c2df-248">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="8c2df-249">Preferuj hostowanie adresów URL</span><span class="sxs-lookup"><span data-stu-id="8c2df-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="8c2df-250">Wskazuje, czy host powinien nasłuchiwania na `WebHostBuilder` adresach URL skonfigurowanych `IServer` z zamiast tych skonfigurowanych z implementacją.</span><span class="sxs-lookup"><span data-stu-id="8c2df-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="8c2df-251">**Klucz**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="8c2df-251">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="8c2df-252">**Typ**: *bool* (`true` lub `1`)</span><span class="sxs-lookup"><span data-stu-id="8c2df-252">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="8c2df-253">**Domyślnie**: prawda</span><span class="sxs-lookup"><span data-stu-id="8c2df-253">**Default**: true</span></span>  
<span data-ttu-id="8c2df-254">**Zestaw za pomocą**:`PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="8c2df-254">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="8c2df-255">**Zmienna środowiskowa:**`ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="8c2df-255">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="8c2df-256">Zapobieganie uruchamianiu hostingu</span><span class="sxs-lookup"><span data-stu-id="8c2df-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="8c2df-257">Zapobiega automatycznemu obciążaniu zestawów uruchamiania hostingu, w tym zestawów uruchamiania hostingu skonfigurowanych przez zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="8c2df-258">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8c2df-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="8c2df-259">**Klucz:** preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="8c2df-259">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="8c2df-260">**Typ**: *bool* (`true` lub `1`)</span><span class="sxs-lookup"><span data-stu-id="8c2df-260">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="8c2df-261">**Domyślnie**: false</span><span class="sxs-lookup"><span data-stu-id="8c2df-261">**Default**: false</span></span>  
<span data-ttu-id="8c2df-262">**Zestaw za pomocą**:`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="8c2df-262">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="8c2df-263">**Zmienna środowiskowa:**`ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="8c2df-263">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="8c2df-264">Adresy URL serwera</span><span class="sxs-lookup"><span data-stu-id="8c2df-264">Server URLs</span></span>

<span data-ttu-id="8c2df-265">Wskazuje adresy IP lub adresy hostów z portami i protokołami, na których serwer powinien nasłuchiwania żądań.</span><span class="sxs-lookup"><span data-stu-id="8c2df-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="8c2df-266">**Klucz**: adresy URL</span><span class="sxs-lookup"><span data-stu-id="8c2df-266">**Key**: urls</span></span>  
<span data-ttu-id="8c2df-267">**Typ**: *ciąg znaków*</span><span class="sxs-lookup"><span data-stu-id="8c2df-267">**Type**: *string*</span></span>  
<span data-ttu-id="8c2df-268">**Domyślnie**:http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="8c2df-268">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="8c2df-269">**Zestaw za pomocą**:`UseUrls`</span><span class="sxs-lookup"><span data-stu-id="8c2df-269">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="8c2df-270">**Zmienna środowiskowa:**`ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="8c2df-270">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="8c2df-271">Zestaw do rozdzielonego średnikiem (;) lista prefiksów adresów URL, na które serwer powinien odpowiedzieć.</span><span class="sxs-lookup"><span data-stu-id="8c2df-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="8c2df-272">Na przykład `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="8c2df-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="8c2df-273">Użyj\*" ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwach hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="8c2df-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="8c2df-274">Protokół (`http://` `https://`lub ) musi być dołączony do każdego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="8c2df-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="8c2df-275">Obsługiwane formaty różnią się w zależności od serwera.</span><span class="sxs-lookup"><span data-stu-id="8c2df-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="8c2df-276">Pustułka ma własny interfejs API konfiguracji punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="8c2df-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="8c2df-277">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8c2df-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="8c2df-278">Limit czasu zamknięcia</span><span class="sxs-lookup"><span data-stu-id="8c2df-278">Shutdown Timeout</span></span>

<span data-ttu-id="8c2df-279">Określa czas oczekiwania na zamknięcie hosta sieci Web.</span><span class="sxs-lookup"><span data-stu-id="8c2df-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="8c2df-280">**Klucz:** shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="8c2df-280">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="8c2df-281">**Typ**: *int*</span><span class="sxs-lookup"><span data-stu-id="8c2df-281">**Type**: *int*</span></span>  
<span data-ttu-id="8c2df-282">**Domyślnie**: 5</span><span class="sxs-lookup"><span data-stu-id="8c2df-282">**Default**: 5</span></span>  
<span data-ttu-id="8c2df-283">**Zestaw za pomocą**:`UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="8c2df-283">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="8c2df-284">**Zmienna środowiskowa:**`ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="8c2df-284">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="8c2df-285">Chociaż klucz akceptuje *int* `UseSetting` z (na `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`przykład), [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) metoda rozszerzenia trwa [TimeSpan](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="8c2df-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="8c2df-286">W okresie limitu czasu hosting:</span><span class="sxs-lookup"><span data-stu-id="8c2df-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="8c2df-287">Wyzwala [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="8c2df-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="8c2df-288">Próbuje zatrzymać hostowane usługi, rejestrując wszelkie błędy dla usług, które nie mogą się zatrzymać.</span><span class="sxs-lookup"><span data-stu-id="8c2df-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="8c2df-289">Jeśli limit czasu wygaśnie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="8c2df-290">Usługi zatrzymują się nawet wtedy, gdy nie zakończyły przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="8c2df-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="8c2df-291">Jeśli usługi wymagają dodatkowego czasu, aby zatrzymać, zwiększyć limit czasu.</span><span class="sxs-lookup"><span data-stu-id="8c2df-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="8c2df-292">Zestaw startowy</span><span class="sxs-lookup"><span data-stu-id="8c2df-292">Startup Assembly</span></span>

<span data-ttu-id="8c2df-293">Określa zestaw do wyszukiwania `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="8c2df-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="8c2df-294">**Klucz:** startupAssembly</span><span class="sxs-lookup"><span data-stu-id="8c2df-294">**Key**: startupAssembly</span></span>  
<span data-ttu-id="8c2df-295">**Typ**: *ciąg znaków*</span><span class="sxs-lookup"><span data-stu-id="8c2df-295">**Type**: *string*</span></span>  
<span data-ttu-id="8c2df-296">**Domyślnie**: Zespół aplikacji</span><span class="sxs-lookup"><span data-stu-id="8c2df-296">**Default**: The app's assembly</span></span>  
<span data-ttu-id="8c2df-297">**Zestaw za pomocą**:`UseStartup`</span><span class="sxs-lookup"><span data-stu-id="8c2df-297">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="8c2df-298">**Zmienna środowiskowa:**`ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="8c2df-298">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="8c2df-299">Można odwoływać`string`się do`TStartup`złożenia według nazwy ( ) lub typu ( ) .</span><span class="sxs-lookup"><span data-stu-id="8c2df-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="8c2df-300">Jeśli `UseStartup` wywoływanych jest wiele metod, pierwszeństwo ma ostatnia.</span><span class="sxs-lookup"><span data-stu-id="8c2df-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="8c2df-301">Katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="8c2df-301">Web root</span></span>

<span data-ttu-id="8c2df-302">Ustawia ścieżkę względną do zasobów statycznych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="8c2df-303">**Klucz**: webroot</span><span class="sxs-lookup"><span data-stu-id="8c2df-303">**Key**: webroot</span></span>  
<span data-ttu-id="8c2df-304">**Typ**: *ciąg znaków*</span><span class="sxs-lookup"><span data-stu-id="8c2df-304">**Type**: *string*</span></span>  
<span data-ttu-id="8c2df-305">**Domyślnie**: `wwwroot`Wartość domyślna to .</span><span class="sxs-lookup"><span data-stu-id="8c2df-305">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="8c2df-306">Musi istnieć ścieżka do *{content root}/wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="8c2df-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="8c2df-307">Jeśli ścieżka nie istnieje, używany jest dostawca plików bez operacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="8c2df-308">**Zestaw za pomocą**:`UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="8c2df-308">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="8c2df-309">**Zmienna środowiskowa:**`ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="8c2df-309">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="8c2df-310">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="8c2df-310">For more information, see:</span></span>

* [<span data-ttu-id="8c2df-311">Podstawy: Korzeń sieci Web</span><span class="sxs-lookup"><span data-stu-id="8c2df-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="8c2df-312">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="8c2df-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="8c2df-313">Zastępowanie konfiguracji</span><span class="sxs-lookup"><span data-stu-id="8c2df-313">Override configuration</span></span>

<span data-ttu-id="8c2df-314">Konfigurowanie hosta sieci Web za pomocą [funkcji Konfiguracji.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="8c2df-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="8c2df-315">W poniższym przykładzie konfiguracja hosta jest opcjonalnie określona w pliku *hostsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="8c2df-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="8c2df-316">Każda konfiguracja załadowana z pliku *hostsettings.json* może zostać zastąpiona argumentami wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="8c2df-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="8c2df-317">Wbudowana konfiguracja `config`(w) służy do konfigurowania hosta za pomocą [funkcji UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="8c2df-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="8c2df-318">`IWebHostBuilder`konfiguracja jest dodawana do konfiguracji aplikacji, ale&mdash; `ConfigureAppConfiguration` odwrotność nie `IWebHostBuilder` jest prawdziwa, nie wpływa na konfigurację.</span><span class="sxs-lookup"><span data-stu-id="8c2df-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="8c2df-319">Zastępowanie konfiguracji dostarczanej `UseUrls` przez *hostettings.json* config first, command-line argument config second:</span><span class="sxs-lookup"><span data-stu-id="8c2df-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

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

<span data-ttu-id="8c2df-320">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8c2df-320">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="8c2df-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) kopiuje tylko `IConfiguration` klucze z dostarczonej do konfiguracji konstruktora hosta.</span><span class="sxs-lookup"><span data-stu-id="8c2df-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="8c2df-322">W związku `reloadOnChange: true` z tym ustawienie plików ustawień JSON, INI i XML nie ma wpływu.</span><span class="sxs-lookup"><span data-stu-id="8c2df-322">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="8c2df-323">Aby określić hosta uruchamianego na określonym adresie URL, żądana wartość może być przekazywana z wiersza polecenia podczas wykonywania [dotnet run](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="8c2df-323">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="8c2df-324">Argument wiersza polecenia zastępuje `urls` wartość z pliku *hostsettings.json,* a serwer nasłuchuje na porcie 8080:</span><span class="sxs-lookup"><span data-stu-id="8c2df-324">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="8c2df-325">Zarządzanie hostem</span><span class="sxs-lookup"><span data-stu-id="8c2df-325">Manage the host</span></span>

<span data-ttu-id="8c2df-326">**Run**</span><span class="sxs-lookup"><span data-stu-id="8c2df-326">**Run**</span></span>

<span data-ttu-id="8c2df-327">Metoda `Run` uruchamia aplikację sieci web i blokuje wątek wywołujący, dopóki host nie zostanie zamknięty:</span><span class="sxs-lookup"><span data-stu-id="8c2df-327">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="8c2df-328">**Początek**</span><span class="sxs-lookup"><span data-stu-id="8c2df-328">**Start**</span></span>

<span data-ttu-id="8c2df-329">Uruchom hosta w sposób nieblokujący, wywołując jego `Start` metodę:</span><span class="sxs-lookup"><span data-stu-id="8c2df-329">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="8c2df-330">Jeśli lista adresów URL jest `Start` przekazywana do metody, nasłuchuje na określonych adresach URL:</span><span class="sxs-lookup"><span data-stu-id="8c2df-330">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

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

<span data-ttu-id="8c2df-331">Aplikacja może zainicjować i uruchomić nowego hosta przy użyciu `CreateDefaultBuilder` wstępnie skonfigurowanych ustawień domyślnych przy użyciu statycznej metody wygody.</span><span class="sxs-lookup"><span data-stu-id="8c2df-331">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="8c2df-332">Metody te uruchamiają serwer bez wyjścia konsoli i z [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) czekać na przerwę (Ctrl-C/SIGINT lub SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="8c2df-332">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="8c2df-333">**Start(Aplikacja RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="8c2df-333">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="8c2df-334">Zacznij `RequestDelegate`od:</span><span class="sxs-lookup"><span data-stu-id="8c2df-334">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="8c2df-335">Złożyć wniosek w `http://localhost:5000` przeglądarce, aby otrzymać odpowiedź "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="8c2df-335">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="8c2df-336">`WaitForShutdown`do momentu wydania przerwy (Ctrl-C/SIGINT lub SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="8c2df-336">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="8c2df-337">Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na wyjście naciśnięcia klawisza.</span><span class="sxs-lookup"><span data-stu-id="8c2df-337">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="8c2df-338">**Start(adres URL ciągu, aplikacja RequestDelegate)**</span><span class="sxs-lookup"><span data-stu-id="8c2df-338">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="8c2df-339">Zacznij od adresu `RequestDelegate`URL i:</span><span class="sxs-lookup"><span data-stu-id="8c2df-339">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="8c2df-340">Daje taki sam wynik jak **Start(RequestDelegate app),** z `http://localhost:8080`wyjątkiem aplikacji odpowiada na .</span><span class="sxs-lookup"><span data-stu-id="8c2df-340">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="8c2df-341">**Start(Akcja\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="8c2df-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="8c2df-342">Użyj wystąpienia `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) do używania oprogramowania pośredniczącego routingu:</span><span class="sxs-lookup"><span data-stu-id="8c2df-342">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

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

<span data-ttu-id="8c2df-343">Użyj następujących żądań przeglądarki w przykładzie:</span><span class="sxs-lookup"><span data-stu-id="8c2df-343">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="8c2df-344">Żądanie</span><span class="sxs-lookup"><span data-stu-id="8c2df-344">Request</span></span>                                    | <span data-ttu-id="8c2df-345">Odpowiedź</span><span class="sxs-lookup"><span data-stu-id="8c2df-345">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="8c2df-346">Cześć, Martin!</span><span class="sxs-lookup"><span data-stu-id="8c2df-346">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="8c2df-347">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="8c2df-347">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="8c2df-348">Zgłasza wyjątek z ciągiem "ooops!"</span><span class="sxs-lookup"><span data-stu-id="8c2df-348">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="8c2df-349">Zgłasza wyjątek z napisem "Uh oh!"</span><span class="sxs-lookup"><span data-stu-id="8c2df-349">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="8c2df-350">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="8c2df-350">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="8c2df-351">Hello World!</span><span class="sxs-lookup"><span data-stu-id="8c2df-351">Hello World!</span></span>                             |

<span data-ttu-id="8c2df-352">`WaitForShutdown`do momentu wydania przerwy (Ctrl-C/SIGINT lub SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="8c2df-352">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="8c2df-353">Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na wyjście naciśnięcia klawisza.</span><span class="sxs-lookup"><span data-stu-id="8c2df-353">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="8c2df-354">**Start(adres URL\<ciągu, Action IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="8c2df-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="8c2df-355">Użyj adresu URL i `IRouteBuilder`wystąpienia:</span><span class="sxs-lookup"><span data-stu-id="8c2df-355">Use a URL and an instance of `IRouteBuilder`:</span></span>

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

<span data-ttu-id="8c2df-356">Daje taki sam wynik jak **Start(Action\<IRouteBuilder> routeBuilder),** z `http://localhost:8080`wyjątkiem aplikacji odpowiada na .</span><span class="sxs-lookup"><span data-stu-id="8c2df-356">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="8c2df-357">**StartWith(Aplikacja\<Action IApplicationBuilder>)**</span><span class="sxs-lookup"><span data-stu-id="8c2df-357">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="8c2df-358">Podaj pełnomocnika, `IApplicationBuilder`aby skonfigurować:</span><span class="sxs-lookup"><span data-stu-id="8c2df-358">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="8c2df-359">Złożyć wniosek w `http://localhost:5000` przeglądarce, aby otrzymać odpowiedź "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="8c2df-359">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="8c2df-360">`WaitForShutdown`do momentu wydania przerwy (Ctrl-C/SIGINT lub SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="8c2df-360">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="8c2df-361">Aplikacja wyświetla `Console.WriteLine` komunikat i czeka na wyjście naciśnięcia klawisza.</span><span class="sxs-lookup"><span data-stu-id="8c2df-361">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="8c2df-362">**StartWith(adres URL\<ciągu, Action IApplicationBuilder> aplikacji)**</span><span class="sxs-lookup"><span data-stu-id="8c2df-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="8c2df-363">Podaj adres URL i `IApplicationBuilder`pełnomocnika, aby skonfigurować :</span><span class="sxs-lookup"><span data-stu-id="8c2df-363">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="8c2df-364">Daje taki sam wynik jak **StartWith(Action\<IApplicationBuilder> app)**, `http://localhost:8080`z wyjątkiem aplikacji odpowiada na .</span><span class="sxs-lookup"><span data-stu-id="8c2df-364">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="8c2df-365">Interfejs IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="8c2df-365">IWebHostEnvironment interface</span></span>

<span data-ttu-id="8c2df-366">Interfejs `IWebHostEnvironment` zawiera informacje o środowisku hostingu sieci Web aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-366">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="8c2df-367">Użyj [iniekcji konstruktora,](xref:fundamentals/dependency-injection) `IWebHostEnvironment` aby uzyskać w celu wykorzystania jego właściwości i metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="8c2df-367">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="8c2df-368">[Podejście oparte na konwencji](xref:fundamentals/environments#environment-based-startup-class-and-methods) może służyć do konfigurowania aplikacji podczas uruchamiania na podstawie środowiska.</span><span class="sxs-lookup"><span data-stu-id="8c2df-368">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="8c2df-369">Alternatywnie, wstrzyknąć `IWebHostEnvironment` do konstruktora `Startup` do użytku w: `ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="8c2df-369">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="8c2df-370">Oprócz metody `IsDevelopment` rozszerzenia, `IWebHostEnvironment` oferuje `IsStaging` `IsProduction`, `IsEnvironment(string environmentName)` i metody.</span><span class="sxs-lookup"><span data-stu-id="8c2df-370">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="8c2df-371">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="8c2df-371">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8c2df-372">Usługa `IWebHostEnvironment` może być również wstrzyknięta bezpośrednio do `Configure` metody konfigurowania rurociągu przetwarzania:</span><span class="sxs-lookup"><span data-stu-id="8c2df-372">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="8c2df-373">`IWebHostEnvironment`można wstrzyknąć do `Invoke` metody podczas tworzenia niestandardowego [oprogramowania pośredniczącego:](xref:fundamentals/middleware/write)</span><span class="sxs-lookup"><span data-stu-id="8c2df-373">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="8c2df-374">Interfejs IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="8c2df-374">IHostingEnvironment interface</span></span>

<span data-ttu-id="8c2df-375">[Interfejs IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) zawiera informacje o środowisku hostingu sieci Web aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-375">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="8c2df-376">Użyj [iniekcji konstruktora,](xref:fundamentals/dependency-injection) `IHostingEnvironment` aby uzyskać w celu wykorzystania jego właściwości i metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="8c2df-376">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="8c2df-377">[Podejście oparte na konwencji](xref:fundamentals/environments#environment-based-startup-class-and-methods) może służyć do konfigurowania aplikacji podczas uruchamiania na podstawie środowiska.</span><span class="sxs-lookup"><span data-stu-id="8c2df-377">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="8c2df-378">Alternatywnie, wstrzyknąć `IHostingEnvironment` do konstruktora `Startup` do użytku w: `ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="8c2df-378">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="8c2df-379">Oprócz metody `IsDevelopment` rozszerzenia, `IHostingEnvironment` oferuje `IsStaging` `IsProduction`, `IsEnvironment(string environmentName)` i metody.</span><span class="sxs-lookup"><span data-stu-id="8c2df-379">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="8c2df-380">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="8c2df-380">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8c2df-381">Usługa `IHostingEnvironment` może być również wstrzyknięta bezpośrednio do `Configure` metody konfigurowania rurociągu przetwarzania:</span><span class="sxs-lookup"><span data-stu-id="8c2df-381">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="8c2df-382">`IHostingEnvironment`można wstrzyknąć do `Invoke` metody podczas tworzenia niestandardowego [oprogramowania pośredniczącego:](xref:fundamentals/middleware/write)</span><span class="sxs-lookup"><span data-stu-id="8c2df-382">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="8c2df-383">Interfejs IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="8c2df-383">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="8c2df-384">`IHostApplicationLifetime`umożliwia działania po starcie i zamknięciu systemu.</span><span class="sxs-lookup"><span data-stu-id="8c2df-384">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="8c2df-385">Trzy właściwości interfejsu są tokeny anulowania `Action` używane do rejestrowania metod, które definiują zdarzenia uruchamiania i zamykania.</span><span class="sxs-lookup"><span data-stu-id="8c2df-385">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="8c2df-386">Token anulowania</span><span class="sxs-lookup"><span data-stu-id="8c2df-386">Cancellation Token</span></span>    | <span data-ttu-id="8c2df-387">Wyzwalane podczas&#8230;</span><span class="sxs-lookup"><span data-stu-id="8c2df-387">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="8c2df-388">Gospodarz w pełni się rozpoczął.</span><span class="sxs-lookup"><span data-stu-id="8c2df-388">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="8c2df-389">Host kończy wdzięczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="8c2df-389">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="8c2df-390">Wszystkie wnioski powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="8c2df-390">All requests should be processed.</span></span> <span data-ttu-id="8c2df-391">Zamykanie bloków, dopóki to zdarzenie nie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="8c2df-391">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="8c2df-392">Host wykonuje wdzięku zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="8c2df-392">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="8c2df-393">Żądania mogą nadal być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="8c2df-393">Requests may still be processing.</span></span> <span data-ttu-id="8c2df-394">Zamykanie bloków, dopóki to zdarzenie nie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="8c2df-394">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="8c2df-395">`StopApplication`żąda rozwiązania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-395">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="8c2df-396">Następująca klasa `StopApplication` używa do bezpiecznie zamknąć aplikację, `Shutdown` gdy wywoływana jest metoda klasy:</span><span class="sxs-lookup"><span data-stu-id="8c2df-396">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="8c2df-397">Interfejs IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="8c2df-397">IApplicationLifetime interface</span></span>

<span data-ttu-id="8c2df-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) pozwala na działania po starcie i zamknięciu.</span><span class="sxs-lookup"><span data-stu-id="8c2df-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="8c2df-399">Trzy właściwości interfejsu są tokeny anulowania `Action` używane do rejestrowania metod, które definiują zdarzenia uruchamiania i zamykania.</span><span class="sxs-lookup"><span data-stu-id="8c2df-399">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="8c2df-400">Token anulowania</span><span class="sxs-lookup"><span data-stu-id="8c2df-400">Cancellation Token</span></span>    | <span data-ttu-id="8c2df-401">Wyzwalane podczas&#8230;</span><span class="sxs-lookup"><span data-stu-id="8c2df-401">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="8c2df-402">Aplikacja Rozpoczęta</span><span class="sxs-lookup"><span data-stu-id="8c2df-402">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="8c2df-403">Gospodarz w pełni się rozpoczął.</span><span class="sxs-lookup"><span data-stu-id="8c2df-403">The host has fully started.</span></span> |
| [<span data-ttu-id="8c2df-404">Aplikacje Zwieńczone</span><span class="sxs-lookup"><span data-stu-id="8c2df-404">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="8c2df-405">Host kończy wdzięczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="8c2df-405">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="8c2df-406">Wszystkie wnioski powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="8c2df-406">All requests should be processed.</span></span> <span data-ttu-id="8c2df-407">Zamykanie bloków, dopóki to zdarzenie nie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="8c2df-407">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="8c2df-408">Stopowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="8c2df-408">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="8c2df-409">Host wykonuje wdzięku zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="8c2df-409">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="8c2df-410">Żądania mogą nadal być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="8c2df-410">Requests may still be processing.</span></span> <span data-ttu-id="8c2df-411">Zamykanie bloków, dopóki to zdarzenie nie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="8c2df-411">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="8c2df-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) żąda zakończenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="8c2df-413">Następująca klasa `StopApplication` używa do bezpiecznie zamknąć aplikację, `Shutdown` gdy wywoływana jest metoda klasy:</span><span class="sxs-lookup"><span data-stu-id="8c2df-413">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="8c2df-414">Sprawdzanie poprawności zakresu</span><span class="sxs-lookup"><span data-stu-id="8c2df-414">Scope validation</span></span>

<span data-ttu-id="8c2df-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) ustawia [ServiceProviderOptions.ValidateScopes,](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) `true` jeśli środowisko aplikacji jest rozwój.</span><span class="sxs-lookup"><span data-stu-id="8c2df-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="8c2df-416">Gdy `ValidateScopes` jest `true`ustawiona na , domyślny dostawca usług wykonuje kontrole, aby sprawdzić, czy:</span><span class="sxs-lookup"><span data-stu-id="8c2df-416">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="8c2df-417">Usługi o określonym zakresie nie są bezpośrednio lub pośrednio rozpoznawane przez głównego dostawcę usług.</span><span class="sxs-lookup"><span data-stu-id="8c2df-417">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="8c2df-418">Usługi o określonym zakresie nie są bezpośrednio lub pośrednio wstrzykuje się do singletons.</span><span class="sxs-lookup"><span data-stu-id="8c2df-418">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="8c2df-419">Główny dostawca usług jest tworzony, gdy wywoływany jest [program BuildServiceProvider.](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider)</span><span class="sxs-lookup"><span data-stu-id="8c2df-419">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="8c2df-420">Okres istnienia głównego dostawcy usług odpowiada okresowi istnienia aplikacji/serwera, gdy dostawca uruchamia się z aplikacją i jest usuwany po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="8c2df-420">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="8c2df-421">Usługi o określonym zakresie są usuwane przez kontener, który je utworzył.</span><span class="sxs-lookup"><span data-stu-id="8c2df-421">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="8c2df-422">Jeśli usługa o określonym zakresie jest tworzona w kontenerze głównym, okres istnienia usługi jest skutecznie promowany do singleton, ponieważ jest usuwany tylko przez kontener główny, gdy aplikacja/serwer jest zamknięty.</span><span class="sxs-lookup"><span data-stu-id="8c2df-422">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="8c2df-423">Sprawdzanie poprawności zakresów usługi łapie te sytuacje, gdy `BuildServiceProvider` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="8c2df-423">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="8c2df-424">Aby zawsze sprawdzać poprawność zakresów, w tym w środowisku produkcyjnym, należy skonfigurować [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) z [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) w konstruktorze hosta:</span><span class="sxs-lookup"><span data-stu-id="8c2df-424">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="8c2df-425">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="8c2df-425">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
