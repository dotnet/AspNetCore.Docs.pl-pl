---
title: Host ogólny platformy .NET
author: rick-anderson
description: Dowiedz się więcej o hoście ogólnym .NET Core, który jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: b528a33fa52bfe56faaf9f3ff8c7e43db0d4e184
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384034"
---
# <a name="net-generic-host"></a><span data-ttu-id="776d8-103">Host ogólny platformy .NET</span><span class="sxs-lookup"><span data-stu-id="776d8-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="776d8-104">Szablony ASP.NET Core tworzą hosta ogólnego .NET<xref:Microsoft.Extensions.Hosting.HostBuilder>Core ( ).</span><span class="sxs-lookup"><span data-stu-id="776d8-104">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="776d8-105">Definicja hosta</span><span class="sxs-lookup"><span data-stu-id="776d8-105">Host definition</span></span>

<span data-ttu-id="776d8-106">*Host* jest obiektem, który hermetyzuje zasoby aplikacji, takie jak:</span><span class="sxs-lookup"><span data-stu-id="776d8-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="776d8-107">Wstrzyknięcie zależności (DI)</span><span class="sxs-lookup"><span data-stu-id="776d8-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="776d8-108">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="776d8-108">Logging</span></span>
* <span data-ttu-id="776d8-109">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="776d8-109">Configuration</span></span>
* <span data-ttu-id="776d8-110">`IHostedService`Implementacje</span><span class="sxs-lookup"><span data-stu-id="776d8-110">`IHostedService` implementations</span></span>

<span data-ttu-id="776d8-111">Po uruchomieniu hosta wywołuje `IHostedService.StartAsync` każdą <xref:Microsoft.Extensions.Hosting.IHostedService> implementację, która znajduje się w kontenerze DI.</span><span class="sxs-lookup"><span data-stu-id="776d8-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="776d8-112">W aplikacji sieci web `IHostedService` jedną z implementacji jest usługa sieci web, która uruchamia [implementację serwera HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="776d8-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="776d8-113">Głównym powodem włączenia wszystkich współzależnych zasobów aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: kontrola nad uruchamianiem aplikacji i wdzięczne zamykanie.</span><span class="sxs-lookup"><span data-stu-id="776d8-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="776d8-114">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="776d8-114">Set up a host</span></span>

<span data-ttu-id="776d8-115">Host jest zazwyczaj skonfigurowany, zbudowany i uruchamiany `Program` przez kod w klasie.</span><span class="sxs-lookup"><span data-stu-id="776d8-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="776d8-116">Metoda: `Main`</span><span class="sxs-lookup"><span data-stu-id="776d8-116">The `Main` method:</span></span>

* <span data-ttu-id="776d8-117">Wywołuje `CreateHostBuilder` metodę tworzenia i konfigurowania obiektu konstruktora.</span><span class="sxs-lookup"><span data-stu-id="776d8-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="776d8-118">`Build` Wywołania `Run` i metody na obiekcie konstruktora.</span><span class="sxs-lookup"><span data-stu-id="776d8-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="776d8-119">Szablony sieci Web ASP.NET Core generują następujący kod w celu utworzenia hosta:</span><span class="sxs-lookup"><span data-stu-id="776d8-119">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="776d8-120">Poniższy kod tworzy obciążenie inne `IHostedService` niż HTTP z implementacją dodaną do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="776d8-120">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="776d8-121">W przypadku obciążenia `Main` HTTP metoda jest `CreateHostBuilder` `ConfigureWebHostDefaults`taka sama, ale wywołuje:</span><span class="sxs-lookup"><span data-stu-id="776d8-121">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="776d8-122">Jeśli aplikacja używa entity framework core, nie należy zmieniać `CreateHostBuilder` nazwę lub podpis metody.</span><span class="sxs-lookup"><span data-stu-id="776d8-122">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="776d8-123">[Narzędzia Entity Framework Core](/ef/core/miscellaneous/cli/) oczekują, aby znaleźć `CreateHostBuilder` metodę, która konfiguruje hosta bez uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-123">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="776d8-124">Aby uzyskać więcej informacji, zobacz [Tworzenie dbcontext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="776d8-124">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="776d8-125">Domyślne ustawienia konstruktora</span><span class="sxs-lookup"><span data-stu-id="776d8-125">Default builder settings</span></span>

<span data-ttu-id="776d8-126">Metoda: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="776d8-126">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="776d8-127">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na <xref:System.IO.Directory.GetCurrentDirectory*>ścieżkę zwróconą przez program .</span><span class="sxs-lookup"><span data-stu-id="776d8-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="776d8-128">Ładuje konfigurację hosta z:</span><span class="sxs-lookup"><span data-stu-id="776d8-128">Loads host configuration from:</span></span>
  * <span data-ttu-id="776d8-129">Zmienne środowiskowe poprzedzone . `DOTNET_`</span><span class="sxs-lookup"><span data-stu-id="776d8-129">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="776d8-130">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="776d8-130">Command-line arguments.</span></span>
* <span data-ttu-id="776d8-131">Ładuje konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="776d8-131">Loads app configuration from:</span></span>
  * <span data-ttu-id="776d8-132">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="776d8-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="776d8-133">*appsettings. {Środowisko}.json*.</span><span class="sxs-lookup"><span data-stu-id="776d8-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="776d8-134">[Tajny menedżer,](xref:security/app-secrets) gdy aplikacja `Development` działa w środowisku.</span><span class="sxs-lookup"><span data-stu-id="776d8-134">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="776d8-135">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="776d8-135">Environment variables.</span></span>
  * <span data-ttu-id="776d8-136">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="776d8-136">Command-line arguments.</span></span>
* <span data-ttu-id="776d8-137">Dodaje następujących dostawców [rejestrowania:](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="776d8-137">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="776d8-138">Konsola</span><span class="sxs-lookup"><span data-stu-id="776d8-138">Console</span></span>
  * <span data-ttu-id="776d8-139">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="776d8-139">Debug</span></span>
  * <span data-ttu-id="776d8-140">EventSource</span><span class="sxs-lookup"><span data-stu-id="776d8-140">EventSource</span></span>
  * <span data-ttu-id="776d8-141">EventLog (tylko w przypadku pracy w systemie Windows)</span><span class="sxs-lookup"><span data-stu-id="776d8-141">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="776d8-142">Włącza [sprawdzanie poprawności zakresu](xref:fundamentals/dependency-injection#scope-validation) i sprawdzania poprawności [zależności,](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) gdy środowisko jest rozwój.</span><span class="sxs-lookup"><span data-stu-id="776d8-142">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="776d8-143">Metoda: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="776d8-143">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="776d8-144">Ładuje konfigurację hosta ze zmiennych środowiskowych poprzedzonych programem `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="776d8-144">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="776d8-145">Ustawia serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci web i konfiguruje go przy użyciu dostawców konfiguracji hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-145">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="776d8-146">Aby zapoznać się z domyślnymi <xref:fundamentals/servers/kestrel#kestrel-options>opcjami serwera Kestrel, zobacz .</span><span class="sxs-lookup"><span data-stu-id="776d8-146">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="776d8-147">Dodaje [oprogramowanie pośredniczące filtrowania hosta](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="776d8-147">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="776d8-148">Dodaje [oprogramowanie pośredniczące nagłówków przesyłanych dalej,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest `true`równe .</span><span class="sxs-lookup"><span data-stu-id="776d8-148">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="776d8-149">Umożliwia integrację z usługiami IIS.</span><span class="sxs-lookup"><span data-stu-id="776d8-149">Enables IIS integration.</span></span> <span data-ttu-id="776d8-150">Aby zapoznać się z <xref:host-and-deploy/iis/index#iis-options>domyślnymi opcjami iis, zobacz .</span><span class="sxs-lookup"><span data-stu-id="776d8-150">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="776d8-151">W sekcji [Ustawienia dla wszystkich typów aplikacji](#settings-for-all-app-types) i Ustawienia aplikacji sieci [Web](#settings-for-web-apps) w dalszej części tego artykułu pokazano, jak zastąpić domyślne ustawienia konstruktora.</span><span class="sxs-lookup"><span data-stu-id="776d8-151">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="776d8-152">Usługi świadczone w ramach ram</span><span class="sxs-lookup"><span data-stu-id="776d8-152">Framework-provided services</span></span>

<span data-ttu-id="776d8-153">Następujące usługi są rejestrowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="776d8-153">The following services are registered automatically:</span></span>

* [<span data-ttu-id="776d8-154">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="776d8-154">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="776d8-155">IHostLifetime (czas pracy)</span><span class="sxs-lookup"><span data-stu-id="776d8-155">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="776d8-156">IHostEnvironment / IWebHostŚrodowicielenie</span><span class="sxs-lookup"><span data-stu-id="776d8-156">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="776d8-157">Aby uzyskać więcej informacji na <xref:fundamentals/dependency-injection#framework-provided-services>temat usług świadczonych w ramach ram, zobacz .</span><span class="sxs-lookup"><span data-stu-id="776d8-157">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="776d8-158">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="776d8-158">IHostApplicationLifetime</span></span>

<span data-ttu-id="776d8-159"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Wstrzyknąć `IApplicationLifetime`(dawniej) usługi do dowolnej klasy do obsługi zadań po starcie i wdzięku zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="776d8-159">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="776d8-160">Trzy właściwości w interfejsie są tokeny anulowania używane do rejestrowania uruchamiania aplikacji i metody obsługi zdarzeń zatrzymania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-160">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="776d8-161">Interfejs zawiera również `StopApplication` metodę.</span><span class="sxs-lookup"><span data-stu-id="776d8-161">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="776d8-162">Poniższy przykład `IHostedService` jest implementacją, która rejestruje `IHostApplicationLifetime` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="776d8-162">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="776d8-163">IHostLifetime (czas pracy)</span><span class="sxs-lookup"><span data-stu-id="776d8-163">IHostLifetime</span></span>

<span data-ttu-id="776d8-164">Implementacja <xref:Microsoft.Extensions.Hosting.IHostLifetime> kontroluje, kiedy host uruchamia się i kiedy zatrzymuje.</span><span class="sxs-lookup"><span data-stu-id="776d8-164">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="776d8-165">Używana jest ostatnia zarejestrowana implementacja.</span><span class="sxs-lookup"><span data-stu-id="776d8-165">The last implementation registered is used.</span></span>

<span data-ttu-id="776d8-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`jest domyślną `IHostLifetime` implementacją.</span><span class="sxs-lookup"><span data-stu-id="776d8-166">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="776d8-167">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="776d8-167">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="776d8-168">Nasłuchuje <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM i wywołuje, <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="776d8-168">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="776d8-169">Odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="776d8-169">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="776d8-170">IHostŚrodowienie</span><span class="sxs-lookup"><span data-stu-id="776d8-170">IHostEnvironment</span></span>

<span data-ttu-id="776d8-171">Wstrzyknąć <xref:Microsoft.Extensions.Hosting.IHostEnvironment> usługę do klasy, aby uzyskać informacje o następujących ustawieniach:</span><span class="sxs-lookup"><span data-stu-id="776d8-171">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="776d8-172">Applicationname</span><span class="sxs-lookup"><span data-stu-id="776d8-172">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="776d8-173">Nazwa środowiska</span><span class="sxs-lookup"><span data-stu-id="776d8-173">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="776d8-174">Ścieżka contentrootpath</span><span class="sxs-lookup"><span data-stu-id="776d8-174">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="776d8-175">Aplikacje sieci `IWebHostEnvironment` Web implementują `IHostEnvironment` interfejs, który dziedziczy i dodaje [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="776d8-175">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="776d8-176">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="776d8-176">Host configuration</span></span>

<span data-ttu-id="776d8-177">Konfiguracja hosta jest używana <xref:Microsoft.Extensions.Hosting.IHostEnvironment> dla właściwości implementacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-177">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="776d8-178">Konfiguracja hosta jest dostępna w <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>pliku [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) wewnątrz .</span><span class="sxs-lookup"><span data-stu-id="776d8-178">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="776d8-179">Po `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , jest zastępowany config aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-179">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="776d8-180">Aby dodać konfigurację <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`hosta, zadzwoń .</span><span class="sxs-lookup"><span data-stu-id="776d8-180">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="776d8-181">`ConfigureHostConfiguration`można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="776d8-181">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="776d8-182">Host używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="776d8-182">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="776d8-183">Dostawca zmiennych środowiskowych `DOTNET_` z prefiksem i `CreateDefaultBuilder`argumentami wiersza polecenia są uwzględniane przez program .</span><span class="sxs-lookup"><span data-stu-id="776d8-183">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="776d8-184">W przypadku aplikacji sieci web dodawany jest dostawca zmiennych środowiskowych z prefiksem. `ASPNETCORE_`</span><span class="sxs-lookup"><span data-stu-id="776d8-184">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="776d8-185">Prefiks jest usuwany podczas odczytu zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="776d8-185">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="776d8-186">Na przykład wartość zmiennej `ASPNETCORE_ENVIRONMENT` środowiskowej dla staje `environment` się wartością konfiguracji hosta dla klucza.</span><span class="sxs-lookup"><span data-stu-id="776d8-186">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="776d8-187">W poniższym przykładzie tworzy konfigurację hosta:</span><span class="sxs-lookup"><span data-stu-id="776d8-187">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="776d8-188">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="776d8-188">App configuration</span></span>

<span data-ttu-id="776d8-189">Konfiguracja aplikacji jest <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder`tworzona przez wywołanie .</span><span class="sxs-lookup"><span data-stu-id="776d8-189">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="776d8-190">`ConfigureAppConfiguration`można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="776d8-190">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="776d8-191">Aplikacja używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="776d8-191">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="776d8-192">Konfiguracja utworzona `ConfigureAppConfiguration` przez jest dostępna w [witrynie HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) dla kolejnych operacji i jako usługa z DI.</span><span class="sxs-lookup"><span data-stu-id="776d8-192">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="776d8-193">Konfiguracja hosta jest również dodawana do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-193">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="776d8-194">Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="776d8-194">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="776d8-195">Ustawienia dla wszystkich typów aplikacji</span><span class="sxs-lookup"><span data-stu-id="776d8-195">Settings for all app types</span></span>

<span data-ttu-id="776d8-196">W tej sekcji wymieniono ustawienia hosta, które dotyczą zarówno obciążeń HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="776d8-196">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="776d8-197">Domyślnie zmienne środowiskowe używane do konfigurowania `DOTNET_` `ASPNETCORE_` tych ustawień mogą mieć lub prefiks.</span><span class="sxs-lookup"><span data-stu-id="776d8-197">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="776d8-198">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="776d8-198">ApplicationName</span></span>

<span data-ttu-id="776d8-199">[Właściwość IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) jest ustawiana z konfiguracji hosta podczas konstruowaniu hosta.</span><span class="sxs-lookup"><span data-stu-id="776d8-199">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="776d8-200">**Klucz:**`applicationName`</span><span class="sxs-lookup"><span data-stu-id="776d8-200">**Key**: `applicationName`</span></span>  
<span data-ttu-id="776d8-201">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-201">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-202">**Domyślnie**: Nazwa zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-202">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="776d8-203">**Zmienna środowiskowa:**`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="776d8-203">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="776d8-204">Aby ustawić tę wartość, należy użyć zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="776d8-204">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="776d8-205">ContentRoot (Typ treści)</span><span class="sxs-lookup"><span data-stu-id="776d8-205">ContentRoot</span></span>

<span data-ttu-id="776d8-206">[Właściwość IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="776d8-206">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="776d8-207">Jeśli ścieżka nie istnieje, host nie uruchamia się.</span><span class="sxs-lookup"><span data-stu-id="776d8-207">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="776d8-208">**Klucz:**`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="776d8-208">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="776d8-209">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-209">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-210">**Domyślnie**: Folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-210">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="776d8-211">**Zmienna środowiskowa:**`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="776d8-211">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="776d8-212">Aby ustawić tę wartość, należy `UseContentRoot` użyć `IHostBuilder`zmiennej środowiskowej lub wywołać na:</span><span class="sxs-lookup"><span data-stu-id="776d8-212">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="776d8-213">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="776d8-213">For more information, see:</span></span>

* [<span data-ttu-id="776d8-214">Podstawy: Katalog główny treści</span><span class="sxs-lookup"><span data-stu-id="776d8-214">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="776d8-215">Webroot</span><span class="sxs-lookup"><span data-stu-id="776d8-215">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="776d8-216">Nazwa środowiska</span><span class="sxs-lookup"><span data-stu-id="776d8-216">EnvironmentName</span></span>

<span data-ttu-id="776d8-217">[Właściwość IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) można ustawić na dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="776d8-217">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="776d8-218">Wartości zdefiniowane `Development`przez `Staging`ramy `Production`obejmują , i .</span><span class="sxs-lookup"><span data-stu-id="776d8-218">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="776d8-219">W wartościach nie rozróżnia się wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="776d8-219">Values aren't case-sensitive.</span></span>

<span data-ttu-id="776d8-220">**Klucz:**`environment`</span><span class="sxs-lookup"><span data-stu-id="776d8-220">**Key**: `environment`</span></span>  
<span data-ttu-id="776d8-221">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-221">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-222">**Domyślnie**:`Production`</span><span class="sxs-lookup"><span data-stu-id="776d8-222">**Default**: `Production`</span></span>  
<span data-ttu-id="776d8-223">**Zmienna środowiskowa:**`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="776d8-223">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="776d8-224">Aby ustawić tę wartość, należy `UseEnvironment` użyć `IHostBuilder`zmiennej środowiskowej lub wywołać na:</span><span class="sxs-lookup"><span data-stu-id="776d8-224">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="776d8-225">Shutdowntimeout</span><span class="sxs-lookup"><span data-stu-id="776d8-225">ShutdownTimeout</span></span>

<span data-ttu-id="776d8-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) ustawia limit <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>czasu dla .</span><span class="sxs-lookup"><span data-stu-id="776d8-226">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="776d8-227">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="776d8-227">The default value is five seconds.</span></span>  <span data-ttu-id="776d8-228">W okresie limitu czasu host:</span><span class="sxs-lookup"><span data-stu-id="776d8-228">During the timeout period, the host:</span></span>

* <span data-ttu-id="776d8-229">Wyzwala [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="776d8-229">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="776d8-230">Próbuje zatrzymać hostowane usługi, rejestrowanie błędów dla usług, które nie mogą się zatrzymać.</span><span class="sxs-lookup"><span data-stu-id="776d8-230">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="776d8-231">Jeśli limit czasu wygaśnie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-231">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="776d8-232">Usługi zatrzymują się nawet wtedy, gdy nie zakończyły przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="776d8-232">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="776d8-233">Jeśli usługi wymagają dodatkowego czasu, aby zatrzymać, zwiększyć limit czasu.</span><span class="sxs-lookup"><span data-stu-id="776d8-233">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="776d8-234">**Klucz:**`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="776d8-234">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="776d8-235">**Typ**:`int`</span><span class="sxs-lookup"><span data-stu-id="776d8-235">**Type**: `int`</span></span>  
<span data-ttu-id="776d8-236">**Domyślnie**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="776d8-236">**Default**: 5 seconds</span></span>  
<span data-ttu-id="776d8-237">**Zmienna środowiskowa:**`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="776d8-237">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="776d8-238">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub skonfiguruj `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="776d8-238">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="776d8-239">W poniższym przykładzie ustawia limit czasu na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="776d8-239">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="776d8-240">Ustawienia aplikacji internetowych</span><span class="sxs-lookup"><span data-stu-id="776d8-240">Settings for web apps</span></span>

<span data-ttu-id="776d8-241">Niektóre ustawienia hosta dotyczą tylko obciążeń HTTP.</span><span class="sxs-lookup"><span data-stu-id="776d8-241">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="776d8-242">Domyślnie zmienne środowiskowe używane do konfigurowania `DOTNET_` `ASPNETCORE_` tych ustawień mogą mieć lub prefiks.</span><span class="sxs-lookup"><span data-stu-id="776d8-242">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="776d8-243">Dostępne `IWebHostBuilder` są metody rozszerzenia dla tych ustawień.</span><span class="sxs-lookup"><span data-stu-id="776d8-243">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="776d8-244">Przykłady kodu, które pokazują, jak `webBuilder` wywołać `IWebHostBuilder`metody rozszerzenia zakłada, że jest wystąpieniem , jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="776d8-244">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="776d8-245">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="776d8-245">CaptureStartupErrors</span></span>

<span data-ttu-id="776d8-246">Kiedy `false`, błędy podczas uruchamiania spowodować wyjście hosta.</span><span class="sxs-lookup"><span data-stu-id="776d8-246">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="776d8-247">Gdy `true`host przechwytuje wyjątki podczas uruchamiania i próbuje uruchomić serwer.</span><span class="sxs-lookup"><span data-stu-id="776d8-247">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="776d8-248">**Klucz:**`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="776d8-248">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="776d8-249">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="776d8-249">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="776d8-250">**Domyślnie:** `false` Domyślnie, chyba że aplikacja działa z Kestrel `true`za IIS, gdzie domyślnie jest .</span><span class="sxs-lookup"><span data-stu-id="776d8-250">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="776d8-251">**Zmienna środowiskowa:**`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="776d8-251">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="776d8-252">Aby ustawić tę wartość, `CaptureStartupErrors`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="776d8-252">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="776d8-253">Szczegółoweerrory</span><span class="sxs-lookup"><span data-stu-id="776d8-253">DetailedErrors</span></span>

<span data-ttu-id="776d8-254">Po włączeniu lub gdy `Development`środowisko jest , aplikacja przechwytuje szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="776d8-254">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="776d8-255">**Klucz:**`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="776d8-255">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="776d8-256">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="776d8-256">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="776d8-257">**Domyślnie**:`false`</span><span class="sxs-lookup"><span data-stu-id="776d8-257">**Default**: `false`</span></span>  
<span data-ttu-id="776d8-258">**Zmienna środowiskowa:**`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="776d8-258">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="776d8-259">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="776d8-259">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="776d8-260">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="776d8-260">HostingStartupAssemblies</span></span>

<span data-ttu-id="776d8-261">Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do załadowania podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="776d8-261">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="776d8-262">Mimo że wartość konfiguracji domyślnie pusty ciąg, zestawy uruchamiania hostingu zawsze zawierają zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-262">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="776d8-263">Po udostępnieniu zestawów uruchamiania hostingu są one dodawane do zestawu aplikacji do ładowania, gdy aplikacja tworzy swoje typowe usługi podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="776d8-263">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="776d8-264">**Klucz:**`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="776d8-264">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="776d8-265">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-265">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-266">**Domyślnie**: Pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="776d8-266">**Default**: Empty string</span></span>  
<span data-ttu-id="776d8-267">**Zmienna środowiskowa:**`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="776d8-267">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="776d8-268">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="776d8-268">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="776d8-269">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="776d8-269">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="776d8-270">Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do wykluczenia podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="776d8-270">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="776d8-271">**Klucz:**`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="776d8-271">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="776d8-272">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-272">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-273">**Domyślnie**: Pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="776d8-273">**Default**: Empty string</span></span>  
<span data-ttu-id="776d8-274">**Zmienna środowiskowa:**`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="776d8-274">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="776d8-275">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="776d8-275">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="776d8-276">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="776d8-276">HTTPS_Port</span></span>

<span data-ttu-id="776d8-277">Port przekierowania HTTPS.</span><span class="sxs-lookup"><span data-stu-id="776d8-277">The HTTPS redirect port.</span></span> <span data-ttu-id="776d8-278">Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="776d8-278">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="776d8-279">**Klucz:**`https_port`</span><span class="sxs-lookup"><span data-stu-id="776d8-279">**Key**: `https_port`</span></span>  
<span data-ttu-id="776d8-280">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-280">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-281">**Domyślnie:** Wartość domyślna nie jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="776d8-281">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="776d8-282">**Zmienna środowiskowa:**`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="776d8-282">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="776d8-283">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="776d8-283">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="776d8-284">PreferHostingUrls (PreferHostingUrls)</span><span class="sxs-lookup"><span data-stu-id="776d8-284">PreferHostingUrls</span></span>

<span data-ttu-id="776d8-285">Wskazuje, czy host powinien nasłuchiwania adresów URL skonfigurowanych przy 200 000 `IWebHostBuilder` adresów URL skonfigurowanych z `IServer` implementacją.</span><span class="sxs-lookup"><span data-stu-id="776d8-285">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="776d8-286">**Klucz:**`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="776d8-286">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="776d8-287">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="776d8-287">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="776d8-288">**Domyślnie**:`true`</span><span class="sxs-lookup"><span data-stu-id="776d8-288">**Default**: `true`</span></span>  
<span data-ttu-id="776d8-289">**Zmienna środowiskowa:**`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="776d8-289">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="776d8-290">Aby ustawić tę wartość, użyj `PreferHostingUrls`zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="776d8-290">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="776d8-291">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="776d8-291">PreventHostingStartup</span></span>

<span data-ttu-id="776d8-292">Zapobiega automatycznemu obciążaniu zestawów uruchamiania hostingu, w tym zestawów uruchamiania hostingu skonfigurowanych przez zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-292">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="776d8-293">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="776d8-293">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="776d8-294">**Klucz:**`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="776d8-294">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="776d8-295">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="776d8-295">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="776d8-296">**Domyślnie**:`false`</span><span class="sxs-lookup"><span data-stu-id="776d8-296">**Default**: `false`</span></span>  
<span data-ttu-id="776d8-297">**Zmienna środowiskowa:**`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="776d8-297">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="776d8-298">Aby ustawić tę wartość, użyj `UseSetting` zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="776d8-298">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="776d8-299">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="776d8-299">StartupAssembly</span></span>

<span data-ttu-id="776d8-300">Zestaw do wyszukiwania `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="776d8-300">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="776d8-301">**Klucz:**`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="776d8-301">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="776d8-302">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-302">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-303">**Domyślnie**: Zespół aplikacji</span><span class="sxs-lookup"><span data-stu-id="776d8-303">**Default**: The app's assembly</span></span>  
<span data-ttu-id="776d8-304">**Zmienna środowiskowa:**`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="776d8-304">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="776d8-305">Aby ustawić tę wartość, należy `UseStartup`użyć zmiennej środowiskowej lub wywołania .</span><span class="sxs-lookup"><span data-stu-id="776d8-305">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="776d8-306">`UseStartup`może przyjąć nazwę`string`zestawu ( )`TStartup`lub typ ( ).</span><span class="sxs-lookup"><span data-stu-id="776d8-306">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="776d8-307">Jeśli `UseStartup` wywoływanych jest wiele metod, pierwszeństwo ma ostatnia.</span><span class="sxs-lookup"><span data-stu-id="776d8-307">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="776d8-308">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="776d8-308">URLs</span></span>

<span data-ttu-id="776d8-309">Lista adresów IP lub adresów hostów rozdzielanych średnikami z portami i protokołami, na których serwer powinien nasłuchiwał żądań.</span><span class="sxs-lookup"><span data-stu-id="776d8-309">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="776d8-310">Na przykład `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="776d8-310">For example, `http://localhost:123`.</span></span> <span data-ttu-id="776d8-311">Użyj\*" ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwach hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="776d8-311">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="776d8-312">Protokół (`http://` `https://`lub ) musi być dołączony do każdego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="776d8-312">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="776d8-313">Obsługiwane formaty różnią się w zależności od serwera.</span><span class="sxs-lookup"><span data-stu-id="776d8-313">Supported formats vary among servers.</span></span>

<span data-ttu-id="776d8-314">**Klucz:**`urls`</span><span class="sxs-lookup"><span data-stu-id="776d8-314">**Key**: `urls`</span></span>  
<span data-ttu-id="776d8-315">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-315">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-316">**Domyślnie**: `http://localhost:5000` i`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="776d8-316">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="776d8-317">**Zmienna środowiskowa:**`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="776d8-317">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="776d8-318">Aby ustawić tę wartość, użyj `UseUrls`zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="776d8-318">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="776d8-319">Pustułka ma własny interfejs API konfiguracji punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="776d8-319">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="776d8-320">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="776d8-320">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="776d8-321">Webroot</span><span class="sxs-lookup"><span data-stu-id="776d8-321">WebRoot</span></span>

<span data-ttu-id="776d8-322">[Właściwość IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) określa względną ścieżkę do statycznych zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-322">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="776d8-323">Jeśli ścieżka nie istnieje, używany jest dostawca plików bez operacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-323">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="776d8-324">**Klucz:**`webroot`</span><span class="sxs-lookup"><span data-stu-id="776d8-324">**Key**: `webroot`</span></span>  
<span data-ttu-id="776d8-325">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-325">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-326">**Domyślnie**: `wwwroot`Wartość domyślna to .</span><span class="sxs-lookup"><span data-stu-id="776d8-326">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="776d8-327">Musi istnieć ścieżka do *{content root}/wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="776d8-327">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="776d8-328">**Zmienna środowiskowa:**`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="776d8-328">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="776d8-329">Aby ustawić tę wartość, należy `UseWebRoot` użyć `IWebHostBuilder`zmiennej środowiskowej lub wywołać na:</span><span class="sxs-lookup"><span data-stu-id="776d8-329">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="776d8-330">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="776d8-330">For more information, see:</span></span>

* [<span data-ttu-id="776d8-331">Podstawy: Korzeń sieci Web</span><span class="sxs-lookup"><span data-stu-id="776d8-331">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="776d8-332">ContentRoot (Typ treści)</span><span class="sxs-lookup"><span data-stu-id="776d8-332">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="776d8-333">Zarządzanie okresem istnienia hosta</span><span class="sxs-lookup"><span data-stu-id="776d8-333">Manage the host lifetime</span></span>

<span data-ttu-id="776d8-334">Wywołaj metody <xref:Microsoft.Extensions.Hosting.IHost> na wbudowanej implementacji, aby uruchomić i zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="776d8-334">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="776d8-335">Te metody <xref:Microsoft.Extensions.Hosting.IHostedService> mają wpływ na wszystkie implementacje, które są zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="776d8-335">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="776d8-336">Run</span><span class="sxs-lookup"><span data-stu-id="776d8-336">Run</span></span>

<span data-ttu-id="776d8-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący, dopóki host nie zostanie zamknięty.</span><span class="sxs-lookup"><span data-stu-id="776d8-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="776d8-338">RunAsync ( RunAsync )</span><span class="sxs-lookup"><span data-stu-id="776d8-338">RunAsync</span></span>

<span data-ttu-id="776d8-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia systemu.</span><span class="sxs-lookup"><span data-stu-id="776d8-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="776d8-340">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="776d8-340">RunConsoleAsync</span></span>

<span data-ttu-id="776d8-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>umożliwia obsługę konsoli, buduje i uruchamia hosta i czeka na <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM, aby zamknąć.</span><span class="sxs-lookup"><span data-stu-id="776d8-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="776d8-342">Uruchamianie</span><span class="sxs-lookup"><span data-stu-id="776d8-342">Start</span></span>

<span data-ttu-id="776d8-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="776d8-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="776d8-344">StartAsync (StartAsync)</span><span class="sxs-lookup"><span data-stu-id="776d8-344">StartAsync</span></span>

<span data-ttu-id="776d8-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia hosta i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="776d8-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="776d8-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na `StartAsync`początku , który czeka, aż zakończy się przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="776d8-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="776d8-347">Może to służyć do opóźnienia uruchamiania, dopóki nie zostanie zasygnaligozowana przez zdarzenie zewnętrzne.</span><span class="sxs-lookup"><span data-stu-id="776d8-347">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="776d8-348">StopAsync (StopAsync)</span><span class="sxs-lookup"><span data-stu-id="776d8-348">StopAsync</span></span>

<span data-ttu-id="776d8-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w podanym limit czasu.</span><span class="sxs-lookup"><span data-stu-id="776d8-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="776d8-350">WaitForShutdown (WaitForShutdown)</span><span class="sxs-lookup"><span data-stu-id="776d8-350">WaitForShutdown</span></span>

<span data-ttu-id="776d8-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje wątek wywołujący, dopóki zamknięcie zostanie wywołane przez IHostLifetime, na przykład przez <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="776d8-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="776d8-352">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="776d8-352">WaitForShutdownAsync</span></span>

<span data-ttu-id="776d8-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task> zwraca, który kończy się po zamknięciu jest <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>wyzwalany za pośrednictwem danego tokenu i wywołania .</span><span class="sxs-lookup"><span data-stu-id="776d8-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="776d8-354">Sterowanie zewnętrzne</span><span class="sxs-lookup"><span data-stu-id="776d8-354">External control</span></span>

<span data-ttu-id="776d8-355">Bezpośrednią kontrolę okresu istnienia hosta można osiągnąć za pomocą metod, które można wywołać zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="776d8-355">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="776d8-356">ASP.NET aplikacje Core konfigurują i uruchamiają hosta.</span><span class="sxs-lookup"><span data-stu-id="776d8-356">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="776d8-357">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="776d8-357">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="776d8-358">W tym artykule omówiono ASP.NET<xref:Microsoft.Extensions.Hosting.HostBuilder>Core Generic Host ( ), który jest używany dla aplikacji, które nie przetwarzają żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="776d8-358">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="776d8-359">Celem hosta ogólnego jest oddzielenie potoku HTTP od interfejsu API hosta sieci Web, aby włączyć szerszą gamę scenariuszy hosta.</span><span class="sxs-lookup"><span data-stu-id="776d8-359">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="776d8-360">Wiadomości, zadania w tle i inne obciążenia inne niż HTTP oparte na generic hosta korzystają z możliwości przekrojowych, takich jak konfiguracja, iniekcja zależności (DI) i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="776d8-360">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="776d8-361">Generic Host jest nowy w ASP.NET Core 2.1 i nie nadaje się do scenariuszy hostingu.</span><span class="sxs-lookup"><span data-stu-id="776d8-361">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="776d8-362">W przypadku scenariuszy hostingu sieci Web należy użyć [hosta sieci Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="776d8-362">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="776d8-363">Generic Host zastąpi hosta sieci Web w przyszłej wersji i będzie działać jako podstawowy interfejs API hosta zarówno w scenariuszach HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="776d8-363">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="776d8-364">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="776d8-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="776d8-365">Podczas uruchamiania przykładowej aplikacji w [programie Visual Studio Code](https://code.visualstudio.com/)należy użyć zewnętrznego lub *zintegrowanego terminala*.</span><span class="sxs-lookup"><span data-stu-id="776d8-365">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="776d8-366">Nie uruchamiaj próbki w `internalConsole`pliku .</span><span class="sxs-lookup"><span data-stu-id="776d8-366">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="776d8-367">Aby ustawić konsolę w programie Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="776d8-367">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="776d8-368">Otwórz plik *vscode/launch.json.*</span><span class="sxs-lookup"><span data-stu-id="776d8-368">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="776d8-369">W konfiguracji **.NET Core Launch (konsola)** znajdź wpis **konsoli.**</span><span class="sxs-lookup"><span data-stu-id="776d8-369">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="776d8-370">Ustaw wartość na `externalTerminal` jedną `integratedTerminal`lub .</span><span class="sxs-lookup"><span data-stu-id="776d8-370">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="776d8-371">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="776d8-371">Introduction</span></span>

<span data-ttu-id="776d8-372">Biblioteka hosta ogólnego <xref:Microsoft.Extensions.Hosting> jest dostępna w obszarze nazw i jest dostarczana przez pakiet [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/)</span><span class="sxs-lookup"><span data-stu-id="776d8-372">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="776d8-373">Pakiet `Microsoft.Extensions.Hosting` jest zawarty w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 lub nowszym).</span><span class="sxs-lookup"><span data-stu-id="776d8-373">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="776d8-374"><xref:Microsoft.Extensions.Hosting.IHostedService>jest punktem wejścia do wykonania kodu.</span><span class="sxs-lookup"><span data-stu-id="776d8-374"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="776d8-375">Każda <xref:Microsoft.Extensions.Hosting.IHostedService> implementacja jest wykonywana w kolejności [rejestracji usługi w ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="776d8-375">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="776d8-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>jest wywoływana <xref:Microsoft.Extensions.Hosting.IHostedService> na każdym po <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> uruchomieniu hosta i jest wywoływana w odwrotnej kolejności rejestracji, gdy host zamyka się bezpiecznie.</span><span class="sxs-lookup"><span data-stu-id="776d8-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="776d8-377">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="776d8-377">Set up a host</span></span>

<span data-ttu-id="776d8-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder>jest głównym składnikiem używanym przez biblioteki i aplikacje do inicjowania, tworzenia i uruchamiania hosta:</span><span class="sxs-lookup"><span data-stu-id="776d8-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="776d8-379">Opcje</span><span class="sxs-lookup"><span data-stu-id="776d8-379">Options</span></span>

<span data-ttu-id="776d8-380"><xref:Microsoft.Extensions.Hosting.HostOptions>skonfigurować opcje <xref:Microsoft.Extensions.Hosting.IHost>dla pliku .</span><span class="sxs-lookup"><span data-stu-id="776d8-380"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="776d8-381">Limit czasu zamknięcia</span><span class="sxs-lookup"><span data-stu-id="776d8-381">Shutdown timeout</span></span>

<span data-ttu-id="776d8-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>ustawia limit czasu <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>dla .</span><span class="sxs-lookup"><span data-stu-id="776d8-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="776d8-383">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="776d8-383">The default value is five seconds.</span></span>

<span data-ttu-id="776d8-384">Następująca konfiguracja `Program.Main` opcji zwiększa domyślny pięciosekundowy limit czasu zamknięcia do 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="776d8-384">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="776d8-385">Usługi domyślne</span><span class="sxs-lookup"><span data-stu-id="776d8-385">Default services</span></span>

<span data-ttu-id="776d8-386">Podczas inicjowania hosta rejestrowane są następujące usługi:</span><span class="sxs-lookup"><span data-stu-id="776d8-386">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="776d8-387">[Środowisko](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>( )</span><span class="sxs-lookup"><span data-stu-id="776d8-387">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="776d8-388">[Konfiguracja](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="776d8-388">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="776d8-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="776d8-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="776d8-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="776d8-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="776d8-391">[Opcje](xref:fundamentals/configuration/options) <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>( )</span><span class="sxs-lookup"><span data-stu-id="776d8-391">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="776d8-392">[Rejestrowanie](xref:fundamentals/logging/index) <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>( )</span><span class="sxs-lookup"><span data-stu-id="776d8-392">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="776d8-393">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="776d8-393">Host configuration</span></span>

<span data-ttu-id="776d8-394">Konfiguracja hosta jest tworzona przez:</span><span class="sxs-lookup"><span data-stu-id="776d8-394">Host configuration is created by:</span></span>

* <span data-ttu-id="776d8-395">Wywoływanie metod <xref:Microsoft.Extensions.Hosting.IHostBuilder> rozszerzenia w celu ustawienia [katalogu głównego](#content-root) zawartości i [środowiska](#environment).</span><span class="sxs-lookup"><span data-stu-id="776d8-395">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="776d8-396">Odczytywanie konfiguracji <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>od dostawców konfiguracji w programie .</span><span class="sxs-lookup"><span data-stu-id="776d8-396">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="776d8-397">Metody rozszerzeń</span><span class="sxs-lookup"><span data-stu-id="776d8-397">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="776d8-398">Klucz aplikacji (nazwa)</span><span class="sxs-lookup"><span data-stu-id="776d8-398">Application key (name)</span></span>

<span data-ttu-id="776d8-399">[Właściwość IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) jest ustawiona z konfiguracji hosta podczas konstruowania hosta.</span><span class="sxs-lookup"><span data-stu-id="776d8-399">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="776d8-400">Aby jawnie ustawić wartość, użyj [hostDefaults.ApplicationKey:](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)</span><span class="sxs-lookup"><span data-stu-id="776d8-400">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="776d8-401">**Klucz:**`applicationName`</span><span class="sxs-lookup"><span data-stu-id="776d8-401">**Key**: `applicationName`</span></span>  
<span data-ttu-id="776d8-402">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-402">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-403">**Domyślnie**: Nazwa zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-403">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="776d8-404">**Zestaw za pomocą**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="776d8-404">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="776d8-405">**Zmienna środowiskowa**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` jest [opcjonalna i zdefiniowana przez użytkownika)](#configurehostconfiguration)</span><span class="sxs-lookup"><span data-stu-id="776d8-405">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="776d8-406">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="776d8-406">Content root</span></span>

<span data-ttu-id="776d8-407">To ustawienie określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="776d8-407">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="776d8-408">**Klucz:**`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="776d8-408">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="776d8-409">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-409">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-410">**Domyślnie**: Domyślnie folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-410">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="776d8-411">**Zestaw za pomocą**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="776d8-411">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="776d8-412">**Zmienna środowiskowa**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` jest [opcjonalna i zdefiniowana przez użytkownika)](#configurehostconfiguration)</span><span class="sxs-lookup"><span data-stu-id="776d8-412">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="776d8-413">Jeśli ścieżka nie istnieje, host nie uruchamia się.</span><span class="sxs-lookup"><span data-stu-id="776d8-413">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="776d8-414">Aby uzyskać więcej informacji, zobacz [Podstawy: Katalog główny zawartości](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="776d8-414">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="776d8-415">Środowisko</span><span class="sxs-lookup"><span data-stu-id="776d8-415">Environment</span></span>

<span data-ttu-id="776d8-416">Ustawia [środowisko](xref:fundamentals/environments)aplikacji .</span><span class="sxs-lookup"><span data-stu-id="776d8-416">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="776d8-417">**Klucz:**`environment`</span><span class="sxs-lookup"><span data-stu-id="776d8-417">**Key**: `environment`</span></span>  
<span data-ttu-id="776d8-418">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-418">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-419">**Domyślnie**:`Production`</span><span class="sxs-lookup"><span data-stu-id="776d8-419">**Default**: `Production`</span></span>  
<span data-ttu-id="776d8-420">**Zestaw za pomocą**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="776d8-420">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="776d8-421">**Zmienna środowiskowa**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` jest [opcjonalna i zdefiniowana przez użytkownika)](#configurehostconfiguration)</span><span class="sxs-lookup"><span data-stu-id="776d8-421">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="776d8-422">Środowisko można ustawić na dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="776d8-422">The environment can be set to any value.</span></span> <span data-ttu-id="776d8-423">Wartości zdefiniowane `Development`przez `Staging`ramy `Production`obejmują , i .</span><span class="sxs-lookup"><span data-stu-id="776d8-423">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="776d8-424">W wartościach nie rozróżnia się wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="776d8-424">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="776d8-425">Konfigurowanie konfiguracji hostowania</span><span class="sxs-lookup"><span data-stu-id="776d8-425">ConfigureHostConfiguration</span></span>

<span data-ttu-id="776d8-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>używa do <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> utworzenia <xref:Microsoft.Extensions.Configuration.IConfiguration> dla hosta.</span><span class="sxs-lookup"><span data-stu-id="776d8-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="776d8-427">Konfiguracja hosta służy do <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> inicjowania do użycia w procesie kompilacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-427">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="776d8-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="776d8-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="776d8-429">Host używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="776d8-429">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="776d8-430">Domyślnie nie są uwzględniane żadne dostawcy.</span><span class="sxs-lookup"><span data-stu-id="776d8-430">No providers are included by default.</span></span> <span data-ttu-id="776d8-431">Należy jawnie określić dostawców konfiguracji, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>których aplikacja wymaga w , w tym:</span><span class="sxs-lookup"><span data-stu-id="776d8-431">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="776d8-432">Konfiguracja pliku (na przykład z pliku *hostsettings.json).*</span><span class="sxs-lookup"><span data-stu-id="776d8-432">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="776d8-433">Konfiguracja zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="776d8-433">Environment variable configuration.</span></span>
* <span data-ttu-id="776d8-434">Konfiguracja argumentu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="776d8-434">Command-line argument configuration.</span></span>
* <span data-ttu-id="776d8-435">Wszelkie inne wymagane dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="776d8-435">Any other required configuration providers.</span></span>

<span data-ttu-id="776d8-436">Konfiguracja pliku hosta jest włączona, określając ścieżkę `SetBasePath` podstawową aplikacji, po której następuje wywołanie jednego z [dostawców konfiguracji plików.](xref:fundamentals/configuration/index#file-configuration-provider)</span><span class="sxs-lookup"><span data-stu-id="776d8-436">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="776d8-437">Przykładowa aplikacja używa pliku JSON, *hostsettings.json*i wywołania, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> aby korzystać z ustawień konfiguracji hosta pliku.</span><span class="sxs-lookup"><span data-stu-id="776d8-437">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="776d8-438">Aby dodać [konfigurację zmiennej środowiskowej](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hosta, należy wywołać <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> konstruktora hosta.</span><span class="sxs-lookup"><span data-stu-id="776d8-438">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="776d8-439">`AddEnvironmentVariables`akceptuje opcjonalny prefiks zdefiniowany przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="776d8-439">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="776d8-440">Przykładowa aplikacja używa prefiksu `PREFIX_`programu .</span><span class="sxs-lookup"><span data-stu-id="776d8-440">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="776d8-441">Prefiks jest usuwany podczas odczytu zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="776d8-441">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="776d8-442">Po skonfigurowaniu hosta przykładowej aplikacji wartość zmiennej środowiskowej `PREFIX_ENVIRONMENT` staje `environment` się wartością konfiguracji hosta dla klucza.</span><span class="sxs-lookup"><span data-stu-id="776d8-442">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="776d8-443">Podczas tworzenia podczas korzystania z programu `dotnet run`Visual [Studio](https://visualstudio.microsoft.com) lub uruchamiania aplikacji z , zmienne środowiskowe mogą być ustawione w pliku *Properties/launchSettings.json.*</span><span class="sxs-lookup"><span data-stu-id="776d8-443">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="776d8-444">W [programie Visual Studio Code](https://code.visualstudio.com/)zmienne środowiskowe mogą być ustawiane w pliku *vscode/launch.json* podczas tworzenia.</span><span class="sxs-lookup"><span data-stu-id="776d8-444">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="776d8-445">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="776d8-445">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="776d8-446">[Konfiguracja wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>jest dodawana przez wywołanie .</span><span class="sxs-lookup"><span data-stu-id="776d8-446">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="776d8-447">Konfiguracja wiersza polecenia jest dodawana jako ostatnia, aby umożliwić argumentom wiersza polecenia zastąpienie konfiguracji dostarczonej przez wcześniejszych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="776d8-447">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="776d8-448">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="776d8-448">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="776d8-449">Dodatkowa konfiguracja może być dostarczona z [kluczami applicationName](#application-key-name) i [contentRoot.](#content-root)</span><span class="sxs-lookup"><span data-stu-id="776d8-449">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="776d8-450">Przykładowa `HostBuilder` <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>konfiguracja przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="776d8-450">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="776d8-451">Konfigurowanie konfiguracjiappconfiguration</span><span class="sxs-lookup"><span data-stu-id="776d8-451">ConfigureAppConfiguration</span></span>

<span data-ttu-id="776d8-452">Konfiguracja aplikacji jest <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> tworzona przez wywołanie <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-452">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="776d8-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>używa do <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> utworzenia <xref:Microsoft.Extensions.Configuration.IConfiguration> aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="776d8-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="776d8-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="776d8-455">Aplikacja używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="776d8-455">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="776d8-456">Konfiguracja utworzona <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> przez jest dostępna w [witrynie HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) dla kolejnych operacji i w <xref:Microsoft.Extensions.Hosting.IHost.Services*>programie .</span><span class="sxs-lookup"><span data-stu-id="776d8-456">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="776d8-457">Konfiguracja aplikacji automatycznie odbiera konfigurację hosta udostępnianą przez [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="776d8-457">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="776d8-458">Przykładowa konfiguracja aplikacji przy użyciu: <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*></span><span class="sxs-lookup"><span data-stu-id="776d8-458">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="776d8-459">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="776d8-459">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="776d8-460">*appsettings. Development.json*:</span><span class="sxs-lookup"><span data-stu-id="776d8-460">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="776d8-461">*appsettings. Produkcja.json*:</span><span class="sxs-lookup"><span data-stu-id="776d8-461">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="776d8-462">Aby przenieść pliki ustawień do katalogu wyjściowego, określ pliki ustawień jako [elementy projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="776d8-462">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="776d8-463">Przykładowa aplikacja przenosi pliki ustawień aplikacji JSON i *hostsettings.json* z następującym `<Content>` elementem:</span><span class="sxs-lookup"><span data-stu-id="776d8-463">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="776d8-464">Metody rozszerzenia konfiguracji, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> takie jak i wymagają dodatkowych pakietów NuGet, takich jak [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) i [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="776d8-464">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="776d8-465">Chyba że aplikacja używa [microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), pakiety te muszą zostać dodane do projektu oprócz podstawowego pakietu [Microsoft.Extensions.Configuration.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration)</span><span class="sxs-lookup"><span data-stu-id="776d8-465">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="776d8-466">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="776d8-466">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="776d8-467">Konfigurowanie usług</span><span class="sxs-lookup"><span data-stu-id="776d8-467">ConfigureServices</span></span>

<span data-ttu-id="776d8-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>dodaje usługi do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="776d8-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="776d8-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="776d8-470">Usługa hostowana jest klasą z logiką <xref:Microsoft.Extensions.Hosting.IHostedService> zadań w tle, która implementuje interfejs.</span><span class="sxs-lookup"><span data-stu-id="776d8-470">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="776d8-471">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="776d8-471">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="776d8-472">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) używa `AddHostedService` metody rozszerzenia, aby dodać `LifetimeEventsHostedService`usługę dla zdarzeń okresu `TimedHostedService`istnienia i zadania w tle z odpowiednim czasem, do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="776d8-472">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="776d8-473">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="776d8-473">ConfigureLogging</span></span>

<span data-ttu-id="776d8-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>dodaje pełnomocnika do skonfigurowania dostarczonego <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span><span class="sxs-lookup"><span data-stu-id="776d8-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="776d8-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="776d8-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="776d8-476">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="776d8-476">UseConsoleLifetime</span></span>

<span data-ttu-id="776d8-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>nasłuchuje <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM i wywołuje, <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="776d8-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="776d8-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="776d8-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="776d8-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`jest wstępnie zarejestrowany jako domyślna implementacja okresu istnienia.</span><span class="sxs-lookup"><span data-stu-id="776d8-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="776d8-480">Używany jest ostatni zarejestrowany okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="776d8-480">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="776d8-481">Konfiguracja kontenera</span><span class="sxs-lookup"><span data-stu-id="776d8-481">Container configuration</span></span>

<span data-ttu-id="776d8-482">Aby obsługiwać podłączanie do innych kontenerów, host może zaakceptować plik <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="776d8-482">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="776d8-483">Zapewnienie fabryki nie jest częścią rejestracji kontenera DI, ale zamiast tego jest nieodłącznym elementem hosta używanym do tworzenia betonowego kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="776d8-483">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="776d8-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder)&gt;](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) zastępuje domyślną fabrykę używaną do utworzenia dostawcy usług aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="776d8-485">Niestandardowa konfiguracja kontenera jest zarządzana <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> przez metodę.</span><span class="sxs-lookup"><span data-stu-id="776d8-485">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="776d8-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>zapewnia silnie typizowane środowisko konfigurowania kontenera na podstawie podstawowego interfejsu API hosta.</span><span class="sxs-lookup"><span data-stu-id="776d8-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="776d8-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="776d8-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="776d8-488">Tworzenie kontenera usługi dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="776d8-488">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="776d8-489">Zapewnienie fabryki kontenerów serwisowych:</span><span class="sxs-lookup"><span data-stu-id="776d8-489">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="776d8-490">Użyj ustawień fabrycznych i skonfiguruj niestandardowy kontener usługi dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="776d8-490">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="776d8-491">Rozszerzalność</span><span class="sxs-lookup"><span data-stu-id="776d8-491">Extensibility</span></span>

<span data-ttu-id="776d8-492">Rozszerzalność hosta jest wykonywana <xref:Microsoft.Extensions.Hosting.IHostBuilder>przy metodach rozszerzeń na .</span><span class="sxs-lookup"><span data-stu-id="776d8-492">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="776d8-493">W poniższym przykładzie pokazano, <xref:Microsoft.Extensions.Hosting.IHostBuilder> jak metoda rozszerzenia rozszerza implementację za <xref:fundamentals/host/hosted-services>pomocą przykładu [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) przedstawionego w .</span><span class="sxs-lookup"><span data-stu-id="776d8-493">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="776d8-494">Aplikacja ustanawia metodę `UseHostedService` rozszerzenia, aby zarejestrować usługę `T`hosta przekazywana w:</span><span class="sxs-lookup"><span data-stu-id="776d8-494">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="776d8-495">Zarządzanie hostem</span><span class="sxs-lookup"><span data-stu-id="776d8-495">Manage the host</span></span>

<span data-ttu-id="776d8-496">Implementacja <xref:Microsoft.Extensions.Hosting.IHost> jest odpowiedzialny za <xref:Microsoft.Extensions.Hosting.IHostedService> uruchamianie i zatrzymywanie implementacji, które są zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="776d8-496">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="776d8-497">Run</span><span class="sxs-lookup"><span data-stu-id="776d8-497">Run</span></span>

<span data-ttu-id="776d8-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący, dopóki host nie zostanie zamknięty:</span><span class="sxs-lookup"><span data-stu-id="776d8-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="776d8-499">RunAsync ( RunAsync )</span><span class="sxs-lookup"><span data-stu-id="776d8-499">RunAsync</span></span>

<span data-ttu-id="776d8-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia systemu:</span><span class="sxs-lookup"><span data-stu-id="776d8-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="776d8-501">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="776d8-501">RunConsoleAsync</span></span>

<span data-ttu-id="776d8-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>umożliwia obsługę konsoli, buduje i uruchamia hosta i czeka na <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM, aby zamknąć.</span><span class="sxs-lookup"><span data-stu-id="776d8-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="776d8-503">Start i StopAsync</span><span class="sxs-lookup"><span data-stu-id="776d8-503">Start and StopAsync</span></span>

<span data-ttu-id="776d8-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="776d8-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="776d8-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w podanym limit czasu.</span><span class="sxs-lookup"><span data-stu-id="776d8-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="776d8-506">StartAsync i StopAsync</span><span class="sxs-lookup"><span data-stu-id="776d8-506">StartAsync and StopAsync</span></span>

<span data-ttu-id="776d8-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="776d8-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="776d8-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zatrzymuje aplikację.</span><span class="sxs-lookup"><span data-stu-id="776d8-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="776d8-509">WaitForShutdown (WaitForShutdown)</span><span class="sxs-lookup"><span data-stu-id="776d8-509">WaitForShutdown</span></span>

<span data-ttu-id="776d8-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>jest wyzwalany <xref:Microsoft.Extensions.Hosting.IHostLifetime>przez , `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` takich jak (nasłuchuje <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="776d8-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="776d8-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>połączeń <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>telefonicznych .</span><span class="sxs-lookup"><span data-stu-id="776d8-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="776d8-512">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="776d8-512">WaitForShutdownAsync</span></span>

<span data-ttu-id="776d8-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task> zwraca, który kończy się po zamknięciu jest <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>wyzwalany za pośrednictwem danego tokenu i wywołania .</span><span class="sxs-lookup"><span data-stu-id="776d8-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="776d8-514">Sterowanie zewnętrzne</span><span class="sxs-lookup"><span data-stu-id="776d8-514">External control</span></span>

<span data-ttu-id="776d8-515">Zewnętrzna kontrola hosta można osiągnąć za pomocą metod, które można wywołać zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="776d8-515">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="776d8-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>początku , który czeka, aż zakończy się przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="776d8-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="776d8-517">Może to służyć do opóźnienia uruchamiania, dopóki nie zostanie zasygnaligozowana przez zdarzenie zewnętrzne.</span><span class="sxs-lookup"><span data-stu-id="776d8-517">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="776d8-518">Interfejs IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="776d8-518">IHostingEnvironment interface</span></span>

<span data-ttu-id="776d8-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>zawiera informacje o środowisku hostingowym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="776d8-520">Użyj [iniekcji konstruktora,](xref:fundamentals/dependency-injection) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> aby uzyskać w celu wykorzystania jego właściwości i metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="776d8-520">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="776d8-521">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="776d8-521">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="776d8-522">Interfejs IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="776d8-522">IApplicationLifetime interface</span></span>

<span data-ttu-id="776d8-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>umożliwia działania po starcie i zamknięciu, w tym wdzięczne żądania zamknięcia systemu.</span><span class="sxs-lookup"><span data-stu-id="776d8-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="776d8-524">Trzy właściwości interfejsu są tokeny anulowania <xref:System.Action> używane do rejestrowania metod, które definiują zdarzenia uruchamiania i zamykania.</span><span class="sxs-lookup"><span data-stu-id="776d8-524">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="776d8-525">Token anulowania</span><span class="sxs-lookup"><span data-stu-id="776d8-525">Cancellation Token</span></span> | <span data-ttu-id="776d8-526">Wyzwalane podczas&#8230;</span><span class="sxs-lookup"><span data-stu-id="776d8-526">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="776d8-527">Gospodarz w pełni się rozpoczął.</span><span class="sxs-lookup"><span data-stu-id="776d8-527">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="776d8-528">Host kończy wdzięczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="776d8-528">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="776d8-529">Wszystkie wnioski powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="776d8-529">All requests should be processed.</span></span> <span data-ttu-id="776d8-530">Zamykanie bloków, dopóki to zdarzenie nie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="776d8-530">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="776d8-531">Host wykonuje wdzięku zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="776d8-531">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="776d8-532">Żądania mogą nadal być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="776d8-532">Requests may still be processing.</span></span> <span data-ttu-id="776d8-533">Zamykanie bloków, dopóki to zdarzenie nie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="776d8-533">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="776d8-534">Konstruktor-wstrzyknąć <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> usługę do dowolnej klasy.</span><span class="sxs-lookup"><span data-stu-id="776d8-534">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="776d8-535">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) używa iniekcji konstruktora `LifetimeEventsHostedService` do klasy <xref:Microsoft.Extensions.Hosting.IHostedService> (implementacji), aby zarejestrować zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="776d8-535">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="776d8-536">*LifetimeEventsHostedService.cs:*</span><span class="sxs-lookup"><span data-stu-id="776d8-536">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="776d8-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>żąda rozwiązania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="776d8-538">Następująca klasa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> używa do bezpiecznie zamknąć aplikację, `Shutdown` gdy wywoływana jest metoda klasy:</span><span class="sxs-lookup"><span data-stu-id="776d8-538">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

<span data-ttu-id="776d8-539">Szablony ASP.NET Core tworzą hosta ogólnego .NET<xref:Microsoft.Extensions.Hosting.HostBuilder>Core ( ).</span><span class="sxs-lookup"><span data-stu-id="776d8-539">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="776d8-540">Definicja hosta</span><span class="sxs-lookup"><span data-stu-id="776d8-540">Host definition</span></span>

<span data-ttu-id="776d8-541">*Host* jest obiektem, który hermetyzuje zasoby aplikacji, takie jak:</span><span class="sxs-lookup"><span data-stu-id="776d8-541">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="776d8-542">Wstrzyknięcie zależności (DI)</span><span class="sxs-lookup"><span data-stu-id="776d8-542">Dependency injection (DI)</span></span>
* <span data-ttu-id="776d8-543">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="776d8-543">Logging</span></span>
* <span data-ttu-id="776d8-544">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="776d8-544">Configuration</span></span>
* <span data-ttu-id="776d8-545">`IHostedService`Implementacje</span><span class="sxs-lookup"><span data-stu-id="776d8-545">`IHostedService` implementations</span></span>

<span data-ttu-id="776d8-546">Po uruchomieniu hosta wywołuje `IHostedService.StartAsync` każdą <xref:Microsoft.Extensions.Hosting.IHostedService> implementację, która znajduje się w kontenerze DI.</span><span class="sxs-lookup"><span data-stu-id="776d8-546">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="776d8-547">W aplikacji sieci web `IHostedService` jedną z implementacji jest usługa sieci web, która uruchamia [implementację serwera HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="776d8-547">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="776d8-548">Głównym powodem włączenia wszystkich współzależnych zasobów aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: kontrola nad uruchamianiem aplikacji i wdzięczne zamykanie.</span><span class="sxs-lookup"><span data-stu-id="776d8-548">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="776d8-549">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="776d8-549">Set up a host</span></span>

<span data-ttu-id="776d8-550">Host jest zazwyczaj skonfigurowany, zbudowany i uruchamiany `Program` przez kod w klasie.</span><span class="sxs-lookup"><span data-stu-id="776d8-550">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="776d8-551">Metoda: `Main`</span><span class="sxs-lookup"><span data-stu-id="776d8-551">The `Main` method:</span></span>

* <span data-ttu-id="776d8-552">Wywołuje `CreateHostBuilder` metodę tworzenia i konfigurowania obiektu konstruktora.</span><span class="sxs-lookup"><span data-stu-id="776d8-552">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="776d8-553">`Build` Wywołania `Run` i metody na obiekcie konstruktora.</span><span class="sxs-lookup"><span data-stu-id="776d8-553">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="776d8-554">Szablony sieci Web ASP.NET Core generują następujący kod w celu utworzenia hosta:</span><span class="sxs-lookup"><span data-stu-id="776d8-554">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="776d8-555">Poniższy kod tworzy obciążenie inne `IHostedService` niż HTTP z implementacją dodaną do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="776d8-555">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="776d8-556">W przypadku obciążenia `Main` HTTP metoda jest `CreateHostBuilder` `ConfigureWebHostDefaults`taka sama, ale wywołuje:</span><span class="sxs-lookup"><span data-stu-id="776d8-556">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="776d8-557">Jeśli aplikacja używa entity framework core, nie należy zmieniać `CreateHostBuilder` nazwę lub podpis metody.</span><span class="sxs-lookup"><span data-stu-id="776d8-557">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="776d8-558">[Narzędzia Entity Framework Core](/ef/core/miscellaneous/cli/) oczekują, aby znaleźć `CreateHostBuilder` metodę, która konfiguruje hosta bez uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-558">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="776d8-559">Aby uzyskać więcej informacji, zobacz [Tworzenie dbcontext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="776d8-559">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="776d8-560">Domyślne ustawienia konstruktora</span><span class="sxs-lookup"><span data-stu-id="776d8-560">Default builder settings</span></span>

<span data-ttu-id="776d8-561">Metoda: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="776d8-561">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="776d8-562">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na <xref:System.IO.Directory.GetCurrentDirectory*>ścieżkę zwróconą przez program .</span><span class="sxs-lookup"><span data-stu-id="776d8-562">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="776d8-563">Ładuje konfigurację hosta z:</span><span class="sxs-lookup"><span data-stu-id="776d8-563">Loads host configuration from:</span></span>
  * <span data-ttu-id="776d8-564">Zmienne środowiskowe poprzedzone . `DOTNET_`</span><span class="sxs-lookup"><span data-stu-id="776d8-564">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="776d8-565">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="776d8-565">Command-line arguments.</span></span>
* <span data-ttu-id="776d8-566">Ładuje konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="776d8-566">Loads app configuration from:</span></span>
  * <span data-ttu-id="776d8-567">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="776d8-567">*appsettings.json*.</span></span>
  * <span data-ttu-id="776d8-568">*appsettings. {Środowisko}.json*.</span><span class="sxs-lookup"><span data-stu-id="776d8-568">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="776d8-569">[Tajny menedżer,](xref:security/app-secrets) gdy aplikacja `Development` działa w środowisku.</span><span class="sxs-lookup"><span data-stu-id="776d8-569">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="776d8-570">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="776d8-570">Environment variables.</span></span>
  * <span data-ttu-id="776d8-571">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="776d8-571">Command-line arguments.</span></span>
* <span data-ttu-id="776d8-572">Dodaje następujących dostawców [rejestrowania:](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="776d8-572">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="776d8-573">Konsola</span><span class="sxs-lookup"><span data-stu-id="776d8-573">Console</span></span>
  * <span data-ttu-id="776d8-574">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="776d8-574">Debug</span></span>
  * <span data-ttu-id="776d8-575">EventSource</span><span class="sxs-lookup"><span data-stu-id="776d8-575">EventSource</span></span>
  * <span data-ttu-id="776d8-576">EventLog (tylko w przypadku pracy w systemie Windows)</span><span class="sxs-lookup"><span data-stu-id="776d8-576">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="776d8-577">Włącza [sprawdzanie poprawności zakresu](xref:fundamentals/dependency-injection#scope-validation) i sprawdzania poprawności [zależności,](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) gdy środowisko jest rozwój.</span><span class="sxs-lookup"><span data-stu-id="776d8-577">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="776d8-578">Metoda: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="776d8-578">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="776d8-579">Ładuje konfigurację hosta ze zmiennych środowiskowych poprzedzonych programem `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="776d8-579">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="776d8-580">Ustawia serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci web i konfiguruje go przy użyciu dostawców konfiguracji hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-580">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="776d8-581">Aby zapoznać się z domyślnymi <xref:fundamentals/servers/kestrel#kestrel-options>opcjami serwera Kestrel, zobacz .</span><span class="sxs-lookup"><span data-stu-id="776d8-581">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="776d8-582">Dodaje [oprogramowanie pośredniczące filtrowania hosta](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="776d8-582">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="776d8-583">Dodaje [oprogramowanie pośredniczące nagłówków przesyłanych dalej,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest `true`równe .</span><span class="sxs-lookup"><span data-stu-id="776d8-583">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="776d8-584">Umożliwia integrację z usługiami IIS.</span><span class="sxs-lookup"><span data-stu-id="776d8-584">Enables IIS integration.</span></span> <span data-ttu-id="776d8-585">Aby zapoznać się z <xref:host-and-deploy/iis/index#iis-options>domyślnymi opcjami iis, zobacz .</span><span class="sxs-lookup"><span data-stu-id="776d8-585">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="776d8-586">W sekcji [Ustawienia dla wszystkich typów aplikacji](#settings-for-all-app-types) i Ustawienia aplikacji sieci [Web](#settings-for-web-apps) w dalszej części tego artykułu pokazano, jak zastąpić domyślne ustawienia konstruktora.</span><span class="sxs-lookup"><span data-stu-id="776d8-586">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="776d8-587">Usługi świadczone w ramach ram</span><span class="sxs-lookup"><span data-stu-id="776d8-587">Framework-provided services</span></span>

<span data-ttu-id="776d8-588">Następujące usługi są rejestrowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="776d8-588">The following services are registered automatically:</span></span>

* [<span data-ttu-id="776d8-589">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="776d8-589">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="776d8-590">IHostLifetime (czas pracy)</span><span class="sxs-lookup"><span data-stu-id="776d8-590">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="776d8-591">IHostEnvironment / IWebHostŚrodowicielenie</span><span class="sxs-lookup"><span data-stu-id="776d8-591">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="776d8-592">Aby uzyskać więcej informacji na <xref:fundamentals/dependency-injection#framework-provided-services>temat usług świadczonych w ramach ram, zobacz .</span><span class="sxs-lookup"><span data-stu-id="776d8-592">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="776d8-593">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="776d8-593">IHostApplicationLifetime</span></span>

<span data-ttu-id="776d8-594"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Wstrzyknąć `IApplicationLifetime`(dawniej) usługi do dowolnej klasy do obsługi zadań po starcie i wdzięku zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="776d8-594">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="776d8-595">Trzy właściwości w interfejsie są tokeny anulowania używane do rejestrowania uruchamiania aplikacji i metody obsługi zdarzeń zatrzymania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-595">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="776d8-596">Interfejs zawiera również `StopApplication` metodę.</span><span class="sxs-lookup"><span data-stu-id="776d8-596">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="776d8-597">Poniższy przykład `IHostedService` jest implementacją, która rejestruje `IHostApplicationLifetime` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="776d8-597">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="776d8-598">IHostLifetime (czas pracy)</span><span class="sxs-lookup"><span data-stu-id="776d8-598">IHostLifetime</span></span>

<span data-ttu-id="776d8-599">Implementacja <xref:Microsoft.Extensions.Hosting.IHostLifetime> kontroluje, kiedy host uruchamia się i kiedy zatrzymuje.</span><span class="sxs-lookup"><span data-stu-id="776d8-599">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="776d8-600">Używana jest ostatnia zarejestrowana implementacja.</span><span class="sxs-lookup"><span data-stu-id="776d8-600">The last implementation registered is used.</span></span>

<span data-ttu-id="776d8-601">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`jest domyślną `IHostLifetime` implementacją.</span><span class="sxs-lookup"><span data-stu-id="776d8-601">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="776d8-602">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="776d8-602">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="776d8-603">Nasłuchuje <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM i wywołuje, <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="776d8-603">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="776d8-604">Odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="776d8-604">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="776d8-605">IHostŚrodowienie</span><span class="sxs-lookup"><span data-stu-id="776d8-605">IHostEnvironment</span></span>

<span data-ttu-id="776d8-606">Wstrzyknąć <xref:Microsoft.Extensions.Hosting.IHostEnvironment> usługę do klasy, aby uzyskać informacje o następujących ustawieniach:</span><span class="sxs-lookup"><span data-stu-id="776d8-606">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="776d8-607">Applicationname</span><span class="sxs-lookup"><span data-stu-id="776d8-607">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="776d8-608">Nazwa środowiska</span><span class="sxs-lookup"><span data-stu-id="776d8-608">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="776d8-609">Ścieżka contentrootpath</span><span class="sxs-lookup"><span data-stu-id="776d8-609">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="776d8-610">Aplikacje sieci `IWebHostEnvironment` Web implementują `IHostEnvironment` interfejs, który dziedziczy i dodaje [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="776d8-610">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="776d8-611">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="776d8-611">Host configuration</span></span>

<span data-ttu-id="776d8-612">Konfiguracja hosta jest używana <xref:Microsoft.Extensions.Hosting.IHostEnvironment> dla właściwości implementacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-612">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="776d8-613">Konfiguracja hosta jest dostępna w <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>pliku [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) wewnątrz .</span><span class="sxs-lookup"><span data-stu-id="776d8-613">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="776d8-614">Po `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , jest zastępowany config aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-614">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="776d8-615">Aby dodać konfigurację <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`hosta, zadzwoń .</span><span class="sxs-lookup"><span data-stu-id="776d8-615">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="776d8-616">`ConfigureHostConfiguration`można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="776d8-616">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="776d8-617">Host używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="776d8-617">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="776d8-618">Dostawca zmiennych środowiskowych `DOTNET_` z prefiksem i `CreateDefaultBuilder`argumentami wiersza polecenia są uwzględniane przez program .</span><span class="sxs-lookup"><span data-stu-id="776d8-618">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="776d8-619">W przypadku aplikacji sieci web dodawany jest dostawca zmiennych środowiskowych z prefiksem. `ASPNETCORE_`</span><span class="sxs-lookup"><span data-stu-id="776d8-619">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="776d8-620">Prefiks jest usuwany podczas odczytu zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="776d8-620">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="776d8-621">Na przykład wartość zmiennej `ASPNETCORE_ENVIRONMENT` środowiskowej dla staje `environment` się wartością konfiguracji hosta dla klucza.</span><span class="sxs-lookup"><span data-stu-id="776d8-621">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="776d8-622">W poniższym przykładzie tworzy konfigurację hosta:</span><span class="sxs-lookup"><span data-stu-id="776d8-622">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="776d8-623">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="776d8-623">App configuration</span></span>

<span data-ttu-id="776d8-624">Konfiguracja aplikacji jest <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder`tworzona przez wywołanie .</span><span class="sxs-lookup"><span data-stu-id="776d8-624">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="776d8-625">`ConfigureAppConfiguration`można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="776d8-625">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="776d8-626">Aplikacja używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="776d8-626">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="776d8-627">Konfiguracja utworzona `ConfigureAppConfiguration` przez jest dostępna w [witrynie HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) dla kolejnych operacji i jako usługa z DI.</span><span class="sxs-lookup"><span data-stu-id="776d8-627">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="776d8-628">Konfiguracja hosta jest również dodawana do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-628">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="776d8-629">Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="776d8-629">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="776d8-630">Ustawienia dla wszystkich typów aplikacji</span><span class="sxs-lookup"><span data-stu-id="776d8-630">Settings for all app types</span></span>

<span data-ttu-id="776d8-631">W tej sekcji wymieniono ustawienia hosta, które dotyczą zarówno obciążeń HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="776d8-631">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="776d8-632">Domyślnie zmienne środowiskowe używane do konfigurowania `DOTNET_` `ASPNETCORE_` tych ustawień mogą mieć lub prefiks.</span><span class="sxs-lookup"><span data-stu-id="776d8-632">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="776d8-633">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="776d8-633">ApplicationName</span></span>

<span data-ttu-id="776d8-634">[Właściwość IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) jest ustawiana z konfiguracji hosta podczas konstruowaniu hosta.</span><span class="sxs-lookup"><span data-stu-id="776d8-634">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="776d8-635">**Klucz:**`applicationName`</span><span class="sxs-lookup"><span data-stu-id="776d8-635">**Key**: `applicationName`</span></span>  
<span data-ttu-id="776d8-636">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-636">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-637">**Domyślnie**: Nazwa zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-637">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="776d8-638">**Zmienna środowiskowa:**`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="776d8-638">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="776d8-639">Aby ustawić tę wartość, należy użyć zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="776d8-639">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="776d8-640">ContentRoot (Typ treści)</span><span class="sxs-lookup"><span data-stu-id="776d8-640">ContentRoot</span></span>

<span data-ttu-id="776d8-641">[Właściwość IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="776d8-641">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="776d8-642">Jeśli ścieżka nie istnieje, host nie uruchamia się.</span><span class="sxs-lookup"><span data-stu-id="776d8-642">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="776d8-643">**Klucz:**`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="776d8-643">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="776d8-644">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-644">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-645">**Domyślnie**: Folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-645">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="776d8-646">**Zmienna środowiskowa:**`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="776d8-646">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="776d8-647">Aby ustawić tę wartość, należy `UseContentRoot` użyć `IHostBuilder`zmiennej środowiskowej lub wywołać na:</span><span class="sxs-lookup"><span data-stu-id="776d8-647">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="776d8-648">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="776d8-648">For more information, see:</span></span>

* [<span data-ttu-id="776d8-649">Podstawy: Katalog główny treści</span><span class="sxs-lookup"><span data-stu-id="776d8-649">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="776d8-650">Webroot</span><span class="sxs-lookup"><span data-stu-id="776d8-650">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="776d8-651">Nazwa środowiska</span><span class="sxs-lookup"><span data-stu-id="776d8-651">EnvironmentName</span></span>

<span data-ttu-id="776d8-652">[Właściwość IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) można ustawić na dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="776d8-652">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="776d8-653">Wartości zdefiniowane `Development`przez `Staging`ramy `Production`obejmują , i .</span><span class="sxs-lookup"><span data-stu-id="776d8-653">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="776d8-654">W wartościach nie rozróżnia się wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="776d8-654">Values aren't case-sensitive.</span></span>

<span data-ttu-id="776d8-655">**Klucz:**`environment`</span><span class="sxs-lookup"><span data-stu-id="776d8-655">**Key**: `environment`</span></span>  
<span data-ttu-id="776d8-656">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-656">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-657">**Domyślnie**:`Production`</span><span class="sxs-lookup"><span data-stu-id="776d8-657">**Default**: `Production`</span></span>  
<span data-ttu-id="776d8-658">**Zmienna środowiskowa:**`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="776d8-658">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="776d8-659">Aby ustawić tę wartość, należy `UseEnvironment` użyć `IHostBuilder`zmiennej środowiskowej lub wywołać na:</span><span class="sxs-lookup"><span data-stu-id="776d8-659">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="776d8-660">Shutdowntimeout</span><span class="sxs-lookup"><span data-stu-id="776d8-660">ShutdownTimeout</span></span>

<span data-ttu-id="776d8-661">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) ustawia limit <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>czasu dla .</span><span class="sxs-lookup"><span data-stu-id="776d8-661">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="776d8-662">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="776d8-662">The default value is five seconds.</span></span>  <span data-ttu-id="776d8-663">W okresie limitu czasu host:</span><span class="sxs-lookup"><span data-stu-id="776d8-663">During the timeout period, the host:</span></span>

* <span data-ttu-id="776d8-664">Wyzwala [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="776d8-664">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="776d8-665">Próbuje zatrzymać hostowane usługi, rejestrowanie błędów dla usług, które nie mogą się zatrzymać.</span><span class="sxs-lookup"><span data-stu-id="776d8-665">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="776d8-666">Jeśli limit czasu wygaśnie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-666">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="776d8-667">Usługi zatrzymują się nawet wtedy, gdy nie zakończyły przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="776d8-667">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="776d8-668">Jeśli usługi wymagają dodatkowego czasu, aby zatrzymać, zwiększyć limit czasu.</span><span class="sxs-lookup"><span data-stu-id="776d8-668">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="776d8-669">**Klucz:**`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="776d8-669">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="776d8-670">**Typ**:`int`</span><span class="sxs-lookup"><span data-stu-id="776d8-670">**Type**: `int`</span></span>  
<span data-ttu-id="776d8-671">**Domyślnie**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="776d8-671">**Default**: 5 seconds</span></span>  
<span data-ttu-id="776d8-672">**Zmienna środowiskowa:**`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="776d8-672">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="776d8-673">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub skonfiguruj `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="776d8-673">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="776d8-674">W poniższym przykładzie ustawia limit czasu na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="776d8-674">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="776d8-675">Wyłącz ponowne ładowanie konfiguracji aplikacji przy zmianie</span><span class="sxs-lookup"><span data-stu-id="776d8-675">Disable app configuration reload on change</span></span>

<span data-ttu-id="776d8-676">[Domyślnie](xref:fundamentals/configuration/index#default), *appsettings.json* i *appsettings.{ Środowisko}.json* są ponownie ładowane po zmianie pliku.</span><span class="sxs-lookup"><span data-stu-id="776d8-676">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="776d8-677">Aby wyłączyć to zachowanie przeładowania w ASP.NET Core 5.0 Preview `hostBuilder:reloadConfigOnChange` 3 lub nowszym, ustaw klawisz na `false`.</span><span class="sxs-lookup"><span data-stu-id="776d8-677">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="776d8-678">**Klucz:**`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="776d8-678">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="776d8-679">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="776d8-679">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="776d8-680">**Domyślnie**:`true`</span><span class="sxs-lookup"><span data-stu-id="776d8-680">**Default**: `true`</span></span>  
<span data-ttu-id="776d8-681">**Argument wiersza polecenia:**`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="776d8-681">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="776d8-682">**Zmienna środowiskowa:**`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="776d8-682">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="776d8-683">Separator`:`dwukropka ( ) nie działa z kluczami hierarchicznymi zmiennych środowiskowych na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="776d8-683">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="776d8-684">Aby uzyskać więcej informacji, zobacz [Zmienne środowiskowe](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="776d8-684">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="776d8-685">Ustawienia aplikacji internetowych</span><span class="sxs-lookup"><span data-stu-id="776d8-685">Settings for web apps</span></span>

<span data-ttu-id="776d8-686">Niektóre ustawienia hosta dotyczą tylko obciążeń HTTP.</span><span class="sxs-lookup"><span data-stu-id="776d8-686">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="776d8-687">Domyślnie zmienne środowiskowe używane do konfigurowania `DOTNET_` `ASPNETCORE_` tych ustawień mogą mieć lub prefiks.</span><span class="sxs-lookup"><span data-stu-id="776d8-687">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="776d8-688">Dostępne `IWebHostBuilder` są metody rozszerzenia dla tych ustawień.</span><span class="sxs-lookup"><span data-stu-id="776d8-688">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="776d8-689">Przykłady kodu, które pokazują, jak `webBuilder` wywołać `IWebHostBuilder`metody rozszerzenia zakłada, że jest wystąpieniem , jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="776d8-689">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="776d8-690">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="776d8-690">CaptureStartupErrors</span></span>

<span data-ttu-id="776d8-691">Kiedy `false`, błędy podczas uruchamiania spowodować wyjście hosta.</span><span class="sxs-lookup"><span data-stu-id="776d8-691">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="776d8-692">Gdy `true`host przechwytuje wyjątki podczas uruchamiania i próbuje uruchomić serwer.</span><span class="sxs-lookup"><span data-stu-id="776d8-692">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="776d8-693">**Klucz:**`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="776d8-693">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="776d8-694">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="776d8-694">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="776d8-695">**Domyślnie:** `false` Domyślnie, chyba że aplikacja działa z Kestrel `true`za IIS, gdzie domyślnie jest .</span><span class="sxs-lookup"><span data-stu-id="776d8-695">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="776d8-696">**Zmienna środowiskowa:**`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="776d8-696">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="776d8-697">Aby ustawić tę wartość, `CaptureStartupErrors`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="776d8-697">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="776d8-698">Szczegółoweerrory</span><span class="sxs-lookup"><span data-stu-id="776d8-698">DetailedErrors</span></span>

<span data-ttu-id="776d8-699">Po włączeniu lub gdy `Development`środowisko jest , aplikacja przechwytuje szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="776d8-699">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="776d8-700">**Klucz:**`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="776d8-700">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="776d8-701">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="776d8-701">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="776d8-702">**Domyślnie**:`false`</span><span class="sxs-lookup"><span data-stu-id="776d8-702">**Default**: `false`</span></span>  
<span data-ttu-id="776d8-703">**Zmienna środowiskowa:**`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="776d8-703">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="776d8-704">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="776d8-704">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="776d8-705">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="776d8-705">HostingStartupAssemblies</span></span>

<span data-ttu-id="776d8-706">Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do załadowania podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="776d8-706">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="776d8-707">Mimo że wartość konfiguracji domyślnie pusty ciąg, zestawy uruchamiania hostingu zawsze zawierają zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-707">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="776d8-708">Po udostępnieniu zestawów uruchamiania hostingu są one dodawane do zestawu aplikacji do ładowania, gdy aplikacja tworzy swoje typowe usługi podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="776d8-708">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="776d8-709">**Klucz:**`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="776d8-709">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="776d8-710">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-710">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-711">**Domyślnie**: Pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="776d8-711">**Default**: Empty string</span></span>  
<span data-ttu-id="776d8-712">**Zmienna środowiskowa:**`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="776d8-712">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="776d8-713">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="776d8-713">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="776d8-714">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="776d8-714">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="776d8-715">Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do wykluczenia podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="776d8-715">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="776d8-716">**Klucz:**`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="776d8-716">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="776d8-717">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-717">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-718">**Domyślnie**: Pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="776d8-718">**Default**: Empty string</span></span>  
<span data-ttu-id="776d8-719">**Zmienna środowiskowa:**`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="776d8-719">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="776d8-720">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="776d8-720">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="776d8-721">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="776d8-721">HTTPS_Port</span></span>

<span data-ttu-id="776d8-722">Port przekierowania HTTPS.</span><span class="sxs-lookup"><span data-stu-id="776d8-722">The HTTPS redirect port.</span></span> <span data-ttu-id="776d8-723">Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="776d8-723">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="776d8-724">**Klucz:**`https_port`</span><span class="sxs-lookup"><span data-stu-id="776d8-724">**Key**: `https_port`</span></span>  
<span data-ttu-id="776d8-725">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-725">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-726">**Domyślnie:** Wartość domyślna nie jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="776d8-726">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="776d8-727">**Zmienna środowiskowa:**`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="776d8-727">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="776d8-728">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="776d8-728">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="776d8-729">PreferHostingUrls (PreferHostingUrls)</span><span class="sxs-lookup"><span data-stu-id="776d8-729">PreferHostingUrls</span></span>

<span data-ttu-id="776d8-730">Wskazuje, czy host powinien nasłuchiwania adresów URL skonfigurowanych przy 200 000 `IWebHostBuilder` adresów URL skonfigurowanych z `IServer` implementacją.</span><span class="sxs-lookup"><span data-stu-id="776d8-730">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="776d8-731">**Klucz:**`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="776d8-731">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="776d8-732">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="776d8-732">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="776d8-733">**Domyślnie**:`true`</span><span class="sxs-lookup"><span data-stu-id="776d8-733">**Default**: `true`</span></span>  
<span data-ttu-id="776d8-734">**Zmienna środowiskowa:**`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="776d8-734">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="776d8-735">Aby ustawić tę wartość, użyj `PreferHostingUrls`zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="776d8-735">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="776d8-736">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="776d8-736">PreventHostingStartup</span></span>

<span data-ttu-id="776d8-737">Zapobiega automatycznemu obciążaniu zestawów uruchamiania hostingu, w tym zestawów uruchamiania hostingu skonfigurowanych przez zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-737">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="776d8-738">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="776d8-738">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="776d8-739">**Klucz:**`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="776d8-739">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="776d8-740">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="776d8-740">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="776d8-741">**Domyślnie**:`false`</span><span class="sxs-lookup"><span data-stu-id="776d8-741">**Default**: `false`</span></span>  
<span data-ttu-id="776d8-742">**Zmienna środowiskowa:**`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="776d8-742">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="776d8-743">Aby ustawić tę wartość, użyj `UseSetting` zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="776d8-743">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="776d8-744">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="776d8-744">StartupAssembly</span></span>

<span data-ttu-id="776d8-745">Zestaw do wyszukiwania `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="776d8-745">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="776d8-746">**Klucz:**`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="776d8-746">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="776d8-747">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-747">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-748">**Domyślnie**: Zespół aplikacji</span><span class="sxs-lookup"><span data-stu-id="776d8-748">**Default**: The app's assembly</span></span>  
<span data-ttu-id="776d8-749">**Zmienna środowiskowa:**`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="776d8-749">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="776d8-750">Aby ustawić tę wartość, należy `UseStartup`użyć zmiennej środowiskowej lub wywołania .</span><span class="sxs-lookup"><span data-stu-id="776d8-750">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="776d8-751">`UseStartup`może przyjąć nazwę`string`zestawu ( )`TStartup`lub typ ( ).</span><span class="sxs-lookup"><span data-stu-id="776d8-751">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="776d8-752">Jeśli `UseStartup` wywoływanych jest wiele metod, pierwszeństwo ma ostatnia.</span><span class="sxs-lookup"><span data-stu-id="776d8-752">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="776d8-753">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="776d8-753">URLs</span></span>

<span data-ttu-id="776d8-754">Lista adresów IP lub adresów hostów rozdzielanych średnikami z portami i protokołami, na których serwer powinien nasłuchiwał żądań.</span><span class="sxs-lookup"><span data-stu-id="776d8-754">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="776d8-755">Na przykład `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="776d8-755">For example, `http://localhost:123`.</span></span> <span data-ttu-id="776d8-756">Użyj\*" ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwach hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="776d8-756">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="776d8-757">Protokół (`http://` `https://`lub ) musi być dołączony do każdego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="776d8-757">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="776d8-758">Obsługiwane formaty różnią się w zależności od serwera.</span><span class="sxs-lookup"><span data-stu-id="776d8-758">Supported formats vary among servers.</span></span>

<span data-ttu-id="776d8-759">**Klucz:**`urls`</span><span class="sxs-lookup"><span data-stu-id="776d8-759">**Key**: `urls`</span></span>  
<span data-ttu-id="776d8-760">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-760">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-761">**Domyślnie**: `http://localhost:5000` i`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="776d8-761">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="776d8-762">**Zmienna środowiskowa:**`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="776d8-762">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="776d8-763">Aby ustawić tę wartość, użyj `UseUrls`zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="776d8-763">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="776d8-764">Pustułka ma własny interfejs API konfiguracji punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="776d8-764">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="776d8-765">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="776d8-765">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="776d8-766">Webroot</span><span class="sxs-lookup"><span data-stu-id="776d8-766">WebRoot</span></span>

<span data-ttu-id="776d8-767">[Właściwość IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) określa względną ścieżkę do statycznych zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-767">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="776d8-768">Jeśli ścieżka nie istnieje, używany jest dostawca plików bez operacji.</span><span class="sxs-lookup"><span data-stu-id="776d8-768">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="776d8-769">**Klucz:**`webroot`</span><span class="sxs-lookup"><span data-stu-id="776d8-769">**Key**: `webroot`</span></span>  
<span data-ttu-id="776d8-770">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="776d8-770">**Type**: `string`</span></span>  
<span data-ttu-id="776d8-771">**Domyślnie**: `wwwroot`Wartość domyślna to .</span><span class="sxs-lookup"><span data-stu-id="776d8-771">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="776d8-772">Musi istnieć ścieżka do *{content root}/wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="776d8-772">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="776d8-773">**Zmienna środowiskowa:**`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="776d8-773">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="776d8-774">Aby ustawić tę wartość, należy `UseWebRoot` użyć `IWebHostBuilder`zmiennej środowiskowej lub wywołać na:</span><span class="sxs-lookup"><span data-stu-id="776d8-774">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="776d8-775">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="776d8-775">For more information, see:</span></span>

* [<span data-ttu-id="776d8-776">Podstawy: Korzeń sieci Web</span><span class="sxs-lookup"><span data-stu-id="776d8-776">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="776d8-777">ContentRoot (Typ treści)</span><span class="sxs-lookup"><span data-stu-id="776d8-777">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="776d8-778">Zarządzanie okresem istnienia hosta</span><span class="sxs-lookup"><span data-stu-id="776d8-778">Manage the host lifetime</span></span>

<span data-ttu-id="776d8-779">Wywołaj metody <xref:Microsoft.Extensions.Hosting.IHost> na wbudowanej implementacji, aby uruchomić i zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="776d8-779">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="776d8-780">Te metody <xref:Microsoft.Extensions.Hosting.IHostedService> mają wpływ na wszystkie implementacje, które są zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="776d8-780">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="776d8-781">Run</span><span class="sxs-lookup"><span data-stu-id="776d8-781">Run</span></span>

<span data-ttu-id="776d8-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący, dopóki host nie zostanie zamknięty.</span><span class="sxs-lookup"><span data-stu-id="776d8-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="776d8-783">RunAsync ( RunAsync )</span><span class="sxs-lookup"><span data-stu-id="776d8-783">RunAsync</span></span>

<span data-ttu-id="776d8-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia systemu.</span><span class="sxs-lookup"><span data-stu-id="776d8-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="776d8-785">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="776d8-785">RunConsoleAsync</span></span>

<span data-ttu-id="776d8-786"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>umożliwia obsługę konsoli, buduje i uruchamia hosta i czeka na <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM, aby zamknąć.</span><span class="sxs-lookup"><span data-stu-id="776d8-786"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="776d8-787">Uruchamianie</span><span class="sxs-lookup"><span data-stu-id="776d8-787">Start</span></span>

<span data-ttu-id="776d8-788"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="776d8-788"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="776d8-789">StartAsync (StartAsync)</span><span class="sxs-lookup"><span data-stu-id="776d8-789">StartAsync</span></span>

<span data-ttu-id="776d8-790"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia hosta i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="776d8-790"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="776d8-791"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na `StartAsync`początku , który czeka, aż zakończy się przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="776d8-791"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="776d8-792">Może to służyć do opóźnienia uruchamiania, dopóki nie zostanie zasygnaligozowana przez zdarzenie zewnętrzne.</span><span class="sxs-lookup"><span data-stu-id="776d8-792">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="776d8-793">StopAsync (StopAsync)</span><span class="sxs-lookup"><span data-stu-id="776d8-793">StopAsync</span></span>

<span data-ttu-id="776d8-794"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w podanym limit czasu.</span><span class="sxs-lookup"><span data-stu-id="776d8-794"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="776d8-795">WaitForShutdown (WaitForShutdown)</span><span class="sxs-lookup"><span data-stu-id="776d8-795">WaitForShutdown</span></span>

<span data-ttu-id="776d8-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje wątek wywołujący, dopóki zamknięcie zostanie wywołane przez IHostLifetime, na przykład przez <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="776d8-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="776d8-797">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="776d8-797">WaitForShutdownAsync</span></span>

<span data-ttu-id="776d8-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task> zwraca, który kończy się po zamknięciu jest <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>wyzwalany za pośrednictwem danego tokenu i wywołania .</span><span class="sxs-lookup"><span data-stu-id="776d8-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="776d8-799">Sterowanie zewnętrzne</span><span class="sxs-lookup"><span data-stu-id="776d8-799">External control</span></span>

<span data-ttu-id="776d8-800">Bezpośrednią kontrolę okresu istnienia hosta można osiągnąć za pomocą metod, które można wywołać zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="776d8-800">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="776d8-801">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="776d8-801">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
