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
# <a name="net-generic-host"></a><span data-ttu-id="658a6-103">Host ogólny platformy .NET</span><span class="sxs-lookup"><span data-stu-id="658a6-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="658a6-104">Szablony ASP.NET Core tworzą hosta ogólnego .NET Core, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="658a6-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="658a6-105">Definicja hosta</span><span class="sxs-lookup"><span data-stu-id="658a6-105">Host definition</span></span>

<span data-ttu-id="658a6-106">*Host* jest obiektem, który hermetyzuje zasoby aplikacji, takie jak:</span><span class="sxs-lookup"><span data-stu-id="658a6-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="658a6-107">Wstrzyknięcie zależności (DI)</span><span class="sxs-lookup"><span data-stu-id="658a6-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="658a6-108">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="658a6-108">Logging</span></span>
* <span data-ttu-id="658a6-109">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="658a6-109">Configuration</span></span>
* <span data-ttu-id="658a6-110">`IHostedService`Implementacje</span><span class="sxs-lookup"><span data-stu-id="658a6-110">`IHostedService` implementations</span></span>

<span data-ttu-id="658a6-111">Po uruchomieniu hosta wywołuje `IHostedService.StartAsync` każdą <xref:Microsoft.Extensions.Hosting.IHostedService> implementację, która znajduje się w kontenerze DI.</span><span class="sxs-lookup"><span data-stu-id="658a6-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="658a6-112">W aplikacji sieci web `IHostedService` jedną z implementacji jest usługa sieci web, która uruchamia [implementację serwera HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="658a6-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="658a6-113">Głównym powodem włączenia wszystkich współzależnych zasobów aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: kontrola nad uruchamianiem aplikacji i wdzięczne zamykanie.</span><span class="sxs-lookup"><span data-stu-id="658a6-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="658a6-114">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="658a6-114">Set up a host</span></span>

<span data-ttu-id="658a6-115">Host jest zazwyczaj skonfigurowany, zbudowany i uruchamiany `Program` przez kod w klasie.</span><span class="sxs-lookup"><span data-stu-id="658a6-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="658a6-116">Metoda: `Main`</span><span class="sxs-lookup"><span data-stu-id="658a6-116">The `Main` method:</span></span>

* <span data-ttu-id="658a6-117">Wywołuje `CreateHostBuilder` metodę tworzenia i konfigurowania obiektu konstruktora.</span><span class="sxs-lookup"><span data-stu-id="658a6-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="658a6-118">`Build` Wywołania `Run` i metody na obiekcie konstruktora.</span><span class="sxs-lookup"><span data-stu-id="658a6-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="658a6-119">Szablony sieci Web ASP.NET Core generują następujący kod w celu utworzenia hosta ogólnego:</span><span class="sxs-lookup"><span data-stu-id="658a6-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="658a6-120">Poniższy kod tworzy hosta ogólnego przy użyciu obciążenia innego niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="658a6-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="658a6-121">Implementacja `IHostedService` jest dodawana do kontenera DI:</span><span class="sxs-lookup"><span data-stu-id="658a6-121">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="658a6-122">W przypadku obciążenia `Main` HTTP metoda jest `CreateHostBuilder` `ConfigureWebHostDefaults`taka sama, ale wywołuje:</span><span class="sxs-lookup"><span data-stu-id="658a6-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="658a6-123">Poprzedni kod jest generowany przez szablony ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="658a6-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="658a6-124">Jeśli aplikacja używa entity framework core, nie należy zmieniać `CreateHostBuilder` nazwę lub podpis metody.</span><span class="sxs-lookup"><span data-stu-id="658a6-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="658a6-125">[Narzędzia Entity Framework Core](/ef/core/miscellaneous/cli/) oczekują, aby znaleźć `CreateHostBuilder` metodę, która konfiguruje hosta bez uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="658a6-126">Aby uzyskać więcej informacji, zobacz [Tworzenie dbcontext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="658a6-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="658a6-127">Domyślne ustawienia konstruktora</span><span class="sxs-lookup"><span data-stu-id="658a6-127">Default builder settings</span></span>

<span data-ttu-id="658a6-128">Metoda: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="658a6-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="658a6-129">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na <xref:System.IO.Directory.GetCurrentDirectory*>ścieżkę zwróconą przez program .</span><span class="sxs-lookup"><span data-stu-id="658a6-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="658a6-130">Ładuje konfigurację hosta z:</span><span class="sxs-lookup"><span data-stu-id="658a6-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="658a6-131">Zmienne środowiskowe poprzedzone . `DOTNET_`</span><span class="sxs-lookup"><span data-stu-id="658a6-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="658a6-132">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="658a6-132">Command-line arguments.</span></span>
* <span data-ttu-id="658a6-133">Ładuje konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="658a6-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="658a6-134">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="658a6-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="658a6-135">*appsettings. {Środowisko}.json*.</span><span class="sxs-lookup"><span data-stu-id="658a6-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="658a6-136">[Tajny menedżer,](xref:security/app-secrets) gdy aplikacja `Development` działa w środowisku.</span><span class="sxs-lookup"><span data-stu-id="658a6-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="658a6-137">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="658a6-137">Environment variables.</span></span>
  * <span data-ttu-id="658a6-138">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="658a6-138">Command-line arguments.</span></span>
* <span data-ttu-id="658a6-139">Dodaje następujących dostawców [rejestrowania:](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="658a6-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="658a6-140">Konsola</span><span class="sxs-lookup"><span data-stu-id="658a6-140">Console</span></span>
  * <span data-ttu-id="658a6-141">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="658a6-141">Debug</span></span>
  * <span data-ttu-id="658a6-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="658a6-142">EventSource</span></span>
  * <span data-ttu-id="658a6-143">EventLog (tylko w przypadku pracy w systemie Windows)</span><span class="sxs-lookup"><span data-stu-id="658a6-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="658a6-144">Włącza [sprawdzanie poprawności zakresu](xref:fundamentals/dependency-injection#scope-validation) i sprawdzania poprawności [zależności,](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) gdy środowisko jest rozwój.</span><span class="sxs-lookup"><span data-stu-id="658a6-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="658a6-145">Metoda: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="658a6-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="658a6-146">Ładuje konfigurację hosta ze zmiennych środowiskowych poprzedzonych programem `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="658a6-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="658a6-147">Ustawia serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci web i konfiguruje go przy użyciu dostawców konfiguracji hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="658a6-148">Aby zapoznać się z domyślnymi <xref:fundamentals/servers/kestrel#kestrel-options>opcjami serwera Kestrel, zobacz .</span><span class="sxs-lookup"><span data-stu-id="658a6-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="658a6-149">Dodaje [oprogramowanie pośredniczące filtrowania hosta](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="658a6-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="658a6-150">Dodaje [oprogramowanie pośredniczące nagłówków przesyłanych dalej,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest `true`równe .</span><span class="sxs-lookup"><span data-stu-id="658a6-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="658a6-151">Umożliwia integrację z usługiami IIS.</span><span class="sxs-lookup"><span data-stu-id="658a6-151">Enables IIS integration.</span></span> <span data-ttu-id="658a6-152">Aby zapoznać się z <xref:host-and-deploy/iis/index#iis-options>domyślnymi opcjami iis, zobacz .</span><span class="sxs-lookup"><span data-stu-id="658a6-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="658a6-153">W sekcji [Ustawienia dla wszystkich typów aplikacji](#settings-for-all-app-types) i Ustawienia aplikacji sieci [Web](#settings-for-web-apps) w dalszej części tego artykułu pokazano, jak zastąpić domyślne ustawienia konstruktora.</span><span class="sxs-lookup"><span data-stu-id="658a6-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="658a6-154">Usługi świadczone w ramach ram</span><span class="sxs-lookup"><span data-stu-id="658a6-154">Framework-provided services</span></span>

<span data-ttu-id="658a6-155">Następujące usługi są rejestrowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="658a6-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="658a6-156">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="658a6-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="658a6-157">IHostLifetime (czas pracy)</span><span class="sxs-lookup"><span data-stu-id="658a6-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="658a6-158">IHostEnvironment / IWebHostŚrodowicielenie</span><span class="sxs-lookup"><span data-stu-id="658a6-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="658a6-159">Aby uzyskać więcej informacji na <xref:fundamentals/dependency-injection#framework-provided-services>temat usług świadczonych w ramach ram, zobacz .</span><span class="sxs-lookup"><span data-stu-id="658a6-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="658a6-160">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="658a6-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="658a6-161"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Wstrzyknąć `IApplicationLifetime`(dawniej) usługi do dowolnej klasy do obsługi zadań po starcie i wdzięku zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="658a6-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="658a6-162">Trzy właściwości w interfejsie są tokeny anulowania używane do rejestrowania uruchamiania aplikacji i metody obsługi zdarzeń zatrzymania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="658a6-163">Interfejs zawiera również `StopApplication` metodę.</span><span class="sxs-lookup"><span data-stu-id="658a6-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="658a6-164">Poniższy przykład `IHostedService` jest implementacją, która rejestruje `IHostApplicationLifetime` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="658a6-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="658a6-165">IHostLifetime (czas pracy)</span><span class="sxs-lookup"><span data-stu-id="658a6-165">IHostLifetime</span></span>

<span data-ttu-id="658a6-166">Implementacja <xref:Microsoft.Extensions.Hosting.IHostLifetime> kontroluje, kiedy host uruchamia się i kiedy zatrzymuje.</span><span class="sxs-lookup"><span data-stu-id="658a6-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="658a6-167">Używana jest ostatnia zarejestrowana implementacja.</span><span class="sxs-lookup"><span data-stu-id="658a6-167">The last implementation registered is used.</span></span>

<span data-ttu-id="658a6-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`jest domyślną `IHostLifetime` implementacją.</span><span class="sxs-lookup"><span data-stu-id="658a6-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="658a6-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="658a6-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="658a6-170">Nasłuchuje <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM i wywołuje, <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="658a6-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="658a6-171">Odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="658a6-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="658a6-172">IHostŚrodowienie</span><span class="sxs-lookup"><span data-stu-id="658a6-172">IHostEnvironment</span></span>

<span data-ttu-id="658a6-173">Wstrzyknąć <xref:Microsoft.Extensions.Hosting.IHostEnvironment> usługę do klasy, aby uzyskać informacje o następujących ustawieniach:</span><span class="sxs-lookup"><span data-stu-id="658a6-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="658a6-174">Applicationname</span><span class="sxs-lookup"><span data-stu-id="658a6-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="658a6-175">Nazwa środowiska</span><span class="sxs-lookup"><span data-stu-id="658a6-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="658a6-176">Ścieżka contentrootpath</span><span class="sxs-lookup"><span data-stu-id="658a6-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="658a6-177">Aplikacje sieci `IWebHostEnvironment` Web implementują `IHostEnvironment` interfejs, który dziedziczy i dodaje [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="658a6-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="658a6-178">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="658a6-178">Host configuration</span></span>

<span data-ttu-id="658a6-179">Konfiguracja hosta jest używana <xref:Microsoft.Extensions.Hosting.IHostEnvironment> dla właściwości implementacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="658a6-180">Konfiguracja hosta jest dostępna w <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>pliku [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) wewnątrz .</span><span class="sxs-lookup"><span data-stu-id="658a6-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="658a6-181">Po `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , jest zastępowany config aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="658a6-182">Aby dodać konfigurację <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`hosta, zadzwoń .</span><span class="sxs-lookup"><span data-stu-id="658a6-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="658a6-183">`ConfigureHostConfiguration`można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="658a6-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="658a6-184">Host używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="658a6-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="658a6-185">Dostawca zmiennych środowiskowych `DOTNET_` z prefiksem i `CreateDefaultBuilder`argumentami wiersza polecenia są uwzględniane przez program .</span><span class="sxs-lookup"><span data-stu-id="658a6-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="658a6-186">W przypadku aplikacji sieci web dodawany jest dostawca zmiennych środowiskowych z prefiksem. `ASPNETCORE_`</span><span class="sxs-lookup"><span data-stu-id="658a6-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="658a6-187">Prefiks jest usuwany podczas odczytu zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="658a6-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="658a6-188">Na przykład wartość zmiennej `ASPNETCORE_ENVIRONMENT` środowiskowej dla staje `environment` się wartością konfiguracji hosta dla klucza.</span><span class="sxs-lookup"><span data-stu-id="658a6-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="658a6-189">W poniższym przykładzie tworzy konfigurację hosta:</span><span class="sxs-lookup"><span data-stu-id="658a6-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="658a6-190">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="658a6-190">App configuration</span></span>

<span data-ttu-id="658a6-191">Konfiguracja aplikacji jest <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder`tworzona przez wywołanie .</span><span class="sxs-lookup"><span data-stu-id="658a6-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="658a6-192">`ConfigureAppConfiguration`można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="658a6-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="658a6-193">Aplikacja używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="658a6-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="658a6-194">Konfiguracja utworzona `ConfigureAppConfiguration` przez jest dostępna w [witrynie HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) dla kolejnych operacji i jako usługa z DI.</span><span class="sxs-lookup"><span data-stu-id="658a6-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="658a6-195">Konfiguracja hosta jest również dodawana do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="658a6-196">Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="658a6-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="658a6-197">Ustawienia dla wszystkich typów aplikacji</span><span class="sxs-lookup"><span data-stu-id="658a6-197">Settings for all app types</span></span>

<span data-ttu-id="658a6-198">W tej sekcji wymieniono ustawienia hosta, które dotyczą zarówno obciążeń HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="658a6-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="658a6-199">Domyślnie zmienne środowiskowe używane do konfigurowania `DOTNET_` `ASPNETCORE_` tych ustawień mogą mieć lub prefiks.</span><span class="sxs-lookup"><span data-stu-id="658a6-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="658a6-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="658a6-200">ApplicationName</span></span>

<span data-ttu-id="658a6-201">[Właściwość IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) jest ustawiana z konfiguracji hosta podczas konstruowaniu hosta.</span><span class="sxs-lookup"><span data-stu-id="658a6-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="658a6-202">**Klucz:**`applicationName`</span><span class="sxs-lookup"><span data-stu-id="658a6-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="658a6-203">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-203">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-204">**Domyślnie**: Nazwa zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="658a6-205">**Zmienna środowiskowa:**`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="658a6-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="658a6-206">Aby ustawić tę wartość, należy użyć zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="658a6-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="658a6-207">ContentRoot (Typ treści)</span><span class="sxs-lookup"><span data-stu-id="658a6-207">ContentRoot</span></span>

<span data-ttu-id="658a6-208">[Właściwość IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="658a6-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="658a6-209">Jeśli ścieżka nie istnieje, host nie uruchamia się.</span><span class="sxs-lookup"><span data-stu-id="658a6-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="658a6-210">**Klucz:**`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="658a6-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="658a6-211">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-211">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-212">**Domyślnie**: Folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="658a6-213">**Zmienna środowiskowa:**`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="658a6-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="658a6-214">Aby ustawić tę wartość, należy `UseContentRoot` użyć `IHostBuilder`zmiennej środowiskowej lub wywołać na:</span><span class="sxs-lookup"><span data-stu-id="658a6-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="658a6-215">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="658a6-215">For more information, see:</span></span>

* [<span data-ttu-id="658a6-216">Podstawy: Katalog główny treści</span><span class="sxs-lookup"><span data-stu-id="658a6-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="658a6-217">Webroot</span><span class="sxs-lookup"><span data-stu-id="658a6-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="658a6-218">Nazwa środowiska</span><span class="sxs-lookup"><span data-stu-id="658a6-218">EnvironmentName</span></span>

<span data-ttu-id="658a6-219">[Właściwość IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) można ustawić na dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="658a6-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="658a6-220">Wartości zdefiniowane `Development`przez `Staging`ramy `Production`obejmują , i .</span><span class="sxs-lookup"><span data-stu-id="658a6-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="658a6-221">W wartościach nie rozróżnia się wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="658a6-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="658a6-222">**Klucz:**`environment`</span><span class="sxs-lookup"><span data-stu-id="658a6-222">**Key**: `environment`</span></span>  
<span data-ttu-id="658a6-223">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-223">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-224">**Domyślnie**:`Production`</span><span class="sxs-lookup"><span data-stu-id="658a6-224">**Default**: `Production`</span></span>  
<span data-ttu-id="658a6-225">**Zmienna środowiskowa:**`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="658a6-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="658a6-226">Aby ustawić tę wartość, należy `UseEnvironment` użyć `IHostBuilder`zmiennej środowiskowej lub wywołać na:</span><span class="sxs-lookup"><span data-stu-id="658a6-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="658a6-227">Shutdowntimeout</span><span class="sxs-lookup"><span data-stu-id="658a6-227">ShutdownTimeout</span></span>

<span data-ttu-id="658a6-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) ustawia limit <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>czasu dla .</span><span class="sxs-lookup"><span data-stu-id="658a6-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="658a6-229">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="658a6-229">The default value is five seconds.</span></span>  <span data-ttu-id="658a6-230">W okresie limitu czasu host:</span><span class="sxs-lookup"><span data-stu-id="658a6-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="658a6-231">Wyzwala [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="658a6-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="658a6-232">Próbuje zatrzymać hostowane usługi, rejestrowanie błędów dla usług, które nie mogą się zatrzymać.</span><span class="sxs-lookup"><span data-stu-id="658a6-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="658a6-233">Jeśli limit czasu wygaśnie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="658a6-234">Usługi zatrzymują się nawet wtedy, gdy nie zakończyły przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="658a6-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="658a6-235">Jeśli usługi wymagają dodatkowego czasu, aby zatrzymać, zwiększyć limit czasu.</span><span class="sxs-lookup"><span data-stu-id="658a6-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="658a6-236">**Klucz:**`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="658a6-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="658a6-237">**Typ**:`int`</span><span class="sxs-lookup"><span data-stu-id="658a6-237">**Type**: `int`</span></span>  
<span data-ttu-id="658a6-238">**Domyślnie**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="658a6-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="658a6-239">**Zmienna środowiskowa:**`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="658a6-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="658a6-240">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub skonfiguruj `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="658a6-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="658a6-241">W poniższym przykładzie ustawia limit czasu na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="658a6-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="658a6-242">Ustawienia aplikacji internetowych</span><span class="sxs-lookup"><span data-stu-id="658a6-242">Settings for web apps</span></span>

<span data-ttu-id="658a6-243">Niektóre ustawienia hosta dotyczą tylko obciążeń HTTP.</span><span class="sxs-lookup"><span data-stu-id="658a6-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="658a6-244">Domyślnie zmienne środowiskowe używane do konfigurowania `DOTNET_` `ASPNETCORE_` tych ustawień mogą mieć lub prefiks.</span><span class="sxs-lookup"><span data-stu-id="658a6-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="658a6-245">Dostępne `IWebHostBuilder` są metody rozszerzenia dla tych ustawień.</span><span class="sxs-lookup"><span data-stu-id="658a6-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="658a6-246">Przykłady kodu, które pokazują, jak `webBuilder` wywołać `IWebHostBuilder`metody rozszerzenia zakłada, że jest wystąpieniem , jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="658a6-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="658a6-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="658a6-247">CaptureStartupErrors</span></span>

<span data-ttu-id="658a6-248">Kiedy `false`, błędy podczas uruchamiania spowodować wyjście hosta.</span><span class="sxs-lookup"><span data-stu-id="658a6-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="658a6-249">Gdy `true`host przechwytuje wyjątki podczas uruchamiania i próbuje uruchomić serwer.</span><span class="sxs-lookup"><span data-stu-id="658a6-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="658a6-250">**Klucz:**`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="658a6-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="658a6-251">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="658a6-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="658a6-252">**Domyślnie:** `false` Domyślnie, chyba że aplikacja działa z Kestrel `true`za IIS, gdzie domyślnie jest .</span><span class="sxs-lookup"><span data-stu-id="658a6-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="658a6-253">**Zmienna środowiskowa:**`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="658a6-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="658a6-254">Aby ustawić tę wartość, `CaptureStartupErrors`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="658a6-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="658a6-255">Szczegółoweerrory</span><span class="sxs-lookup"><span data-stu-id="658a6-255">DetailedErrors</span></span>

<span data-ttu-id="658a6-256">Po włączeniu lub gdy `Development`środowisko jest , aplikacja przechwytuje szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="658a6-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="658a6-257">**Klucz:**`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="658a6-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="658a6-258">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="658a6-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="658a6-259">**Domyślnie**:`false`</span><span class="sxs-lookup"><span data-stu-id="658a6-259">**Default**: `false`</span></span>  
<span data-ttu-id="658a6-260">**Zmienna środowiskowa:**`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="658a6-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="658a6-261">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="658a6-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="658a6-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="658a6-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="658a6-263">Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do załadowania podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="658a6-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="658a6-264">Mimo że wartość konfiguracji domyślnie pusty ciąg, zestawy uruchamiania hostingu zawsze zawierają zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="658a6-265">Po udostępnieniu zestawów uruchamiania hostingu są one dodawane do zestawu aplikacji do ładowania, gdy aplikacja tworzy swoje typowe usługi podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="658a6-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="658a6-266">**Klucz:**`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="658a6-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="658a6-267">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-267">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-268">**Domyślnie**: Pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="658a6-268">**Default**: Empty string</span></span>  
<span data-ttu-id="658a6-269">**Zmienna środowiskowa:**`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="658a6-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="658a6-270">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="658a6-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="658a6-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="658a6-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="658a6-272">Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do wykluczenia podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="658a6-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="658a6-273">**Klucz:**`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="658a6-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="658a6-274">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-274">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-275">**Domyślnie**: Pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="658a6-275">**Default**: Empty string</span></span>  
<span data-ttu-id="658a6-276">**Zmienna środowiskowa:**`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="658a6-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="658a6-277">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="658a6-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="658a6-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="658a6-278">HTTPS_Port</span></span>

<span data-ttu-id="658a6-279">Port przekierowania HTTPS.</span><span class="sxs-lookup"><span data-stu-id="658a6-279">The HTTPS redirect port.</span></span> <span data-ttu-id="658a6-280">Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="658a6-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="658a6-281">**Klucz:**`https_port`</span><span class="sxs-lookup"><span data-stu-id="658a6-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="658a6-282">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-282">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-283">**Domyślnie:** Wartość domyślna nie jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="658a6-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="658a6-284">**Zmienna środowiskowa:**`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="658a6-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="658a6-285">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="658a6-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="658a6-286">PreferHostingUrls (PreferHostingUrls)</span><span class="sxs-lookup"><span data-stu-id="658a6-286">PreferHostingUrls</span></span>

<span data-ttu-id="658a6-287">Wskazuje, czy host powinien nasłuchiwania adresów URL skonfigurowanych przy 200 000 `IWebHostBuilder` adresów URL skonfigurowanych z `IServer` implementacją.</span><span class="sxs-lookup"><span data-stu-id="658a6-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="658a6-288">**Klucz:**`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="658a6-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="658a6-289">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="658a6-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="658a6-290">**Domyślnie**:`true`</span><span class="sxs-lookup"><span data-stu-id="658a6-290">**Default**: `true`</span></span>  
<span data-ttu-id="658a6-291">**Zmienna środowiskowa:**`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="658a6-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="658a6-292">Aby ustawić tę wartość, użyj `PreferHostingUrls`zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="658a6-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="658a6-293">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="658a6-293">PreventHostingStartup</span></span>

<span data-ttu-id="658a6-294">Zapobiega automatycznemu obciążaniu zestawów uruchamiania hostingu, w tym zestawów uruchamiania hostingu skonfigurowanych przez zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="658a6-295">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="658a6-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="658a6-296">**Klucz:**`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="658a6-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="658a6-297">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="658a6-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="658a6-298">**Domyślnie**:`false`</span><span class="sxs-lookup"><span data-stu-id="658a6-298">**Default**: `false`</span></span>  
<span data-ttu-id="658a6-299">**Zmienna środowiskowa:**`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="658a6-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="658a6-300">Aby ustawić tę wartość, użyj `UseSetting` zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="658a6-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="658a6-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="658a6-301">StartupAssembly</span></span>

<span data-ttu-id="658a6-302">Zestaw do wyszukiwania `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="658a6-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="658a6-303">**Klucz:**`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="658a6-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="658a6-304">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-304">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-305">**Domyślnie**: Zespół aplikacji</span><span class="sxs-lookup"><span data-stu-id="658a6-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="658a6-306">**Zmienna środowiskowa:**`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="658a6-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="658a6-307">Aby ustawić tę wartość, należy `UseStartup`użyć zmiennej środowiskowej lub wywołania .</span><span class="sxs-lookup"><span data-stu-id="658a6-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="658a6-308">`UseStartup`może przyjąć nazwę`string`zestawu ( )`TStartup`lub typ ( ).</span><span class="sxs-lookup"><span data-stu-id="658a6-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="658a6-309">Jeśli `UseStartup` wywoływanych jest wiele metod, pierwszeństwo ma ostatnia.</span><span class="sxs-lookup"><span data-stu-id="658a6-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="658a6-310">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="658a6-310">URLs</span></span>

<span data-ttu-id="658a6-311">Lista adresów IP lub adresów hostów rozdzielanych średnikami z portami i protokołami, na których serwer powinien nasłuchiwał żądań.</span><span class="sxs-lookup"><span data-stu-id="658a6-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="658a6-312">Na przykład `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="658a6-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="658a6-313">Użyj\*" ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwach hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="658a6-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="658a6-314">Protokół (`http://` `https://`lub ) musi być dołączony do każdego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="658a6-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="658a6-315">Obsługiwane formaty różnią się w zależności od serwera.</span><span class="sxs-lookup"><span data-stu-id="658a6-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="658a6-316">**Klucz:**`urls`</span><span class="sxs-lookup"><span data-stu-id="658a6-316">**Key**: `urls`</span></span>  
<span data-ttu-id="658a6-317">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-317">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-318">**Domyślnie**: `http://localhost:5000` i`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="658a6-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="658a6-319">**Zmienna środowiskowa:**`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="658a6-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="658a6-320">Aby ustawić tę wartość, użyj `UseUrls`zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="658a6-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="658a6-321">Pustułka ma własny interfejs API konfiguracji punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="658a6-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="658a6-322">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="658a6-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="658a6-323">Webroot</span><span class="sxs-lookup"><span data-stu-id="658a6-323">WebRoot</span></span>

<span data-ttu-id="658a6-324">[Właściwość IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) określa względną ścieżkę do statycznych zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="658a6-325">Jeśli ścieżka nie istnieje, używany jest dostawca plików bez operacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="658a6-326">**Klucz:**`webroot`</span><span class="sxs-lookup"><span data-stu-id="658a6-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="658a6-327">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-327">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-328">**Domyślnie**: `wwwroot`Wartość domyślna to .</span><span class="sxs-lookup"><span data-stu-id="658a6-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="658a6-329">Musi istnieć ścieżka do *{content root}/wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="658a6-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="658a6-330">**Zmienna środowiskowa:**`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="658a6-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="658a6-331">Aby ustawić tę wartość, należy `UseWebRoot` użyć `IWebHostBuilder`zmiennej środowiskowej lub wywołać na:</span><span class="sxs-lookup"><span data-stu-id="658a6-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="658a6-332">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="658a6-332">For more information, see:</span></span>

* [<span data-ttu-id="658a6-333">Podstawy: Korzeń sieci Web</span><span class="sxs-lookup"><span data-stu-id="658a6-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="658a6-334">ContentRoot (Typ treści)</span><span class="sxs-lookup"><span data-stu-id="658a6-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="658a6-335">Zarządzanie okresem istnienia hosta</span><span class="sxs-lookup"><span data-stu-id="658a6-335">Manage the host lifetime</span></span>

<span data-ttu-id="658a6-336">Wywołaj metody <xref:Microsoft.Extensions.Hosting.IHost> na wbudowanej implementacji, aby uruchomić i zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="658a6-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="658a6-337">Te metody <xref:Microsoft.Extensions.Hosting.IHostedService> mają wpływ na wszystkie implementacje, które są zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="658a6-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="658a6-338">Run</span><span class="sxs-lookup"><span data-stu-id="658a6-338">Run</span></span>

<span data-ttu-id="658a6-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący, dopóki host nie zostanie zamknięty.</span><span class="sxs-lookup"><span data-stu-id="658a6-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="658a6-340">RunAsync ( RunAsync )</span><span class="sxs-lookup"><span data-stu-id="658a6-340">RunAsync</span></span>

<span data-ttu-id="658a6-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia systemu.</span><span class="sxs-lookup"><span data-stu-id="658a6-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="658a6-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="658a6-342">RunConsoleAsync</span></span>

<span data-ttu-id="658a6-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>umożliwia obsługę konsoli, buduje i uruchamia hosta i czeka na <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM, aby zamknąć.</span><span class="sxs-lookup"><span data-stu-id="658a6-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="658a6-344">Uruchamianie</span><span class="sxs-lookup"><span data-stu-id="658a6-344">Start</span></span>

<span data-ttu-id="658a6-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="658a6-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="658a6-346">StartAsync (StartAsync)</span><span class="sxs-lookup"><span data-stu-id="658a6-346">StartAsync</span></span>

<span data-ttu-id="658a6-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia hosta i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="658a6-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="658a6-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na `StartAsync`początku , który czeka, aż zakończy się przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="658a6-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="658a6-349">Może to służyć do opóźnienia uruchamiania, dopóki nie zostanie zasygnaligozowana przez zdarzenie zewnętrzne.</span><span class="sxs-lookup"><span data-stu-id="658a6-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="658a6-350">StopAsync (StopAsync)</span><span class="sxs-lookup"><span data-stu-id="658a6-350">StopAsync</span></span>

<span data-ttu-id="658a6-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w podanym limit czasu.</span><span class="sxs-lookup"><span data-stu-id="658a6-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="658a6-352">WaitForShutdown (WaitForShutdown)</span><span class="sxs-lookup"><span data-stu-id="658a6-352">WaitForShutdown</span></span>

<span data-ttu-id="658a6-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje wątek wywołujący, dopóki zamknięcie zostanie wywołane przez IHostLifetime, na przykład przez <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="658a6-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="658a6-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="658a6-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="658a6-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task> zwraca, który kończy się po zamknięciu jest <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>wyzwalany za pośrednictwem danego tokenu i wywołania .</span><span class="sxs-lookup"><span data-stu-id="658a6-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="658a6-356">Sterowanie zewnętrzne</span><span class="sxs-lookup"><span data-stu-id="658a6-356">External control</span></span>

<span data-ttu-id="658a6-357">Bezpośrednią kontrolę okresu istnienia hosta można osiągnąć za pomocą metod, które można wywołać zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="658a6-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="658a6-358">ASP.NET aplikacje Core konfigurują i uruchamiają hosta.</span><span class="sxs-lookup"><span data-stu-id="658a6-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="658a6-359">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="658a6-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="658a6-360">W tym artykule omówiono ASP.NET<xref:Microsoft.Extensions.Hosting.HostBuilder>Core Generic Host ( ), który jest używany dla aplikacji, które nie przetwarzają żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="658a6-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="658a6-361">Celem hosta ogólnego jest oddzielenie potoku HTTP od interfejsu API hosta sieci Web, aby włączyć szerszą gamę scenariuszy hosta.</span><span class="sxs-lookup"><span data-stu-id="658a6-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="658a6-362">Wiadomości, zadania w tle i inne obciążenia inne niż HTTP oparte na generic hosta korzystają z możliwości przekrojowych, takich jak konfiguracja, iniekcja zależności (DI) i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="658a6-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="658a6-363">Generic Host jest nowy w ASP.NET Core 2.1 i nie nadaje się do scenariuszy hostingu.</span><span class="sxs-lookup"><span data-stu-id="658a6-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="658a6-364">W przypadku scenariuszy hostingu sieci Web należy użyć [hosta sieci Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="658a6-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="658a6-365">Generic Host zastąpi hosta sieci Web w przyszłej wersji i będzie działać jako podstawowy interfejs API hosta zarówno w scenariuszach HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="658a6-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="658a6-366">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="658a6-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="658a6-367">Podczas uruchamiania przykładowej aplikacji w [programie Visual Studio Code](https://code.visualstudio.com/)należy użyć zewnętrznego lub *zintegrowanego terminala*.</span><span class="sxs-lookup"><span data-stu-id="658a6-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="658a6-368">Nie uruchamiaj próbki w `internalConsole`pliku .</span><span class="sxs-lookup"><span data-stu-id="658a6-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="658a6-369">Aby ustawić konsolę w programie Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="658a6-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="658a6-370">Otwórz plik *vscode/launch.json.*</span><span class="sxs-lookup"><span data-stu-id="658a6-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="658a6-371">W konfiguracji **.NET Core Launch (konsola)** znajdź wpis **konsoli.**</span><span class="sxs-lookup"><span data-stu-id="658a6-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="658a6-372">Ustaw wartość na `externalTerminal` jedną `integratedTerminal`lub .</span><span class="sxs-lookup"><span data-stu-id="658a6-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="658a6-373">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="658a6-373">Introduction</span></span>

<span data-ttu-id="658a6-374">Biblioteka hosta ogólnego <xref:Microsoft.Extensions.Hosting> jest dostępna w obszarze nazw i jest dostarczana przez pakiet [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/)</span><span class="sxs-lookup"><span data-stu-id="658a6-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="658a6-375">Pakiet `Microsoft.Extensions.Hosting` jest zawarty w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 lub nowszym).</span><span class="sxs-lookup"><span data-stu-id="658a6-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="658a6-376"><xref:Microsoft.Extensions.Hosting.IHostedService>jest punktem wejścia do wykonania kodu.</span><span class="sxs-lookup"><span data-stu-id="658a6-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="658a6-377">Każda <xref:Microsoft.Extensions.Hosting.IHostedService> implementacja jest wykonywana w kolejności [rejestracji usługi w ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="658a6-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="658a6-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>jest wywoływana <xref:Microsoft.Extensions.Hosting.IHostedService> na każdym po <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> uruchomieniu hosta i jest wywoływana w odwrotnej kolejności rejestracji, gdy host zamyka się bezpiecznie.</span><span class="sxs-lookup"><span data-stu-id="658a6-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="658a6-379">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="658a6-379">Set up a host</span></span>

<span data-ttu-id="658a6-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder>jest głównym składnikiem używanym przez biblioteki i aplikacje do inicjowania, tworzenia i uruchamiania hosta:</span><span class="sxs-lookup"><span data-stu-id="658a6-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="658a6-381">Opcje</span><span class="sxs-lookup"><span data-stu-id="658a6-381">Options</span></span>

<span data-ttu-id="658a6-382"><xref:Microsoft.Extensions.Hosting.HostOptions>skonfigurować opcje <xref:Microsoft.Extensions.Hosting.IHost>dla pliku .</span><span class="sxs-lookup"><span data-stu-id="658a6-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="658a6-383">Limit czasu zamknięcia</span><span class="sxs-lookup"><span data-stu-id="658a6-383">Shutdown timeout</span></span>

<span data-ttu-id="658a6-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>ustawia limit czasu <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>dla .</span><span class="sxs-lookup"><span data-stu-id="658a6-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="658a6-385">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="658a6-385">The default value is five seconds.</span></span>

<span data-ttu-id="658a6-386">Następująca konfiguracja `Program.Main` opcji zwiększa domyślny pięciosekundowy limit czasu zamknięcia do 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="658a6-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="658a6-387">Usługi domyślne</span><span class="sxs-lookup"><span data-stu-id="658a6-387">Default services</span></span>

<span data-ttu-id="658a6-388">Podczas inicjowania hosta rejestrowane są następujące usługi:</span><span class="sxs-lookup"><span data-stu-id="658a6-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="658a6-389">[Środowisko](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>( )</span><span class="sxs-lookup"><span data-stu-id="658a6-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="658a6-390">[Konfiguracja](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="658a6-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="658a6-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="658a6-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="658a6-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="658a6-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="658a6-393">[Opcje](xref:fundamentals/configuration/options) <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>( )</span><span class="sxs-lookup"><span data-stu-id="658a6-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="658a6-394">[Rejestrowanie](xref:fundamentals/logging/index) <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>( )</span><span class="sxs-lookup"><span data-stu-id="658a6-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="658a6-395">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="658a6-395">Host configuration</span></span>

<span data-ttu-id="658a6-396">Konfiguracja hosta jest tworzona przez:</span><span class="sxs-lookup"><span data-stu-id="658a6-396">Host configuration is created by:</span></span>

* <span data-ttu-id="658a6-397">Wywoływanie metod <xref:Microsoft.Extensions.Hosting.IHostBuilder> rozszerzenia w celu ustawienia [katalogu głównego](#content-root) zawartości i [środowiska](#environment).</span><span class="sxs-lookup"><span data-stu-id="658a6-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="658a6-398">Odczytywanie konfiguracji <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>od dostawców konfiguracji w programie .</span><span class="sxs-lookup"><span data-stu-id="658a6-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="658a6-399">Metody rozszerzeń</span><span class="sxs-lookup"><span data-stu-id="658a6-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="658a6-400">Klucz aplikacji (nazwa)</span><span class="sxs-lookup"><span data-stu-id="658a6-400">Application key (name)</span></span>

<span data-ttu-id="658a6-401">[Właściwość IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) jest ustawiona z konfiguracji hosta podczas konstruowania hosta.</span><span class="sxs-lookup"><span data-stu-id="658a6-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="658a6-402">Aby jawnie ustawić wartość, użyj [hostDefaults.ApplicationKey:](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)</span><span class="sxs-lookup"><span data-stu-id="658a6-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="658a6-403">**Klucz:**`applicationName`</span><span class="sxs-lookup"><span data-stu-id="658a6-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="658a6-404">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-404">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-405">**Domyślnie**: Nazwa zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="658a6-406">**Zestaw za pomocą**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="658a6-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="658a6-407">**Zmienna środowiskowa**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` jest [opcjonalna i zdefiniowana przez użytkownika)](#configurehostconfiguration)</span><span class="sxs-lookup"><span data-stu-id="658a6-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="658a6-408">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="658a6-408">Content root</span></span>

<span data-ttu-id="658a6-409">To ustawienie określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="658a6-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="658a6-410">**Klucz:**`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="658a6-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="658a6-411">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-411">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-412">**Domyślnie**: Domyślnie folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="658a6-413">**Zestaw za pomocą**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="658a6-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="658a6-414">**Zmienna środowiskowa**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` jest [opcjonalna i zdefiniowana przez użytkownika)](#configurehostconfiguration)</span><span class="sxs-lookup"><span data-stu-id="658a6-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="658a6-415">Jeśli ścieżka nie istnieje, host nie uruchamia się.</span><span class="sxs-lookup"><span data-stu-id="658a6-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="658a6-416">Aby uzyskać więcej informacji, zobacz [Podstawy: Katalog główny zawartości](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="658a6-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="658a6-417">Środowisko</span><span class="sxs-lookup"><span data-stu-id="658a6-417">Environment</span></span>

<span data-ttu-id="658a6-418">Ustawia [środowisko](xref:fundamentals/environments)aplikacji .</span><span class="sxs-lookup"><span data-stu-id="658a6-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="658a6-419">**Klucz:**`environment`</span><span class="sxs-lookup"><span data-stu-id="658a6-419">**Key**: `environment`</span></span>  
<span data-ttu-id="658a6-420">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-420">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-421">**Domyślnie**:`Production`</span><span class="sxs-lookup"><span data-stu-id="658a6-421">**Default**: `Production`</span></span>  
<span data-ttu-id="658a6-422">**Zestaw za pomocą**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="658a6-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="658a6-423">**Zmienna środowiskowa**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` jest [opcjonalna i zdefiniowana przez użytkownika)](#configurehostconfiguration)</span><span class="sxs-lookup"><span data-stu-id="658a6-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="658a6-424">Środowisko można ustawić na dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="658a6-424">The environment can be set to any value.</span></span> <span data-ttu-id="658a6-425">Wartości zdefiniowane `Development`przez `Staging`ramy `Production`obejmują , i .</span><span class="sxs-lookup"><span data-stu-id="658a6-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="658a6-426">W wartościach nie rozróżnia się wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="658a6-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="658a6-427">Konfigurowanie konfiguracji hostowania</span><span class="sxs-lookup"><span data-stu-id="658a6-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="658a6-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>używa do <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> utworzenia <xref:Microsoft.Extensions.Configuration.IConfiguration> dla hosta.</span><span class="sxs-lookup"><span data-stu-id="658a6-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="658a6-429">Konfiguracja hosta służy do <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> inicjowania do użycia w procesie kompilacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="658a6-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="658a6-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="658a6-431">Host używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="658a6-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="658a6-432">Domyślnie nie są uwzględniane żadne dostawcy.</span><span class="sxs-lookup"><span data-stu-id="658a6-432">No providers are included by default.</span></span> <span data-ttu-id="658a6-433">Należy jawnie określić dostawców konfiguracji, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>których aplikacja wymaga w , w tym:</span><span class="sxs-lookup"><span data-stu-id="658a6-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="658a6-434">Konfiguracja pliku (na przykład z pliku *hostsettings.json).*</span><span class="sxs-lookup"><span data-stu-id="658a6-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="658a6-435">Konfiguracja zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="658a6-435">Environment variable configuration.</span></span>
* <span data-ttu-id="658a6-436">Konfiguracja argumentu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="658a6-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="658a6-437">Wszelkie inne wymagane dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="658a6-437">Any other required configuration providers.</span></span>

<span data-ttu-id="658a6-438">Konfiguracja pliku hosta jest włączona, określając ścieżkę `SetBasePath` podstawową aplikacji, po której następuje wywołanie jednego z [dostawców konfiguracji plików.](xref:fundamentals/configuration/index#file-configuration-provider)</span><span class="sxs-lookup"><span data-stu-id="658a6-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="658a6-439">Przykładowa aplikacja używa pliku JSON, *hostsettings.json*i wywołania, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> aby korzystać z ustawień konfiguracji hosta pliku.</span><span class="sxs-lookup"><span data-stu-id="658a6-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="658a6-440">Aby dodać [konfigurację zmiennej środowiskowej](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hosta, należy wywołać <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> konstruktora hosta.</span><span class="sxs-lookup"><span data-stu-id="658a6-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="658a6-441">`AddEnvironmentVariables`akceptuje opcjonalny prefiks zdefiniowany przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="658a6-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="658a6-442">Przykładowa aplikacja używa prefiksu `PREFIX_`programu .</span><span class="sxs-lookup"><span data-stu-id="658a6-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="658a6-443">Prefiks jest usuwany podczas odczytu zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="658a6-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="658a6-444">Po skonfigurowaniu hosta przykładowej aplikacji wartość zmiennej środowiskowej `PREFIX_ENVIRONMENT` staje `environment` się wartością konfiguracji hosta dla klucza.</span><span class="sxs-lookup"><span data-stu-id="658a6-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="658a6-445">Podczas tworzenia podczas korzystania z programu `dotnet run`Visual [Studio](https://visualstudio.microsoft.com) lub uruchamiania aplikacji z , zmienne środowiskowe mogą być ustawione w pliku *Properties/launchSettings.json.*</span><span class="sxs-lookup"><span data-stu-id="658a6-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="658a6-446">W [programie Visual Studio Code](https://code.visualstudio.com/)zmienne środowiskowe mogą być ustawiane w pliku *vscode/launch.json* podczas tworzenia.</span><span class="sxs-lookup"><span data-stu-id="658a6-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="658a6-447">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="658a6-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="658a6-448">[Konfiguracja wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>jest dodawana przez wywołanie .</span><span class="sxs-lookup"><span data-stu-id="658a6-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="658a6-449">Konfiguracja wiersza polecenia jest dodawana jako ostatnia, aby umożliwić argumentom wiersza polecenia zastąpienie konfiguracji dostarczonej przez wcześniejszych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="658a6-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="658a6-450">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="658a6-450">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="658a6-451">Dodatkowa konfiguracja może być dostarczona z [kluczami applicationName](#application-key-name) i [contentRoot.](#content-root)</span><span class="sxs-lookup"><span data-stu-id="658a6-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="658a6-452">Przykładowa `HostBuilder` <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>konfiguracja przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="658a6-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="658a6-453">Konfigurowanie konfiguracjiappconfiguration</span><span class="sxs-lookup"><span data-stu-id="658a6-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="658a6-454">Konfiguracja aplikacji jest <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> tworzona przez wywołanie <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="658a6-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>używa do <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> utworzenia <xref:Microsoft.Extensions.Configuration.IConfiguration> aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="658a6-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="658a6-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="658a6-457">Aplikacja używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="658a6-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="658a6-458">Konfiguracja utworzona <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> przez jest dostępna w [witrynie HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) dla kolejnych operacji i w <xref:Microsoft.Extensions.Hosting.IHost.Services*>programie .</span><span class="sxs-lookup"><span data-stu-id="658a6-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="658a6-459">Konfiguracja aplikacji automatycznie odbiera konfigurację hosta udostępnianą przez [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="658a6-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="658a6-460">Przykładowa konfiguracja aplikacji przy użyciu: <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*></span><span class="sxs-lookup"><span data-stu-id="658a6-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="658a6-461">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="658a6-461">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="658a6-462">*appsettings. Development.json*:</span><span class="sxs-lookup"><span data-stu-id="658a6-462">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="658a6-463">*appsettings. Produkcja.json*:</span><span class="sxs-lookup"><span data-stu-id="658a6-463">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="658a6-464">Aby przenieść pliki ustawień do katalogu wyjściowego, określ pliki ustawień jako [elementy projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="658a6-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="658a6-465">Przykładowa aplikacja przenosi pliki ustawień aplikacji JSON i *hostsettings.json* z następującym `<Content>` elementem:</span><span class="sxs-lookup"><span data-stu-id="658a6-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="658a6-466">Metody rozszerzenia konfiguracji, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> takie jak i wymagają dodatkowych pakietów NuGet, takich jak [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) i [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="658a6-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="658a6-467">Chyba że aplikacja używa [microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), pakiety te muszą zostać dodane do projektu oprócz podstawowego pakietu [Microsoft.Extensions.Configuration.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration)</span><span class="sxs-lookup"><span data-stu-id="658a6-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="658a6-468">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="658a6-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="658a6-469">Konfigurowanie usług</span><span class="sxs-lookup"><span data-stu-id="658a6-469">ConfigureServices</span></span>

<span data-ttu-id="658a6-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>dodaje usługi do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="658a6-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="658a6-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="658a6-472">Usługa hostowana jest klasą z logiką <xref:Microsoft.Extensions.Hosting.IHostedService> zadań w tle, która implementuje interfejs.</span><span class="sxs-lookup"><span data-stu-id="658a6-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="658a6-473">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="658a6-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="658a6-474">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) używa `AddHostedService` metody rozszerzenia, aby dodać `LifetimeEventsHostedService`usługę dla zdarzeń okresu `TimedHostedService`istnienia i zadania w tle z odpowiednim czasem, do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="658a6-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="658a6-475">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="658a6-475">ConfigureLogging</span></span>

<span data-ttu-id="658a6-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>dodaje pełnomocnika do skonfigurowania dostarczonego <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span><span class="sxs-lookup"><span data-stu-id="658a6-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="658a6-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="658a6-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="658a6-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="658a6-478">UseConsoleLifetime</span></span>

<span data-ttu-id="658a6-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>nasłuchuje <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM i wywołuje, <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="658a6-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="658a6-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="658a6-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="658a6-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`jest wstępnie zarejestrowany jako domyślna implementacja okresu istnienia.</span><span class="sxs-lookup"><span data-stu-id="658a6-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="658a6-482">Używany jest ostatni zarejestrowany okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="658a6-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="658a6-483">Konfiguracja kontenera</span><span class="sxs-lookup"><span data-stu-id="658a6-483">Container configuration</span></span>

<span data-ttu-id="658a6-484">Aby obsługiwać podłączanie do innych kontenerów, host może zaakceptować plik <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="658a6-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="658a6-485">Zapewnienie fabryki nie jest częścią rejestracji kontenera DI, ale zamiast tego jest nieodłącznym elementem hosta używanym do tworzenia betonowego kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="658a6-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="658a6-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder)&gt;](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) zastępuje domyślną fabrykę używaną do utworzenia dostawcy usług aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="658a6-487">Niestandardowa konfiguracja kontenera jest zarządzana <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> przez metodę.</span><span class="sxs-lookup"><span data-stu-id="658a6-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="658a6-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>zapewnia silnie typizowane środowisko konfigurowania kontenera na podstawie podstawowego interfejsu API hosta.</span><span class="sxs-lookup"><span data-stu-id="658a6-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="658a6-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="658a6-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="658a6-490">Tworzenie kontenera usługi dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="658a6-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="658a6-491">Zapewnienie fabryki kontenerów serwisowych:</span><span class="sxs-lookup"><span data-stu-id="658a6-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="658a6-492">Użyj ustawień fabrycznych i skonfiguruj niestandardowy kontener usługi dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="658a6-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="658a6-493">Rozszerzalność</span><span class="sxs-lookup"><span data-stu-id="658a6-493">Extensibility</span></span>

<span data-ttu-id="658a6-494">Rozszerzalność hosta jest wykonywana <xref:Microsoft.Extensions.Hosting.IHostBuilder>przy metodach rozszerzeń na .</span><span class="sxs-lookup"><span data-stu-id="658a6-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="658a6-495">W poniższym przykładzie pokazano, <xref:Microsoft.Extensions.Hosting.IHostBuilder> jak metoda rozszerzenia rozszerza implementację za <xref:fundamentals/host/hosted-services>pomocą przykładu [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) przedstawionego w .</span><span class="sxs-lookup"><span data-stu-id="658a6-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="658a6-496">Aplikacja ustanawia metodę `UseHostedService` rozszerzenia, aby zarejestrować usługę `T`hosta przekazywana w:</span><span class="sxs-lookup"><span data-stu-id="658a6-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="658a6-497">Zarządzanie hostem</span><span class="sxs-lookup"><span data-stu-id="658a6-497">Manage the host</span></span>

<span data-ttu-id="658a6-498">Implementacja <xref:Microsoft.Extensions.Hosting.IHost> jest odpowiedzialny za <xref:Microsoft.Extensions.Hosting.IHostedService> uruchamianie i zatrzymywanie implementacji, które są zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="658a6-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="658a6-499">Run</span><span class="sxs-lookup"><span data-stu-id="658a6-499">Run</span></span>

<span data-ttu-id="658a6-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący, dopóki host nie zostanie zamknięty:</span><span class="sxs-lookup"><span data-stu-id="658a6-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="658a6-501">RunAsync ( RunAsync )</span><span class="sxs-lookup"><span data-stu-id="658a6-501">RunAsync</span></span>

<span data-ttu-id="658a6-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia systemu:</span><span class="sxs-lookup"><span data-stu-id="658a6-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="658a6-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="658a6-503">RunConsoleAsync</span></span>

<span data-ttu-id="658a6-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>umożliwia obsługę konsoli, buduje i uruchamia hosta i czeka na <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM, aby zamknąć.</span><span class="sxs-lookup"><span data-stu-id="658a6-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="658a6-505">Start i StopAsync</span><span class="sxs-lookup"><span data-stu-id="658a6-505">Start and StopAsync</span></span>

<span data-ttu-id="658a6-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="658a6-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="658a6-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w podanym limit czasu.</span><span class="sxs-lookup"><span data-stu-id="658a6-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="658a6-508">StartAsync i StopAsync</span><span class="sxs-lookup"><span data-stu-id="658a6-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="658a6-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="658a6-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="658a6-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zatrzymuje aplikację.</span><span class="sxs-lookup"><span data-stu-id="658a6-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="658a6-511">WaitForShutdown (WaitForShutdown)</span><span class="sxs-lookup"><span data-stu-id="658a6-511">WaitForShutdown</span></span>

<span data-ttu-id="658a6-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>jest wyzwalany <xref:Microsoft.Extensions.Hosting.IHostLifetime>przez , `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` takich jak (nasłuchuje <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="658a6-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="658a6-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>połączeń <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>telefonicznych .</span><span class="sxs-lookup"><span data-stu-id="658a6-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="658a6-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="658a6-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="658a6-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task> zwraca, który kończy się po zamknięciu jest <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>wyzwalany za pośrednictwem danego tokenu i wywołania .</span><span class="sxs-lookup"><span data-stu-id="658a6-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="658a6-516">Sterowanie zewnętrzne</span><span class="sxs-lookup"><span data-stu-id="658a6-516">External control</span></span>

<span data-ttu-id="658a6-517">Zewnętrzna kontrola hosta można osiągnąć za pomocą metod, które można wywołać zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="658a6-517">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="658a6-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>początku , który czeka, aż zakończy się przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="658a6-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="658a6-519">Może to służyć do opóźnienia uruchamiania, dopóki nie zostanie zasygnaligozowana przez zdarzenie zewnętrzne.</span><span class="sxs-lookup"><span data-stu-id="658a6-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="658a6-520">Interfejs IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="658a6-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="658a6-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>zawiera informacje o środowisku hostingowym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="658a6-522">Użyj [iniekcji konstruktora,](xref:fundamentals/dependency-injection) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> aby uzyskać w celu wykorzystania jego właściwości i metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="658a6-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="658a6-523">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="658a6-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="658a6-524">Interfejs IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="658a6-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="658a6-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>umożliwia działania po starcie i zamknięciu, w tym wdzięczne żądania zamknięcia systemu.</span><span class="sxs-lookup"><span data-stu-id="658a6-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="658a6-526">Trzy właściwości interfejsu są tokeny anulowania <xref:System.Action> używane do rejestrowania metod, które definiują zdarzenia uruchamiania i zamykania.</span><span class="sxs-lookup"><span data-stu-id="658a6-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="658a6-527">Token anulowania</span><span class="sxs-lookup"><span data-stu-id="658a6-527">Cancellation Token</span></span> | <span data-ttu-id="658a6-528">Wyzwalane podczas&#8230;</span><span class="sxs-lookup"><span data-stu-id="658a6-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="658a6-529">Gospodarz w pełni się rozpoczął.</span><span class="sxs-lookup"><span data-stu-id="658a6-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="658a6-530">Host kończy wdzięczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="658a6-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="658a6-531">Wszystkie wnioski powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="658a6-531">All requests should be processed.</span></span> <span data-ttu-id="658a6-532">Zamykanie bloków, dopóki to zdarzenie nie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="658a6-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="658a6-533">Host wykonuje wdzięku zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="658a6-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="658a6-534">Żądania mogą nadal być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="658a6-534">Requests may still be processing.</span></span> <span data-ttu-id="658a6-535">Zamykanie bloków, dopóki to zdarzenie nie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="658a6-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="658a6-536">Konstruktor-wstrzyknąć <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> usługę do dowolnej klasy.</span><span class="sxs-lookup"><span data-stu-id="658a6-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="658a6-537">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) używa iniekcji konstruktora `LifetimeEventsHostedService` do klasy <xref:Microsoft.Extensions.Hosting.IHostedService> (implementacji), aby zarejestrować zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="658a6-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="658a6-538">*LifetimeEventsHostedService.cs:*</span><span class="sxs-lookup"><span data-stu-id="658a6-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="658a6-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>żąda rozwiązania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="658a6-540">Następująca klasa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> używa do bezpiecznie zamknąć aplikację, `Shutdown` gdy wywoływana jest metoda klasy:</span><span class="sxs-lookup"><span data-stu-id="658a6-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

<span data-ttu-id="658a6-541">Szablony ASP.NET Core tworzą hosta ogólnego .NET<xref:Microsoft.Extensions.Hosting.HostBuilder>Core ( ).</span><span class="sxs-lookup"><span data-stu-id="658a6-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="658a6-542">Definicja hosta</span><span class="sxs-lookup"><span data-stu-id="658a6-542">Host definition</span></span>

<span data-ttu-id="658a6-543">*Host* jest obiektem, który hermetyzuje zasoby aplikacji, takie jak:</span><span class="sxs-lookup"><span data-stu-id="658a6-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="658a6-544">Wstrzyknięcie zależności (DI)</span><span class="sxs-lookup"><span data-stu-id="658a6-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="658a6-545">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="658a6-545">Logging</span></span>
* <span data-ttu-id="658a6-546">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="658a6-546">Configuration</span></span>
* <span data-ttu-id="658a6-547">`IHostedService`Implementacje</span><span class="sxs-lookup"><span data-stu-id="658a6-547">`IHostedService` implementations</span></span>

<span data-ttu-id="658a6-548">Po uruchomieniu hosta wywołuje `IHostedService.StartAsync` każdą <xref:Microsoft.Extensions.Hosting.IHostedService> implementację, która znajduje się w kontenerze DI.</span><span class="sxs-lookup"><span data-stu-id="658a6-548">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="658a6-549">W aplikacji sieci web `IHostedService` jedną z implementacji jest usługa sieci web, która uruchamia [implementację serwera HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="658a6-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="658a6-550">Głównym powodem włączenia wszystkich współzależnych zasobów aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: kontrola nad uruchamianiem aplikacji i wdzięczne zamykanie.</span><span class="sxs-lookup"><span data-stu-id="658a6-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="658a6-551">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="658a6-551">Set up a host</span></span>

<span data-ttu-id="658a6-552">Host jest zazwyczaj skonfigurowany, zbudowany i uruchamiany `Program` przez kod w klasie.</span><span class="sxs-lookup"><span data-stu-id="658a6-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="658a6-553">Metoda: `Main`</span><span class="sxs-lookup"><span data-stu-id="658a6-553">The `Main` method:</span></span>

* <span data-ttu-id="658a6-554">Wywołuje `CreateHostBuilder` metodę tworzenia i konfigurowania obiektu konstruktora.</span><span class="sxs-lookup"><span data-stu-id="658a6-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="658a6-555">`Build` Wywołania `Run` i metody na obiekcie konstruktora.</span><span class="sxs-lookup"><span data-stu-id="658a6-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="658a6-556">Szablony sieci Web ASP.NET Core generują następujący kod w celu utworzenia hosta:</span><span class="sxs-lookup"><span data-stu-id="658a6-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="658a6-557">Poniższy kod tworzy obciążenie inne `IHostedService` niż HTTP z implementacją dodaną do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="658a6-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="658a6-558">W przypadku obciążenia `Main` HTTP metoda jest `CreateHostBuilder` `ConfigureWebHostDefaults`taka sama, ale wywołuje:</span><span class="sxs-lookup"><span data-stu-id="658a6-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="658a6-559">Jeśli aplikacja używa entity framework core, nie należy zmieniać `CreateHostBuilder` nazwę lub podpis metody.</span><span class="sxs-lookup"><span data-stu-id="658a6-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="658a6-560">[Narzędzia Entity Framework Core](/ef/core/miscellaneous/cli/) oczekują, aby znaleźć `CreateHostBuilder` metodę, która konfiguruje hosta bez uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="658a6-561">Aby uzyskać więcej informacji, zobacz [Tworzenie dbcontext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="658a6-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="658a6-562">Domyślne ustawienia konstruktora</span><span class="sxs-lookup"><span data-stu-id="658a6-562">Default builder settings</span></span>

<span data-ttu-id="658a6-563">Metoda: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="658a6-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="658a6-564">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na <xref:System.IO.Directory.GetCurrentDirectory*>ścieżkę zwróconą przez program .</span><span class="sxs-lookup"><span data-stu-id="658a6-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="658a6-565">Ładuje konfigurację hosta z:</span><span class="sxs-lookup"><span data-stu-id="658a6-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="658a6-566">Zmienne środowiskowe poprzedzone . `DOTNET_`</span><span class="sxs-lookup"><span data-stu-id="658a6-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="658a6-567">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="658a6-567">Command-line arguments.</span></span>
* <span data-ttu-id="658a6-568">Ładuje konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="658a6-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="658a6-569">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="658a6-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="658a6-570">*appsettings. {Środowisko}.json*.</span><span class="sxs-lookup"><span data-stu-id="658a6-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="658a6-571">[Tajny menedżer,](xref:security/app-secrets) gdy aplikacja `Development` działa w środowisku.</span><span class="sxs-lookup"><span data-stu-id="658a6-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="658a6-572">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="658a6-572">Environment variables.</span></span>
  * <span data-ttu-id="658a6-573">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="658a6-573">Command-line arguments.</span></span>
* <span data-ttu-id="658a6-574">Dodaje następujących dostawców [rejestrowania:](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="658a6-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="658a6-575">Konsola</span><span class="sxs-lookup"><span data-stu-id="658a6-575">Console</span></span>
  * <span data-ttu-id="658a6-576">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="658a6-576">Debug</span></span>
  * <span data-ttu-id="658a6-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="658a6-577">EventSource</span></span>
  * <span data-ttu-id="658a6-578">EventLog (tylko w przypadku pracy w systemie Windows)</span><span class="sxs-lookup"><span data-stu-id="658a6-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="658a6-579">Włącza [sprawdzanie poprawności zakresu](xref:fundamentals/dependency-injection#scope-validation) i sprawdzania poprawności [zależności,](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) gdy środowisko jest rozwój.</span><span class="sxs-lookup"><span data-stu-id="658a6-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="658a6-580">Metoda: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="658a6-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="658a6-581">Ładuje konfigurację hosta ze zmiennych środowiskowych poprzedzonych programem `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="658a6-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="658a6-582">Ustawia serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci web i konfiguruje go przy użyciu dostawców konfiguracji hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="658a6-583">Aby zapoznać się z domyślnymi <xref:fundamentals/servers/kestrel#kestrel-options>opcjami serwera Kestrel, zobacz .</span><span class="sxs-lookup"><span data-stu-id="658a6-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="658a6-584">Dodaje [oprogramowanie pośredniczące filtrowania hosta](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="658a6-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="658a6-585">Dodaje [oprogramowanie pośredniczące nagłówków przesyłanych dalej,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest `true`równe .</span><span class="sxs-lookup"><span data-stu-id="658a6-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="658a6-586">Umożliwia integrację z usługiami IIS.</span><span class="sxs-lookup"><span data-stu-id="658a6-586">Enables IIS integration.</span></span> <span data-ttu-id="658a6-587">Aby zapoznać się z <xref:host-and-deploy/iis/index#iis-options>domyślnymi opcjami iis, zobacz .</span><span class="sxs-lookup"><span data-stu-id="658a6-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="658a6-588">W sekcji [Ustawienia dla wszystkich typów aplikacji](#settings-for-all-app-types) i Ustawienia aplikacji sieci [Web](#settings-for-web-apps) w dalszej części tego artykułu pokazano, jak zastąpić domyślne ustawienia konstruktora.</span><span class="sxs-lookup"><span data-stu-id="658a6-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="658a6-589">Usługi świadczone w ramach ram</span><span class="sxs-lookup"><span data-stu-id="658a6-589">Framework-provided services</span></span>

<span data-ttu-id="658a6-590">Następujące usługi są rejestrowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="658a6-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="658a6-591">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="658a6-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="658a6-592">IHostLifetime (czas pracy)</span><span class="sxs-lookup"><span data-stu-id="658a6-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="658a6-593">IHostEnvironment / IWebHostŚrodowicielenie</span><span class="sxs-lookup"><span data-stu-id="658a6-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="658a6-594">Aby uzyskać więcej informacji na <xref:fundamentals/dependency-injection#framework-provided-services>temat usług świadczonych w ramach ram, zobacz .</span><span class="sxs-lookup"><span data-stu-id="658a6-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="658a6-595">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="658a6-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="658a6-596"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Wstrzyknąć `IApplicationLifetime`(dawniej) usługi do dowolnej klasy do obsługi zadań po starcie i wdzięku zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="658a6-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="658a6-597">Trzy właściwości w interfejsie są tokeny anulowania używane do rejestrowania uruchamiania aplikacji i metody obsługi zdarzeń zatrzymania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="658a6-598">Interfejs zawiera również `StopApplication` metodę.</span><span class="sxs-lookup"><span data-stu-id="658a6-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="658a6-599">Poniższy przykład `IHostedService` jest implementacją, która rejestruje `IHostApplicationLifetime` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="658a6-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="658a6-600">IHostLifetime (czas pracy)</span><span class="sxs-lookup"><span data-stu-id="658a6-600">IHostLifetime</span></span>

<span data-ttu-id="658a6-601">Implementacja <xref:Microsoft.Extensions.Hosting.IHostLifetime> kontroluje, kiedy host uruchamia się i kiedy zatrzymuje.</span><span class="sxs-lookup"><span data-stu-id="658a6-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="658a6-602">Używana jest ostatnia zarejestrowana implementacja.</span><span class="sxs-lookup"><span data-stu-id="658a6-602">The last implementation registered is used.</span></span>

<span data-ttu-id="658a6-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`jest domyślną `IHostLifetime` implementacją.</span><span class="sxs-lookup"><span data-stu-id="658a6-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="658a6-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="658a6-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="658a6-605">Nasłuchuje <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM i wywołuje, <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="658a6-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="658a6-606">Odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="658a6-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="658a6-607">IHostŚrodowienie</span><span class="sxs-lookup"><span data-stu-id="658a6-607">IHostEnvironment</span></span>

<span data-ttu-id="658a6-608">Wstrzyknąć <xref:Microsoft.Extensions.Hosting.IHostEnvironment> usługę do klasy, aby uzyskać informacje o następujących ustawieniach:</span><span class="sxs-lookup"><span data-stu-id="658a6-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="658a6-609">Applicationname</span><span class="sxs-lookup"><span data-stu-id="658a6-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="658a6-610">Nazwa środowiska</span><span class="sxs-lookup"><span data-stu-id="658a6-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="658a6-611">Ścieżka contentrootpath</span><span class="sxs-lookup"><span data-stu-id="658a6-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="658a6-612">Aplikacje sieci `IWebHostEnvironment` Web implementują `IHostEnvironment` interfejs, który dziedziczy i dodaje [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="658a6-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="658a6-613">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="658a6-613">Host configuration</span></span>

<span data-ttu-id="658a6-614">Konfiguracja hosta jest używana <xref:Microsoft.Extensions.Hosting.IHostEnvironment> dla właściwości implementacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="658a6-615">Konfiguracja hosta jest dostępna w <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>pliku [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) wewnątrz .</span><span class="sxs-lookup"><span data-stu-id="658a6-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="658a6-616">Po `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , jest zastępowany config aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="658a6-617">Aby dodać konfigurację <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`hosta, zadzwoń .</span><span class="sxs-lookup"><span data-stu-id="658a6-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="658a6-618">`ConfigureHostConfiguration`można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="658a6-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="658a6-619">Host używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="658a6-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="658a6-620">Dostawca zmiennych środowiskowych `DOTNET_` z prefiksem i `CreateDefaultBuilder`argumentami wiersza polecenia są uwzględniane przez program .</span><span class="sxs-lookup"><span data-stu-id="658a6-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="658a6-621">W przypadku aplikacji sieci web dodawany jest dostawca zmiennych środowiskowych z prefiksem. `ASPNETCORE_`</span><span class="sxs-lookup"><span data-stu-id="658a6-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="658a6-622">Prefiks jest usuwany podczas odczytu zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="658a6-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="658a6-623">Na przykład wartość zmiennej `ASPNETCORE_ENVIRONMENT` środowiskowej dla staje `environment` się wartością konfiguracji hosta dla klucza.</span><span class="sxs-lookup"><span data-stu-id="658a6-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="658a6-624">W poniższym przykładzie tworzy konfigurację hosta:</span><span class="sxs-lookup"><span data-stu-id="658a6-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="658a6-625">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="658a6-625">App configuration</span></span>

<span data-ttu-id="658a6-626">Konfiguracja aplikacji jest <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder`tworzona przez wywołanie .</span><span class="sxs-lookup"><span data-stu-id="658a6-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="658a6-627">`ConfigureAppConfiguration`można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="658a6-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="658a6-628">Aplikacja używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="658a6-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="658a6-629">Konfiguracja utworzona `ConfigureAppConfiguration` przez jest dostępna w [witrynie HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) dla kolejnych operacji i jako usługa z DI.</span><span class="sxs-lookup"><span data-stu-id="658a6-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="658a6-630">Konfiguracja hosta jest również dodawana do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="658a6-631">Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="658a6-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="658a6-632">Ustawienia dla wszystkich typów aplikacji</span><span class="sxs-lookup"><span data-stu-id="658a6-632">Settings for all app types</span></span>

<span data-ttu-id="658a6-633">W tej sekcji wymieniono ustawienia hosta, które dotyczą zarówno obciążeń HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="658a6-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="658a6-634">Domyślnie zmienne środowiskowe używane do konfigurowania `DOTNET_` `ASPNETCORE_` tych ustawień mogą mieć lub prefiks.</span><span class="sxs-lookup"><span data-stu-id="658a6-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="658a6-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="658a6-635">ApplicationName</span></span>

<span data-ttu-id="658a6-636">[Właściwość IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) jest ustawiana z konfiguracji hosta podczas konstruowaniu hosta.</span><span class="sxs-lookup"><span data-stu-id="658a6-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="658a6-637">**Klucz:**`applicationName`</span><span class="sxs-lookup"><span data-stu-id="658a6-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="658a6-638">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-638">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-639">**Domyślnie**: Nazwa zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="658a6-640">**Zmienna środowiskowa:**`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="658a6-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="658a6-641">Aby ustawić tę wartość, należy użyć zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="658a6-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="658a6-642">ContentRoot (Typ treści)</span><span class="sxs-lookup"><span data-stu-id="658a6-642">ContentRoot</span></span>

<span data-ttu-id="658a6-643">[Właściwość IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="658a6-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="658a6-644">Jeśli ścieżka nie istnieje, host nie uruchamia się.</span><span class="sxs-lookup"><span data-stu-id="658a6-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="658a6-645">**Klucz:**`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="658a6-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="658a6-646">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-646">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-647">**Domyślnie**: Folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="658a6-648">**Zmienna środowiskowa:**`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="658a6-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="658a6-649">Aby ustawić tę wartość, należy `UseContentRoot` użyć `IHostBuilder`zmiennej środowiskowej lub wywołać na:</span><span class="sxs-lookup"><span data-stu-id="658a6-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="658a6-650">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="658a6-650">For more information, see:</span></span>

* [<span data-ttu-id="658a6-651">Podstawy: Katalog główny treści</span><span class="sxs-lookup"><span data-stu-id="658a6-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="658a6-652">Webroot</span><span class="sxs-lookup"><span data-stu-id="658a6-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="658a6-653">Nazwa środowiska</span><span class="sxs-lookup"><span data-stu-id="658a6-653">EnvironmentName</span></span>

<span data-ttu-id="658a6-654">[Właściwość IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) można ustawić na dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="658a6-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="658a6-655">Wartości zdefiniowane `Development`przez `Staging`ramy `Production`obejmują , i .</span><span class="sxs-lookup"><span data-stu-id="658a6-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="658a6-656">W wartościach nie rozróżnia się wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="658a6-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="658a6-657">**Klucz:**`environment`</span><span class="sxs-lookup"><span data-stu-id="658a6-657">**Key**: `environment`</span></span>  
<span data-ttu-id="658a6-658">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-658">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-659">**Domyślnie**:`Production`</span><span class="sxs-lookup"><span data-stu-id="658a6-659">**Default**: `Production`</span></span>  
<span data-ttu-id="658a6-660">**Zmienna środowiskowa:**`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="658a6-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="658a6-661">Aby ustawić tę wartość, należy `UseEnvironment` użyć `IHostBuilder`zmiennej środowiskowej lub wywołać na:</span><span class="sxs-lookup"><span data-stu-id="658a6-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="658a6-662">Shutdowntimeout</span><span class="sxs-lookup"><span data-stu-id="658a6-662">ShutdownTimeout</span></span>

<span data-ttu-id="658a6-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) ustawia limit <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>czasu dla .</span><span class="sxs-lookup"><span data-stu-id="658a6-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="658a6-664">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="658a6-664">The default value is five seconds.</span></span>  <span data-ttu-id="658a6-665">W okresie limitu czasu host:</span><span class="sxs-lookup"><span data-stu-id="658a6-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="658a6-666">Wyzwala [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="658a6-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="658a6-667">Próbuje zatrzymać hostowane usługi, rejestrowanie błędów dla usług, które nie mogą się zatrzymać.</span><span class="sxs-lookup"><span data-stu-id="658a6-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="658a6-668">Jeśli limit czasu wygaśnie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="658a6-669">Usługi zatrzymują się nawet wtedy, gdy nie zakończyły przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="658a6-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="658a6-670">Jeśli usługi wymagają dodatkowego czasu, aby zatrzymać, zwiększyć limit czasu.</span><span class="sxs-lookup"><span data-stu-id="658a6-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="658a6-671">**Klucz:**`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="658a6-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="658a6-672">**Typ**:`int`</span><span class="sxs-lookup"><span data-stu-id="658a6-672">**Type**: `int`</span></span>  
<span data-ttu-id="658a6-673">**Domyślnie**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="658a6-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="658a6-674">**Zmienna środowiskowa:**`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="658a6-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="658a6-675">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub skonfiguruj `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="658a6-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="658a6-676">W poniższym przykładzie ustawia limit czasu na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="658a6-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="658a6-677">Wyłącz ponowne ładowanie konfiguracji aplikacji przy zmianie</span><span class="sxs-lookup"><span data-stu-id="658a6-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="658a6-678">[Domyślnie](xref:fundamentals/configuration/index#default), *appsettings.json* i *appsettings.{ Środowisko}.json* są ponownie ładowane po zmianie pliku.</span><span class="sxs-lookup"><span data-stu-id="658a6-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="658a6-679">Aby wyłączyć to zachowanie przeładowania w ASP.NET Core 5.0 Preview `hostBuilder:reloadConfigOnChange` 3 lub nowszym, ustaw klawisz na `false`.</span><span class="sxs-lookup"><span data-stu-id="658a6-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="658a6-680">**Klucz:**`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="658a6-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="658a6-681">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="658a6-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="658a6-682">**Domyślnie**:`true`</span><span class="sxs-lookup"><span data-stu-id="658a6-682">**Default**: `true`</span></span>  
<span data-ttu-id="658a6-683">**Argument wiersza polecenia:**`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="658a6-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="658a6-684">**Zmienna środowiskowa:**`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="658a6-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="658a6-685">Separator`:`dwukropka ( ) nie działa z kluczami hierarchicznymi zmiennych środowiskowych na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="658a6-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="658a6-686">Aby uzyskać więcej informacji, zobacz [Zmienne środowiskowe](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="658a6-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="658a6-687">Ustawienia aplikacji internetowych</span><span class="sxs-lookup"><span data-stu-id="658a6-687">Settings for web apps</span></span>

<span data-ttu-id="658a6-688">Niektóre ustawienia hosta dotyczą tylko obciążeń HTTP.</span><span class="sxs-lookup"><span data-stu-id="658a6-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="658a6-689">Domyślnie zmienne środowiskowe używane do konfigurowania `DOTNET_` `ASPNETCORE_` tych ustawień mogą mieć lub prefiks.</span><span class="sxs-lookup"><span data-stu-id="658a6-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="658a6-690">Dostępne `IWebHostBuilder` są metody rozszerzenia dla tych ustawień.</span><span class="sxs-lookup"><span data-stu-id="658a6-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="658a6-691">Przykłady kodu, które pokazują, jak `webBuilder` wywołać `IWebHostBuilder`metody rozszerzenia zakłada, że jest wystąpieniem , jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="658a6-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="658a6-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="658a6-692">CaptureStartupErrors</span></span>

<span data-ttu-id="658a6-693">Kiedy `false`, błędy podczas uruchamiania spowodować wyjście hosta.</span><span class="sxs-lookup"><span data-stu-id="658a6-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="658a6-694">Gdy `true`host przechwytuje wyjątki podczas uruchamiania i próbuje uruchomić serwer.</span><span class="sxs-lookup"><span data-stu-id="658a6-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="658a6-695">**Klucz:**`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="658a6-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="658a6-696">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="658a6-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="658a6-697">**Domyślnie:** `false` Domyślnie, chyba że aplikacja działa z Kestrel `true`za IIS, gdzie domyślnie jest .</span><span class="sxs-lookup"><span data-stu-id="658a6-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="658a6-698">**Zmienna środowiskowa:**`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="658a6-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="658a6-699">Aby ustawić tę wartość, `CaptureStartupErrors`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="658a6-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="658a6-700">Szczegółoweerrory</span><span class="sxs-lookup"><span data-stu-id="658a6-700">DetailedErrors</span></span>

<span data-ttu-id="658a6-701">Po włączeniu lub gdy `Development`środowisko jest , aplikacja przechwytuje szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="658a6-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="658a6-702">**Klucz:**`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="658a6-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="658a6-703">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="658a6-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="658a6-704">**Domyślnie**:`false`</span><span class="sxs-lookup"><span data-stu-id="658a6-704">**Default**: `false`</span></span>  
<span data-ttu-id="658a6-705">**Zmienna środowiskowa:**`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="658a6-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="658a6-706">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="658a6-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="658a6-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="658a6-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="658a6-708">Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do załadowania podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="658a6-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="658a6-709">Mimo że wartość konfiguracji domyślnie pusty ciąg, zestawy uruchamiania hostingu zawsze zawierają zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="658a6-710">Po udostępnieniu zestawów uruchamiania hostingu są one dodawane do zestawu aplikacji do ładowania, gdy aplikacja tworzy swoje typowe usługi podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="658a6-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="658a6-711">**Klucz:**`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="658a6-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="658a6-712">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-712">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-713">**Domyślnie**: Pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="658a6-713">**Default**: Empty string</span></span>  
<span data-ttu-id="658a6-714">**Zmienna środowiskowa:**`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="658a6-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="658a6-715">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="658a6-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="658a6-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="658a6-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="658a6-717">Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do wykluczenia podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="658a6-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="658a6-718">**Klucz:**`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="658a6-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="658a6-719">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-719">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-720">**Domyślnie**: Pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="658a6-720">**Default**: Empty string</span></span>  
<span data-ttu-id="658a6-721">**Zmienna środowiskowa:**`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="658a6-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="658a6-722">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="658a6-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="658a6-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="658a6-723">HTTPS_Port</span></span>

<span data-ttu-id="658a6-724">Port przekierowania HTTPS.</span><span class="sxs-lookup"><span data-stu-id="658a6-724">The HTTPS redirect port.</span></span> <span data-ttu-id="658a6-725">Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="658a6-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="658a6-726">**Klucz:**`https_port`</span><span class="sxs-lookup"><span data-stu-id="658a6-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="658a6-727">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-727">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-728">**Domyślnie:** Wartość domyślna nie jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="658a6-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="658a6-729">**Zmienna środowiskowa:**`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="658a6-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="658a6-730">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="658a6-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="658a6-731">PreferHostingUrls (PreferHostingUrls)</span><span class="sxs-lookup"><span data-stu-id="658a6-731">PreferHostingUrls</span></span>

<span data-ttu-id="658a6-732">Wskazuje, czy host powinien nasłuchiwania adresów URL skonfigurowanych przy 200 000 `IWebHostBuilder` adresów URL skonfigurowanych z `IServer` implementacją.</span><span class="sxs-lookup"><span data-stu-id="658a6-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="658a6-733">**Klucz:**`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="658a6-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="658a6-734">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="658a6-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="658a6-735">**Domyślnie**:`true`</span><span class="sxs-lookup"><span data-stu-id="658a6-735">**Default**: `true`</span></span>  
<span data-ttu-id="658a6-736">**Zmienna środowiskowa:**`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="658a6-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="658a6-737">Aby ustawić tę wartość, użyj `PreferHostingUrls`zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="658a6-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="658a6-738">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="658a6-738">PreventHostingStartup</span></span>

<span data-ttu-id="658a6-739">Zapobiega automatycznemu obciążaniu zestawów uruchamiania hostingu, w tym zestawów uruchamiania hostingu skonfigurowanych przez zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="658a6-740">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="658a6-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="658a6-741">**Klucz:**`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="658a6-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="658a6-742">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="658a6-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="658a6-743">**Domyślnie**:`false`</span><span class="sxs-lookup"><span data-stu-id="658a6-743">**Default**: `false`</span></span>  
<span data-ttu-id="658a6-744">**Zmienna środowiskowa:**`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="658a6-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="658a6-745">Aby ustawić tę wartość, użyj `UseSetting` zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="658a6-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="658a6-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="658a6-746">StartupAssembly</span></span>

<span data-ttu-id="658a6-747">Zestaw do wyszukiwania `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="658a6-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="658a6-748">**Klucz:**`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="658a6-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="658a6-749">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-749">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-750">**Domyślnie**: Zespół aplikacji</span><span class="sxs-lookup"><span data-stu-id="658a6-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="658a6-751">**Zmienna środowiskowa:**`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="658a6-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="658a6-752">Aby ustawić tę wartość, należy `UseStartup`użyć zmiennej środowiskowej lub wywołania .</span><span class="sxs-lookup"><span data-stu-id="658a6-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="658a6-753">`UseStartup`może przyjąć nazwę`string`zestawu ( )`TStartup`lub typ ( ).</span><span class="sxs-lookup"><span data-stu-id="658a6-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="658a6-754">Jeśli `UseStartup` wywoływanych jest wiele metod, pierwszeństwo ma ostatnia.</span><span class="sxs-lookup"><span data-stu-id="658a6-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="658a6-755">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="658a6-755">URLs</span></span>

<span data-ttu-id="658a6-756">Lista adresów IP lub adresów hostów rozdzielanych średnikami z portami i protokołami, na których serwer powinien nasłuchiwał żądań.</span><span class="sxs-lookup"><span data-stu-id="658a6-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="658a6-757">Na przykład `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="658a6-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="658a6-758">Użyj\*" ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwach hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="658a6-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="658a6-759">Protokół (`http://` `https://`lub ) musi być dołączony do każdego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="658a6-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="658a6-760">Obsługiwane formaty różnią się w zależności od serwera.</span><span class="sxs-lookup"><span data-stu-id="658a6-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="658a6-761">**Klucz:**`urls`</span><span class="sxs-lookup"><span data-stu-id="658a6-761">**Key**: `urls`</span></span>  
<span data-ttu-id="658a6-762">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-762">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-763">**Domyślnie**: `http://localhost:5000` i`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="658a6-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="658a6-764">**Zmienna środowiskowa:**`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="658a6-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="658a6-765">Aby ustawić tę wartość, użyj `UseUrls`zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="658a6-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="658a6-766">Pustułka ma własny interfejs API konfiguracji punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="658a6-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="658a6-767">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="658a6-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="658a6-768">Webroot</span><span class="sxs-lookup"><span data-stu-id="658a6-768">WebRoot</span></span>

<span data-ttu-id="658a6-769">[Właściwość IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) określa względną ścieżkę do statycznych zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="658a6-770">Jeśli ścieżka nie istnieje, używany jest dostawca plików bez operacji.</span><span class="sxs-lookup"><span data-stu-id="658a6-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="658a6-771">**Klucz:**`webroot`</span><span class="sxs-lookup"><span data-stu-id="658a6-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="658a6-772">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="658a6-772">**Type**: `string`</span></span>  
<span data-ttu-id="658a6-773">**Domyślnie**: `wwwroot`Wartość domyślna to .</span><span class="sxs-lookup"><span data-stu-id="658a6-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="658a6-774">Musi istnieć ścieżka do *{content root}/wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="658a6-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="658a6-775">**Zmienna środowiskowa:**`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="658a6-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="658a6-776">Aby ustawić tę wartość, należy `UseWebRoot` użyć `IWebHostBuilder`zmiennej środowiskowej lub wywołać na:</span><span class="sxs-lookup"><span data-stu-id="658a6-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="658a6-777">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="658a6-777">For more information, see:</span></span>

* [<span data-ttu-id="658a6-778">Podstawy: Korzeń sieci Web</span><span class="sxs-lookup"><span data-stu-id="658a6-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="658a6-779">ContentRoot (Typ treści)</span><span class="sxs-lookup"><span data-stu-id="658a6-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="658a6-780">Zarządzanie okresem istnienia hosta</span><span class="sxs-lookup"><span data-stu-id="658a6-780">Manage the host lifetime</span></span>

<span data-ttu-id="658a6-781">Wywołaj metody <xref:Microsoft.Extensions.Hosting.IHost> na wbudowanej implementacji, aby uruchomić i zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="658a6-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="658a6-782">Te metody <xref:Microsoft.Extensions.Hosting.IHostedService> mają wpływ na wszystkie implementacje, które są zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="658a6-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="658a6-783">Run</span><span class="sxs-lookup"><span data-stu-id="658a6-783">Run</span></span>

<span data-ttu-id="658a6-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący, dopóki host nie zostanie zamknięty.</span><span class="sxs-lookup"><span data-stu-id="658a6-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="658a6-785">RunAsync ( RunAsync )</span><span class="sxs-lookup"><span data-stu-id="658a6-785">RunAsync</span></span>

<span data-ttu-id="658a6-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia systemu.</span><span class="sxs-lookup"><span data-stu-id="658a6-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="658a6-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="658a6-787">RunConsoleAsync</span></span>

<span data-ttu-id="658a6-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>umożliwia obsługę konsoli, buduje i uruchamia hosta i czeka na <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM, aby zamknąć.</span><span class="sxs-lookup"><span data-stu-id="658a6-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="658a6-789">Uruchamianie</span><span class="sxs-lookup"><span data-stu-id="658a6-789">Start</span></span>

<span data-ttu-id="658a6-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="658a6-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="658a6-791">StartAsync (StartAsync)</span><span class="sxs-lookup"><span data-stu-id="658a6-791">StartAsync</span></span>

<span data-ttu-id="658a6-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia hosta i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="658a6-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="658a6-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na `StartAsync`początku , który czeka, aż zakończy się przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="658a6-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="658a6-794">Może to służyć do opóźnienia uruchamiania, dopóki nie zostanie zasygnaligozowana przez zdarzenie zewnętrzne.</span><span class="sxs-lookup"><span data-stu-id="658a6-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="658a6-795">StopAsync (StopAsync)</span><span class="sxs-lookup"><span data-stu-id="658a6-795">StopAsync</span></span>

<span data-ttu-id="658a6-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w podanym limit czasu.</span><span class="sxs-lookup"><span data-stu-id="658a6-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="658a6-797">WaitForShutdown (WaitForShutdown)</span><span class="sxs-lookup"><span data-stu-id="658a6-797">WaitForShutdown</span></span>

<span data-ttu-id="658a6-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje wątek wywołujący, dopóki zamknięcie zostanie wywołane przez IHostLifetime, na przykład przez <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="658a6-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="658a6-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="658a6-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="658a6-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task> zwraca, który kończy się po zamknięciu jest <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>wyzwalany za pośrednictwem danego tokenu i wywołania .</span><span class="sxs-lookup"><span data-stu-id="658a6-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="658a6-801">Sterowanie zewnętrzne</span><span class="sxs-lookup"><span data-stu-id="658a6-801">External control</span></span>

<span data-ttu-id="658a6-802">Bezpośrednią kontrolę okresu istnienia hosta można osiągnąć za pomocą metod, które można wywołać zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="658a6-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="658a6-803">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="658a6-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
