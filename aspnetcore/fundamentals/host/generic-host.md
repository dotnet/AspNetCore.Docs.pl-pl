---
title: Host ogólny platformy .NET
author: rick-anderson
description: Dowiedz się więcej o hoście ogólnym .NET Core, który jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 454216cec72048217ede412f8ff6d4261f7353b1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417633"
---
# <a name="net-generic-host"></a><span data-ttu-id="bb019-103">Host ogólny platformy .NET</span><span class="sxs-lookup"><span data-stu-id="bb019-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="bb019-104">W tym artykule przedstawiono hosta ogólnego .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) i zawiera wskazówki dotyczące używania go.</span><span class="sxs-lookup"><span data-stu-id="bb019-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="bb019-105">Co to jest gospodarz?</span><span class="sxs-lookup"><span data-stu-id="bb019-105">What's a host?</span></span>

<span data-ttu-id="bb019-106">*Host* jest obiektem, który hermetyzuje zasoby aplikacji, takie jak:</span><span class="sxs-lookup"><span data-stu-id="bb019-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="bb019-107">Wstrzyknięcie zależności (DI)</span><span class="sxs-lookup"><span data-stu-id="bb019-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="bb019-108">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="bb019-108">Logging</span></span>
* <span data-ttu-id="bb019-109">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="bb019-109">Configuration</span></span>
* <span data-ttu-id="bb019-110">`IHostedService`Implementacje</span><span class="sxs-lookup"><span data-stu-id="bb019-110">`IHostedService` implementations</span></span>

<span data-ttu-id="bb019-111">Po uruchomieniu hosta wywołuje `IHostedService.StartAsync` każdą <xref:Microsoft.Extensions.Hosting.IHostedService> implementację, która znajduje się w kontenerze DI.</span><span class="sxs-lookup"><span data-stu-id="bb019-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="bb019-112">W aplikacji sieci web `IHostedService` jedną z implementacji jest usługa sieci web, która uruchamia [implementację serwera HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="bb019-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="bb019-113">Głównym powodem włączenia wszystkich współzależnych zasobów aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: kontrola nad uruchamianiem aplikacji i wdzięczne zamykanie.</span><span class="sxs-lookup"><span data-stu-id="bb019-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="bb019-114">W wersjach ASP.NET Core wcześniejszych niż 3.0 [host sieci Web](xref:fundamentals/host/web-host) jest używany dla obciążeń HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb019-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="bb019-115">Host sieci Web nie jest już zalecany dla aplikacji sieci Web i pozostaje dostępny tylko dla zgodności z powrotem.</span><span class="sxs-lookup"><span data-stu-id="bb019-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="bb019-116">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="bb019-116">Set up a host</span></span>

<span data-ttu-id="bb019-117">Host jest zazwyczaj skonfigurowany, zbudowany i uruchamiany `Program` przez kod w klasie.</span><span class="sxs-lookup"><span data-stu-id="bb019-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="bb019-118">Metoda: `Main`</span><span class="sxs-lookup"><span data-stu-id="bb019-118">The `Main` method:</span></span>

* <span data-ttu-id="bb019-119">Wywołuje `CreateHostBuilder` metodę tworzenia i konfigurowania obiektu konstruktora.</span><span class="sxs-lookup"><span data-stu-id="bb019-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="bb019-120">`Build` Wywołania `Run` i metody na obiekcie konstruktora.</span><span class="sxs-lookup"><span data-stu-id="bb019-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="bb019-121">Oto *Program.cs* kod dla obciążenia innego niż HTTP, `IHostedService` z pojedynczą implementacją dodaną do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="bb019-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="bb019-122">W przypadku obciążenia `Main` HTTP metoda jest `CreateHostBuilder` `ConfigureWebHostDefaults`taka sama, ale wywołuje:</span><span class="sxs-lookup"><span data-stu-id="bb019-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="bb019-123">Jeśli aplikacja używa entity framework core, nie należy zmieniać `CreateHostBuilder` nazwę lub podpis metody.</span><span class="sxs-lookup"><span data-stu-id="bb019-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="bb019-124">[Narzędzia Entity Framework Core](/ef/core/miscellaneous/cli/) oczekują, aby znaleźć `CreateHostBuilder` metodę, która konfiguruje hosta bez uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="bb019-125">Aby uzyskać więcej informacji, zobacz [Tworzenie dbcontext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="bb019-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="bb019-126">Domyślne ustawienia konstruktora</span><span class="sxs-lookup"><span data-stu-id="bb019-126">Default builder settings</span></span>

<span data-ttu-id="bb019-127">Metoda: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="bb019-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="bb019-128">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na <xref:System.IO.Directory.GetCurrentDirectory*>ścieżkę zwróconą przez program .</span><span class="sxs-lookup"><span data-stu-id="bb019-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="bb019-129">Ładuje konfigurację hosta z:</span><span class="sxs-lookup"><span data-stu-id="bb019-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="bb019-130">Zmienne środowiskowe poprzedzone . `DOTNET_`</span><span class="sxs-lookup"><span data-stu-id="bb019-130">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="bb019-131">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="bb019-131">Command-line arguments.</span></span>
* <span data-ttu-id="bb019-132">Ładuje konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="bb019-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="bb019-133">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="bb019-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="bb019-134">*appsettings. {Środowisko}.json*.</span><span class="sxs-lookup"><span data-stu-id="bb019-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="bb019-135">[Tajny menedżer,](xref:security/app-secrets) gdy aplikacja `Development` działa w środowisku.</span><span class="sxs-lookup"><span data-stu-id="bb019-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="bb019-136">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="bb019-136">Environment variables.</span></span>
  * <span data-ttu-id="bb019-137">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="bb019-137">Command-line arguments.</span></span>
* <span data-ttu-id="bb019-138">Dodaje następujących dostawców [rejestrowania:](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="bb019-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="bb019-139">Konsola</span><span class="sxs-lookup"><span data-stu-id="bb019-139">Console</span></span>
  * <span data-ttu-id="bb019-140">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="bb019-140">Debug</span></span>
  * <span data-ttu-id="bb019-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="bb019-141">EventSource</span></span>
  * <span data-ttu-id="bb019-142">EventLog (tylko w przypadku pracy w systemie Windows)</span><span class="sxs-lookup"><span data-stu-id="bb019-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="bb019-143">Włącza [sprawdzanie poprawności zakresu](xref:fundamentals/dependency-injection#scope-validation) i sprawdzania poprawności [zależności,](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) gdy środowisko jest rozwój.</span><span class="sxs-lookup"><span data-stu-id="bb019-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="bb019-144">Metoda: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="bb019-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="bb019-145">Ładuje konfigurację hosta ze zmiennych środowiskowych poprzedzonych programem `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="bb019-145">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="bb019-146">Ustawia serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci web i konfiguruje go przy użyciu dostawców konfiguracji hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="bb019-147">Aby zapoznać się z domyślnymi <xref:fundamentals/servers/kestrel#kestrel-options>opcjami serwera Kestrel, zobacz .</span><span class="sxs-lookup"><span data-stu-id="bb019-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="bb019-148">Dodaje [oprogramowanie pośredniczące filtrowania hosta](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="bb019-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="bb019-149">Dodaje [oprogramowanie pośredniczące nagłówków przesyłanych dalej,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest `true`równe .</span><span class="sxs-lookup"><span data-stu-id="bb019-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="bb019-150">Umożliwia integrację z usługiami IIS.</span><span class="sxs-lookup"><span data-stu-id="bb019-150">Enables IIS integration.</span></span> <span data-ttu-id="bb019-151">Aby zapoznać się z <xref:host-and-deploy/iis/index#iis-options>domyślnymi opcjami iis, zobacz .</span><span class="sxs-lookup"><span data-stu-id="bb019-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="bb019-152">W sekcji [Ustawienia dla wszystkich typów aplikacji](#settings-for-all-app-types) i Ustawienia aplikacji sieci [Web](#settings-for-web-apps) w dalszej części tego artykułu pokazano, jak zastąpić domyślne ustawienia konstruktora.</span><span class="sxs-lookup"><span data-stu-id="bb019-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="bb019-153">Usługi świadczone w ramach ram</span><span class="sxs-lookup"><span data-stu-id="bb019-153">Framework-provided services</span></span>

<span data-ttu-id="bb019-154">Następujące usługi są rejestrowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="bb019-154">The following services are registered automatically:</span></span>

* [<span data-ttu-id="bb019-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="bb019-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="bb019-156">IHostLifetime (czas pracy)</span><span class="sxs-lookup"><span data-stu-id="bb019-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="bb019-157">IHostEnvironment / IWebHostŚrodowicielenie</span><span class="sxs-lookup"><span data-stu-id="bb019-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="bb019-158">Aby uzyskać więcej informacji na <xref:fundamentals/dependency-injection#framework-provided-services>temat usług świadczonych w ramach ram, zobacz .</span><span class="sxs-lookup"><span data-stu-id="bb019-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="bb019-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="bb019-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="bb019-160"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Wstrzyknąć `IApplicationLifetime`(dawniej) usługi do dowolnej klasy do obsługi zadań po starcie i wdzięku zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="bb019-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="bb019-161">Trzy właściwości w interfejsie są tokeny anulowania używane do rejestrowania uruchamiania aplikacji i metody obsługi zdarzeń zatrzymania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="bb019-162">Interfejs zawiera również `StopApplication` metodę.</span><span class="sxs-lookup"><span data-stu-id="bb019-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="bb019-163">Poniższy przykład `IHostedService` jest implementacją, która rejestruje `IHostApplicationLifetime` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="bb019-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="bb019-164">IHostLifetime (czas pracy)</span><span class="sxs-lookup"><span data-stu-id="bb019-164">IHostLifetime</span></span>

<span data-ttu-id="bb019-165">Implementacja <xref:Microsoft.Extensions.Hosting.IHostLifetime> kontroluje, kiedy host uruchamia się i kiedy zatrzymuje.</span><span class="sxs-lookup"><span data-stu-id="bb019-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="bb019-166">Używana jest ostatnia zarejestrowana implementacja.</span><span class="sxs-lookup"><span data-stu-id="bb019-166">The last implementation registered is used.</span></span>

<span data-ttu-id="bb019-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`jest domyślną `IHostLifetime` implementacją.</span><span class="sxs-lookup"><span data-stu-id="bb019-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="bb019-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="bb019-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="bb019-169">Nasłuchuje <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM i wywołuje, <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="bb019-169">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="bb019-170">Odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="bb019-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="bb019-171">IHostŚrodowienie</span><span class="sxs-lookup"><span data-stu-id="bb019-171">IHostEnvironment</span></span>

<span data-ttu-id="bb019-172">Wstrzyknąć <xref:Microsoft.Extensions.Hosting.IHostEnvironment> usługę do klasy, aby uzyskać informacje o następujących ustawieniach:</span><span class="sxs-lookup"><span data-stu-id="bb019-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="bb019-173">Applicationname</span><span class="sxs-lookup"><span data-stu-id="bb019-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="bb019-174">Nazwa środowiska</span><span class="sxs-lookup"><span data-stu-id="bb019-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="bb019-175">Ścieżka contentrootpath</span><span class="sxs-lookup"><span data-stu-id="bb019-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="bb019-176">Aplikacje sieci `IWebHostEnvironment` Web implementują `IHostEnvironment` interfejs, który dziedziczy i dodaje [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="bb019-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="bb019-177">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="bb019-177">Host configuration</span></span>

<span data-ttu-id="bb019-178">Konfiguracja hosta jest używana <xref:Microsoft.Extensions.Hosting.IHostEnvironment> dla właściwości implementacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-178">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="bb019-179">Konfiguracja hosta jest dostępna w <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>pliku [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) wewnątrz .</span><span class="sxs-lookup"><span data-stu-id="bb019-179">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="bb019-180">Po `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , jest zastępowany config aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-180">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="bb019-181">Aby dodać konfigurację <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`hosta, zadzwoń .</span><span class="sxs-lookup"><span data-stu-id="bb019-181">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="bb019-182">`ConfigureHostConfiguration`można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="bb019-182">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="bb019-183">Host używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="bb019-183">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="bb019-184">Dostawca zmiennych środowiskowych `DOTNET_` z prefiksem i `CreateDefaultBuilder`argumentami wiersza polecenia są uwzględniane przez program .</span><span class="sxs-lookup"><span data-stu-id="bb019-184">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="bb019-185">W przypadku aplikacji sieci web dodawany jest dostawca zmiennych środowiskowych z prefiksem. `ASPNETCORE_`</span><span class="sxs-lookup"><span data-stu-id="bb019-185">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="bb019-186">Prefiks jest usuwany podczas odczytu zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="bb019-186">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="bb019-187">Na przykład wartość zmiennej `ASPNETCORE_ENVIRONMENT` środowiskowej dla staje `environment` się wartością konfiguracji hosta dla klucza.</span><span class="sxs-lookup"><span data-stu-id="bb019-187">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="bb019-188">W poniższym przykładzie tworzy konfigurację hosta:</span><span class="sxs-lookup"><span data-stu-id="bb019-188">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="bb019-189">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="bb019-189">App configuration</span></span>

<span data-ttu-id="bb019-190">Konfiguracja aplikacji jest <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder`tworzona przez wywołanie .</span><span class="sxs-lookup"><span data-stu-id="bb019-190">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="bb019-191">`ConfigureAppConfiguration`można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="bb019-191">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="bb019-192">Aplikacja używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="bb019-192">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="bb019-193">Konfiguracja utworzona `ConfigureAppConfiguration` przez jest dostępna w [witrynie HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) dla kolejnych operacji i jako usługa z DI.</span><span class="sxs-lookup"><span data-stu-id="bb019-193">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="bb019-194">Konfiguracja hosta jest również dodawana do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-194">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="bb019-195">Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="bb019-195">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="bb019-196">Ustawienia dla wszystkich typów aplikacji</span><span class="sxs-lookup"><span data-stu-id="bb019-196">Settings for all app types</span></span>

<span data-ttu-id="bb019-197">W tej sekcji wymieniono ustawienia hosta, które dotyczą zarówno obciążeń HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb019-197">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="bb019-198">Domyślnie zmienne środowiskowe używane do konfigurowania `DOTNET_` `ASPNETCORE_` tych ustawień mogą mieć lub prefiks.</span><span class="sxs-lookup"><span data-stu-id="bb019-198">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="bb019-199">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="bb019-199">ApplicationName</span></span>

<span data-ttu-id="bb019-200">[Właściwość IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) jest ustawiana z konfiguracji hosta podczas konstruowaniu hosta.</span><span class="sxs-lookup"><span data-stu-id="bb019-200">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="bb019-201">**Klucz:**`applicationName`</span><span class="sxs-lookup"><span data-stu-id="bb019-201">**Key**: `applicationName`</span></span>  
<span data-ttu-id="bb019-202">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-202">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-203">**Domyślnie**: Nazwa zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-203">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="bb019-204">**Zmienna środowiskowa:**`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="bb019-204">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="bb019-205">Aby ustawić tę wartość, należy użyć zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="bb019-205">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="bb019-206">Ścieżka contentrootpath</span><span class="sxs-lookup"><span data-stu-id="bb019-206">ContentRootPath</span></span>

<span data-ttu-id="bb019-207">[Właściwość IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="bb019-207">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="bb019-208">Jeśli ścieżka nie istnieje, host nie uruchamia się.</span><span class="sxs-lookup"><span data-stu-id="bb019-208">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="bb019-209">**Klucz:**`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="bb019-209">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="bb019-210">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-210">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-211">**Domyślnie**: Folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-211">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="bb019-212">**Zmienna środowiskowa:**`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="bb019-212">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="bb019-213">Aby ustawić tę wartość, należy `UseContentRoot` użyć `IHostBuilder`zmiennej środowiskowej lub wywołać na:</span><span class="sxs-lookup"><span data-stu-id="bb019-213">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="bb019-214">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="bb019-214">For more information, see:</span></span>

* [<span data-ttu-id="bb019-215">Podstawy: Katalog główny treści</span><span class="sxs-lookup"><span data-stu-id="bb019-215">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="bb019-216">Webroot</span><span class="sxs-lookup"><span data-stu-id="bb019-216">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="bb019-217">Nazwa środowiska</span><span class="sxs-lookup"><span data-stu-id="bb019-217">EnvironmentName</span></span>

<span data-ttu-id="bb019-218">[Właściwość IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) można ustawić na dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="bb019-218">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="bb019-219">Wartości zdefiniowane `Development`przez `Staging`ramy `Production`obejmują , i .</span><span class="sxs-lookup"><span data-stu-id="bb019-219">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="bb019-220">W wartościach nie rozróżnia się wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="bb019-220">Values aren't case-sensitive.</span></span>

<span data-ttu-id="bb019-221">**Klucz:**`environment`</span><span class="sxs-lookup"><span data-stu-id="bb019-221">**Key**: `environment`</span></span>  
<span data-ttu-id="bb019-222">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-222">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-223">**Domyślnie**:`Production`</span><span class="sxs-lookup"><span data-stu-id="bb019-223">**Default**: `Production`</span></span>  
<span data-ttu-id="bb019-224">**Zmienna środowiskowa:**`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="bb019-224">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="bb019-225">Aby ustawić tę wartość, należy `UseEnvironment` użyć `IHostBuilder`zmiennej środowiskowej lub wywołać na:</span><span class="sxs-lookup"><span data-stu-id="bb019-225">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="bb019-226">Shutdowntimeout</span><span class="sxs-lookup"><span data-stu-id="bb019-226">ShutdownTimeout</span></span>

<span data-ttu-id="bb019-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) ustawia limit <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>czasu dla .</span><span class="sxs-lookup"><span data-stu-id="bb019-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="bb019-228">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="bb019-228">The default value is five seconds.</span></span>  <span data-ttu-id="bb019-229">W okresie limitu czasu host:</span><span class="sxs-lookup"><span data-stu-id="bb019-229">During the timeout period, the host:</span></span>

* <span data-ttu-id="bb019-230">Wyzwala [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="bb019-230">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="bb019-231">Próbuje zatrzymać hostowane usługi, rejestrowanie błędów dla usług, które nie mogą się zatrzymać.</span><span class="sxs-lookup"><span data-stu-id="bb019-231">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="bb019-232">Jeśli limit czasu wygaśnie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-232">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="bb019-233">Usługi zatrzymują się nawet wtedy, gdy nie zakończyły przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="bb019-233">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="bb019-234">Jeśli usługi wymagają dodatkowego czasu, aby zatrzymać, zwiększyć limit czasu.</span><span class="sxs-lookup"><span data-stu-id="bb019-234">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="bb019-235">**Klucz:**`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="bb019-235">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="bb019-236">**Typ**:`int`</span><span class="sxs-lookup"><span data-stu-id="bb019-236">**Type**: `int`</span></span>  
<span data-ttu-id="bb019-237">**Domyślnie**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="bb019-237">**Default**: 5 seconds</span></span>  
<span data-ttu-id="bb019-238">**Zmienna środowiskowa:**`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="bb019-238">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="bb019-239">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub skonfiguruj `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="bb019-239">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="bb019-240">W poniższym przykładzie ustawia limit czasu na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="bb019-240">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="bb019-241">Wyłącz ponowne ładowanie konfiguracji aplikacji przy zmianie</span><span class="sxs-lookup"><span data-stu-id="bb019-241">Disable app configuration reload on change</span></span>

<span data-ttu-id="bb019-242">[Domyślnie](xref:fundamentals/configuration/index#default), *appsettings.json* i *appsettings.{ Środowisko}.json* są ponownie ładowane po zmianie pliku.</span><span class="sxs-lookup"><span data-stu-id="bb019-242">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="bb019-243">Aby wyłączyć to zachowanie przeładowania w ASP.NET Core 5.0 Preview `hostBuilder:reloadConfigOnChange` 3 lub nowszym, ustaw klawisz na `false`.</span><span class="sxs-lookup"><span data-stu-id="bb019-243">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="bb019-244">**Klucz:**`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="bb019-244">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="bb019-245">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="bb019-245">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="bb019-246">**Domyślnie**:`true`</span><span class="sxs-lookup"><span data-stu-id="bb019-246">**Default**: `true`</span></span>  
<span data-ttu-id="bb019-247">**Argument wiersza polecenia:**`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="bb019-247">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="bb019-248">**Zmienna środowiskowa:**`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="bb019-248">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="bb019-249">Separator`:`dwukropka ( ) nie działa z kluczami hierarchicznymi zmiennych środowiskowych na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="bb019-249">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="bb019-250">Aby uzyskać więcej informacji, zobacz [Zmienne środowiskowe](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="bb019-250">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="bb019-251">Ustawienia aplikacji internetowych</span><span class="sxs-lookup"><span data-stu-id="bb019-251">Settings for web apps</span></span>

<span data-ttu-id="bb019-252">Niektóre ustawienia hosta dotyczą tylko obciążeń HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb019-252">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="bb019-253">Domyślnie zmienne środowiskowe używane do konfigurowania `DOTNET_` `ASPNETCORE_` tych ustawień mogą mieć lub prefiks.</span><span class="sxs-lookup"><span data-stu-id="bb019-253">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="bb019-254">Dostępne `IWebHostBuilder` są metody rozszerzenia dla tych ustawień.</span><span class="sxs-lookup"><span data-stu-id="bb019-254">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="bb019-255">Przykłady kodu, które pokazują, jak `webBuilder` wywołać `IWebHostBuilder`metody rozszerzenia zakłada, że jest wystąpieniem , jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="bb019-255">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="bb019-256">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="bb019-256">CaptureStartupErrors</span></span>

<span data-ttu-id="bb019-257">Kiedy `false`, błędy podczas uruchamiania spowodować wyjście hosta.</span><span class="sxs-lookup"><span data-stu-id="bb019-257">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="bb019-258">Gdy `true`host przechwytuje wyjątki podczas uruchamiania i próbuje uruchomić serwer.</span><span class="sxs-lookup"><span data-stu-id="bb019-258">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="bb019-259">**Klucz:**`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="bb019-259">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="bb019-260">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="bb019-260">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="bb019-261">**Domyślnie:** `false` Domyślnie, chyba że aplikacja działa z Kestrel `true`za IIS, gdzie domyślnie jest .</span><span class="sxs-lookup"><span data-stu-id="bb019-261">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="bb019-262">**Zmienna środowiskowa:**`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="bb019-262">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="bb019-263">Aby ustawić tę wartość, `CaptureStartupErrors`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-263">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="bb019-264">Szczegółoweerrory</span><span class="sxs-lookup"><span data-stu-id="bb019-264">DetailedErrors</span></span>

<span data-ttu-id="bb019-265">Po włączeniu lub gdy `Development`środowisko jest , aplikacja przechwytuje szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="bb019-265">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="bb019-266">**Klucz:**`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="bb019-266">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="bb019-267">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="bb019-267">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="bb019-268">**Domyślnie**:`false`</span><span class="sxs-lookup"><span data-stu-id="bb019-268">**Default**: `false`</span></span>  
<span data-ttu-id="bb019-269">**Zmienna środowiskowa:**`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="bb019-269">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="bb019-270">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="bb019-271">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="bb019-271">HostingStartupAssemblies</span></span>

<span data-ttu-id="bb019-272">Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do załadowania podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="bb019-272">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="bb019-273">Mimo że wartość konfiguracji domyślnie pusty ciąg, zestawy uruchamiania hostingu zawsze zawierają zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-273">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="bb019-274">Po udostępnieniu zestawów uruchamiania hostingu są one dodawane do zestawu aplikacji do ładowania, gdy aplikacja tworzy swoje typowe usługi podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="bb019-274">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="bb019-275">**Klucz:**`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="bb019-275">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="bb019-276">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-276">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-277">**Domyślnie**: Pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="bb019-277">**Default**: Empty string</span></span>  
<span data-ttu-id="bb019-278">**Zmienna środowiskowa:**`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="bb019-278">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="bb019-279">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-279">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="bb019-280">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="bb019-280">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="bb019-281">Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do wykluczenia podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="bb019-281">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="bb019-282">**Klucz:**`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="bb019-282">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="bb019-283">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-283">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-284">**Domyślnie**: Pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="bb019-284">**Default**: Empty string</span></span>  
<span data-ttu-id="bb019-285">**Zmienna środowiskowa:**`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="bb019-285">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="bb019-286">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-286">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="bb019-287">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="bb019-287">HTTPS_Port</span></span>

<span data-ttu-id="bb019-288">Port przekierowania HTTPS.</span><span class="sxs-lookup"><span data-stu-id="bb019-288">The HTTPS redirect port.</span></span> <span data-ttu-id="bb019-289">Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="bb019-289">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="bb019-290">**Klucz:**`https_port`</span><span class="sxs-lookup"><span data-stu-id="bb019-290">**Key**: `https_port`</span></span>  
<span data-ttu-id="bb019-291">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-291">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-292">**Domyślnie:** Wartość domyślna nie jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="bb019-292">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="bb019-293">**Zmienna środowiskowa:**`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="bb019-293">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="bb019-294">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-294">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="bb019-295">PreferHostingUrls (PreferHostingUrls)</span><span class="sxs-lookup"><span data-stu-id="bb019-295">PreferHostingUrls</span></span>

<span data-ttu-id="bb019-296">Wskazuje, czy host powinien nasłuchiwania adresów URL skonfigurowanych przy 200 000 `IWebHostBuilder` adresów URL skonfigurowanych z `IServer` implementacją.</span><span class="sxs-lookup"><span data-stu-id="bb019-296">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="bb019-297">**Klucz:**`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="bb019-297">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="bb019-298">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="bb019-298">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="bb019-299">**Domyślnie**:`true`</span><span class="sxs-lookup"><span data-stu-id="bb019-299">**Default**: `true`</span></span>  
<span data-ttu-id="bb019-300">**Zmienna środowiskowa:**`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="bb019-300">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="bb019-301">Aby ustawić tę wartość, użyj `PreferHostingUrls`zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-301">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="bb019-302">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="bb019-302">PreventHostingStartup</span></span>

<span data-ttu-id="bb019-303">Zapobiega automatycznemu obciążaniu zestawów uruchamiania hostingu, w tym zestawów uruchamiania hostingu skonfigurowanych przez zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-303">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="bb019-304">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="bb019-304">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="bb019-305">**Klucz:**`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="bb019-305">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="bb019-306">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="bb019-306">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="bb019-307">**Domyślnie**:`false`</span><span class="sxs-lookup"><span data-stu-id="bb019-307">**Default**: `false`</span></span>  
<span data-ttu-id="bb019-308">**Zmienna środowiskowa:**`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="bb019-308">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="bb019-309">Aby ustawić tę wartość, użyj `UseSetting` zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-309">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="bb019-310">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="bb019-310">StartupAssembly</span></span>

<span data-ttu-id="bb019-311">Zestaw do wyszukiwania `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="bb019-311">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="bb019-312">**Klucz:**`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="bb019-312">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="bb019-313">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-313">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-314">**Domyślnie**: Zespół aplikacji</span><span class="sxs-lookup"><span data-stu-id="bb019-314">**Default**: The app's assembly</span></span>  
<span data-ttu-id="bb019-315">**Zmienna środowiskowa:**`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="bb019-315">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="bb019-316">Aby ustawić tę wartość, należy `UseStartup`użyć zmiennej środowiskowej lub wywołania .</span><span class="sxs-lookup"><span data-stu-id="bb019-316">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="bb019-317">`UseStartup`może przyjąć nazwę`string`zestawu ( )`TStartup`lub typ ( ).</span><span class="sxs-lookup"><span data-stu-id="bb019-317">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="bb019-318">Jeśli `UseStartup` wywoływanych jest wiele metod, pierwszeństwo ma ostatnia.</span><span class="sxs-lookup"><span data-stu-id="bb019-318">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="bb019-319">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="bb019-319">URLs</span></span>

<span data-ttu-id="bb019-320">Lista adresów IP lub adresów hostów rozdzielanych średnikami z portami i protokołami, na których serwer powinien nasłuchiwał żądań.</span><span class="sxs-lookup"><span data-stu-id="bb019-320">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="bb019-321">Na przykład `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="bb019-321">For example, `http://localhost:123`.</span></span> <span data-ttu-id="bb019-322">Użyj\*" ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwach hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="bb019-322">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="bb019-323">Protokół (`http://` `https://`lub ) musi być dołączony do każdego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="bb019-323">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="bb019-324">Obsługiwane formaty różnią się w zależności od serwera.</span><span class="sxs-lookup"><span data-stu-id="bb019-324">Supported formats vary among servers.</span></span>

<span data-ttu-id="bb019-325">**Klucz:**`urls`</span><span class="sxs-lookup"><span data-stu-id="bb019-325">**Key**: `urls`</span></span>  
<span data-ttu-id="bb019-326">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-326">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-327">**Domyślnie**: `http://localhost:5000` i`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="bb019-327">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="bb019-328">**Zmienna środowiskowa:**`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="bb019-328">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="bb019-329">Aby ustawić tę wartość, użyj `UseUrls`zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-329">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="bb019-330">Pustułka ma własny interfejs API konfiguracji punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="bb019-330">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="bb019-331">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="bb019-331">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="bb019-332">Webroot</span><span class="sxs-lookup"><span data-stu-id="bb019-332">WebRoot</span></span>

<span data-ttu-id="bb019-333">Względna ścieżka do zasobów statycznych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-333">The relative path to the app's static assets.</span></span>

<span data-ttu-id="bb019-334">**Klucz:**`webroot`</span><span class="sxs-lookup"><span data-stu-id="bb019-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="bb019-335">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-335">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-336">**Domyślnie**: `wwwroot`Wartość domyślna to .</span><span class="sxs-lookup"><span data-stu-id="bb019-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="bb019-337">Musi istnieć ścieżka do *{content root}/wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="bb019-337">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="bb019-338">Jeśli ścieżka nie istnieje, używany jest dostawca plików bez operacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-338">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="bb019-339">**Zmienna środowiskowa:**`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="bb019-339">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="bb019-340">Aby ustawić tę wartość, użyj `UseWebRoot`zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-340">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="bb019-341">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="bb019-341">For more information, see:</span></span>

* [<span data-ttu-id="bb019-342">Podstawy: Korzeń sieci Web</span><span class="sxs-lookup"><span data-stu-id="bb019-342">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="bb019-343">Ścieżka contentrootpath</span><span class="sxs-lookup"><span data-stu-id="bb019-343">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="bb019-344">Zarządzanie okresem istnienia hosta</span><span class="sxs-lookup"><span data-stu-id="bb019-344">Manage the host lifetime</span></span>

<span data-ttu-id="bb019-345">Wywołaj metody <xref:Microsoft.Extensions.Hosting.IHost> na wbudowanej implementacji, aby uruchomić i zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="bb019-345">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="bb019-346">Te metody <xref:Microsoft.Extensions.Hosting.IHostedService> mają wpływ na wszystkie implementacje, które są zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="bb019-346">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="bb019-347">Run</span><span class="sxs-lookup"><span data-stu-id="bb019-347">Run</span></span>

<span data-ttu-id="bb019-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący, dopóki host nie zostanie zamknięty.</span><span class="sxs-lookup"><span data-stu-id="bb019-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="bb019-349">RunAsync ( RunAsync )</span><span class="sxs-lookup"><span data-stu-id="bb019-349">RunAsync</span></span>

<span data-ttu-id="bb019-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia systemu.</span><span class="sxs-lookup"><span data-stu-id="bb019-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="bb019-351">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="bb019-351">RunConsoleAsync</span></span>

<span data-ttu-id="bb019-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>umożliwia obsługę konsoli, buduje i uruchamia hosta i czeka na <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM, aby zamknąć.</span><span class="sxs-lookup"><span data-stu-id="bb019-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="bb019-353">Uruchamianie</span><span class="sxs-lookup"><span data-stu-id="bb019-353">Start</span></span>

<span data-ttu-id="bb019-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="bb019-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="bb019-355">StartAsync (StartAsync)</span><span class="sxs-lookup"><span data-stu-id="bb019-355">StartAsync</span></span>

<span data-ttu-id="bb019-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia hosta i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="bb019-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="bb019-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na `StartAsync`początku , który czeka, aż zakończy się przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="bb019-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="bb019-358">Może to służyć do opóźnienia uruchamiania, dopóki nie zostanie zasygnaligozowana przez zdarzenie zewnętrzne.</span><span class="sxs-lookup"><span data-stu-id="bb019-358">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="bb019-359">StopAsync (StopAsync)</span><span class="sxs-lookup"><span data-stu-id="bb019-359">StopAsync</span></span>

<span data-ttu-id="bb019-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w podanym limit czasu.</span><span class="sxs-lookup"><span data-stu-id="bb019-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="bb019-361">WaitForShutdown (WaitForShutdown)</span><span class="sxs-lookup"><span data-stu-id="bb019-361">WaitForShutdown</span></span>

<span data-ttu-id="bb019-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje wątek wywołujący, dopóki zamknięcie zostanie wywołane przez IHostLifetime, na przykład przez <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="bb019-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="bb019-363">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="bb019-363">WaitForShutdownAsync</span></span>

<span data-ttu-id="bb019-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task> zwraca, który kończy się po zamknięciu jest <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>wyzwalany za pośrednictwem danego tokenu i wywołania .</span><span class="sxs-lookup"><span data-stu-id="bb019-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="bb019-365">Sterowanie zewnętrzne</span><span class="sxs-lookup"><span data-stu-id="bb019-365">External control</span></span>

<span data-ttu-id="bb019-366">Bezpośrednią kontrolę okresu istnienia hosta można osiągnąć za pomocą metod, które można wywołać zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="bb019-366">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="bb019-367">W tym artykule przedstawiono hosta ogólnego .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) i zawiera wskazówki dotyczące używania go.</span><span class="sxs-lookup"><span data-stu-id="bb019-367">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="bb019-368">Co to jest gospodarz?</span><span class="sxs-lookup"><span data-stu-id="bb019-368">What's a host?</span></span>

<span data-ttu-id="bb019-369">*Host* jest obiektem, który hermetyzuje zasoby aplikacji, takie jak:</span><span class="sxs-lookup"><span data-stu-id="bb019-369">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="bb019-370">Wstrzyknięcie zależności (DI)</span><span class="sxs-lookup"><span data-stu-id="bb019-370">Dependency injection (DI)</span></span>
* <span data-ttu-id="bb019-371">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="bb019-371">Logging</span></span>
* <span data-ttu-id="bb019-372">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="bb019-372">Configuration</span></span>
* <span data-ttu-id="bb019-373">`IHostedService`Implementacje</span><span class="sxs-lookup"><span data-stu-id="bb019-373">`IHostedService` implementations</span></span>

<span data-ttu-id="bb019-374">Po uruchomieniu hosta wywołuje `IHostedService.StartAsync` każdą <xref:Microsoft.Extensions.Hosting.IHostedService> implementację, która znajduje się w kontenerze DI.</span><span class="sxs-lookup"><span data-stu-id="bb019-374">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="bb019-375">W aplikacji sieci web `IHostedService` jedną z implementacji jest usługa sieci web, która uruchamia [implementację serwera HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="bb019-375">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="bb019-376">Głównym powodem włączenia wszystkich współzależnych zasobów aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: kontrola nad uruchamianiem aplikacji i wdzięczne zamykanie.</span><span class="sxs-lookup"><span data-stu-id="bb019-376">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="bb019-377">W wersjach ASP.NET Core wcześniejszych niż 3.0 [host sieci Web](xref:fundamentals/host/web-host) jest używany dla obciążeń HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb019-377">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="bb019-378">Host sieci Web nie jest już zalecany dla aplikacji sieci Web i pozostaje dostępny tylko dla zgodności z powrotem.</span><span class="sxs-lookup"><span data-stu-id="bb019-378">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="bb019-379">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="bb019-379">Set up a host</span></span>

<span data-ttu-id="bb019-380">Host jest zazwyczaj skonfigurowany, zbudowany i uruchamiany `Program` przez kod w klasie.</span><span class="sxs-lookup"><span data-stu-id="bb019-380">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="bb019-381">Metoda: `Main`</span><span class="sxs-lookup"><span data-stu-id="bb019-381">The `Main` method:</span></span>

* <span data-ttu-id="bb019-382">Wywołuje `CreateHostBuilder` metodę tworzenia i konfigurowania obiektu konstruktora.</span><span class="sxs-lookup"><span data-stu-id="bb019-382">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="bb019-383">`Build` Wywołania `Run` i metody na obiekcie konstruktora.</span><span class="sxs-lookup"><span data-stu-id="bb019-383">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="bb019-384">Oto *Program.cs* kod dla obciążenia innego niż HTTP, `IHostedService` z pojedynczą implementacją dodaną do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="bb019-384">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="bb019-385">W przypadku obciążenia `Main` HTTP metoda jest `CreateHostBuilder` `ConfigureWebHostDefaults`taka sama, ale wywołuje:</span><span class="sxs-lookup"><span data-stu-id="bb019-385">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="bb019-386">Jeśli aplikacja używa entity framework core, nie należy zmieniać `CreateHostBuilder` nazwę lub podpis metody.</span><span class="sxs-lookup"><span data-stu-id="bb019-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="bb019-387">[Narzędzia Entity Framework Core](/ef/core/miscellaneous/cli/) oczekują, aby znaleźć `CreateHostBuilder` metodę, która konfiguruje hosta bez uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="bb019-388">Aby uzyskać więcej informacji, zobacz [Tworzenie dbcontext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="bb019-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="bb019-389">Domyślne ustawienia konstruktora</span><span class="sxs-lookup"><span data-stu-id="bb019-389">Default builder settings</span></span>

<span data-ttu-id="bb019-390">Metoda: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*></span><span class="sxs-lookup"><span data-stu-id="bb019-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="bb019-391">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na <xref:System.IO.Directory.GetCurrentDirectory*>ścieżkę zwróconą przez program .</span><span class="sxs-lookup"><span data-stu-id="bb019-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="bb019-392">Ładuje konfigurację hosta z:</span><span class="sxs-lookup"><span data-stu-id="bb019-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="bb019-393">Zmienne środowiskowe poprzedzone . `DOTNET_`</span><span class="sxs-lookup"><span data-stu-id="bb019-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="bb019-394">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="bb019-394">Command-line arguments.</span></span>
* <span data-ttu-id="bb019-395">Ładuje konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="bb019-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="bb019-396">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="bb019-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="bb019-397">*appsettings. {Środowisko}.json*.</span><span class="sxs-lookup"><span data-stu-id="bb019-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="bb019-398">[Tajny menedżer,](xref:security/app-secrets) gdy aplikacja `Development` działa w środowisku.</span><span class="sxs-lookup"><span data-stu-id="bb019-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="bb019-399">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="bb019-399">Environment variables.</span></span>
  * <span data-ttu-id="bb019-400">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="bb019-400">Command-line arguments.</span></span>
* <span data-ttu-id="bb019-401">Dodaje następujących dostawców [rejestrowania:](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="bb019-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="bb019-402">Konsola</span><span class="sxs-lookup"><span data-stu-id="bb019-402">Console</span></span>
  * <span data-ttu-id="bb019-403">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="bb019-403">Debug</span></span>
  * <span data-ttu-id="bb019-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="bb019-404">EventSource</span></span>
  * <span data-ttu-id="bb019-405">EventLog (tylko w przypadku pracy w systemie Windows)</span><span class="sxs-lookup"><span data-stu-id="bb019-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="bb019-406">Włącza [sprawdzanie poprawności zakresu](xref:fundamentals/dependency-injection#scope-validation) i sprawdzania poprawności [zależności,](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) gdy środowisko jest rozwój.</span><span class="sxs-lookup"><span data-stu-id="bb019-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="bb019-407">Metoda: `ConfigureWebHostDefaults`</span><span class="sxs-lookup"><span data-stu-id="bb019-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="bb019-408">Ładuje konfigurację hosta ze zmiennych środowiskowych poprzedzonych programem `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="bb019-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="bb019-409">Ustawia serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci web i konfiguruje go przy użyciu dostawców konfiguracji hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="bb019-410">Aby zapoznać się z domyślnymi <xref:fundamentals/servers/kestrel#kestrel-options>opcjami serwera Kestrel, zobacz .</span><span class="sxs-lookup"><span data-stu-id="bb019-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="bb019-411">Dodaje [oprogramowanie pośredniczące filtrowania hosta](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="bb019-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="bb019-412">Dodaje [oprogramowanie pośredniczące nagłówków przesyłanych dalej,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest `true`równe .</span><span class="sxs-lookup"><span data-stu-id="bb019-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="bb019-413">Umożliwia integrację z usługiami IIS.</span><span class="sxs-lookup"><span data-stu-id="bb019-413">Enables IIS integration.</span></span> <span data-ttu-id="bb019-414">Aby zapoznać się z <xref:host-and-deploy/iis/index#iis-options>domyślnymi opcjami iis, zobacz .</span><span class="sxs-lookup"><span data-stu-id="bb019-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="bb019-415">W sekcji [Ustawienia dla wszystkich typów aplikacji](#settings-for-all-app-types) i Ustawienia aplikacji sieci [Web](#settings-for-web-apps) w dalszej części tego artykułu pokazano, jak zastąpić domyślne ustawienia konstruktora.</span><span class="sxs-lookup"><span data-stu-id="bb019-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="bb019-416">Usługi świadczone w ramach ram</span><span class="sxs-lookup"><span data-stu-id="bb019-416">Framework-provided services</span></span>

<span data-ttu-id="bb019-417">Następujące usługi są rejestrowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="bb019-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="bb019-418">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="bb019-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="bb019-419">IHostLifetime (czas pracy)</span><span class="sxs-lookup"><span data-stu-id="bb019-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="bb019-420">IHostEnvironment / IWebHostŚrodowicielenie</span><span class="sxs-lookup"><span data-stu-id="bb019-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="bb019-421">Aby uzyskać więcej informacji na <xref:fundamentals/dependency-injection#framework-provided-services>temat usług świadczonych w ramach ram, zobacz .</span><span class="sxs-lookup"><span data-stu-id="bb019-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="bb019-422">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="bb019-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="bb019-423"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> Wstrzyknąć `IApplicationLifetime`(dawniej) usługi do dowolnej klasy do obsługi zadań po starcie i wdzięku zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="bb019-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="bb019-424">Trzy właściwości w interfejsie są tokeny anulowania używane do rejestrowania uruchamiania aplikacji i metody obsługi zdarzeń zatrzymania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="bb019-425">Interfejs zawiera również `StopApplication` metodę.</span><span class="sxs-lookup"><span data-stu-id="bb019-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="bb019-426">Poniższy przykład `IHostedService` jest implementacją, która rejestruje `IHostApplicationLifetime` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="bb019-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="bb019-427">IHostLifetime (czas pracy)</span><span class="sxs-lookup"><span data-stu-id="bb019-427">IHostLifetime</span></span>

<span data-ttu-id="bb019-428">Implementacja <xref:Microsoft.Extensions.Hosting.IHostLifetime> kontroluje, kiedy host uruchamia się i kiedy zatrzymuje.</span><span class="sxs-lookup"><span data-stu-id="bb019-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="bb019-429">Używana jest ostatnia zarejestrowana implementacja.</span><span class="sxs-lookup"><span data-stu-id="bb019-429">The last implementation registered is used.</span></span>

<span data-ttu-id="bb019-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`jest domyślną `IHostLifetime` implementacją.</span><span class="sxs-lookup"><span data-stu-id="bb019-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="bb019-431">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="bb019-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="bb019-432">Nasłuchuje <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM i wywołuje, <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="bb019-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="bb019-433">Odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="bb019-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="bb019-434">IHostŚrodowienie</span><span class="sxs-lookup"><span data-stu-id="bb019-434">IHostEnvironment</span></span>

<span data-ttu-id="bb019-435">Wstrzyknąć <xref:Microsoft.Extensions.Hosting.IHostEnvironment> usługę do klasy, aby uzyskać informacje o następujących ustawieniach:</span><span class="sxs-lookup"><span data-stu-id="bb019-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="bb019-436">Applicationname</span><span class="sxs-lookup"><span data-stu-id="bb019-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="bb019-437">Nazwa środowiska</span><span class="sxs-lookup"><span data-stu-id="bb019-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="bb019-438">Ścieżka contentrootpath</span><span class="sxs-lookup"><span data-stu-id="bb019-438">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="bb019-439">Aplikacje sieci `IWebHostEnvironment` Web implementują `IHostEnvironment` interfejs, który dziedziczy i dodaje [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="bb019-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="bb019-440">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="bb019-440">Host configuration</span></span>

<span data-ttu-id="bb019-441">Konfiguracja hosta jest używana <xref:Microsoft.Extensions.Hosting.IHostEnvironment> dla właściwości implementacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="bb019-442">Konfiguracja hosta jest dostępna w <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>pliku [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) wewnątrz .</span><span class="sxs-lookup"><span data-stu-id="bb019-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="bb019-443">Po `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , jest zastępowany config aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="bb019-444">Aby dodać konfigurację <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`hosta, zadzwoń .</span><span class="sxs-lookup"><span data-stu-id="bb019-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="bb019-445">`ConfigureHostConfiguration`można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="bb019-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="bb019-446">Host używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="bb019-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="bb019-447">Dostawca zmiennych środowiskowych `DOTNET_` z prefiksem i `CreateDefaultBuilder`argumentami wiersza polecenia są uwzględniane przez program .</span><span class="sxs-lookup"><span data-stu-id="bb019-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="bb019-448">W przypadku aplikacji sieci web dodawany jest dostawca zmiennych środowiskowych z prefiksem. `ASPNETCORE_`</span><span class="sxs-lookup"><span data-stu-id="bb019-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="bb019-449">Prefiks jest usuwany podczas odczytu zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="bb019-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="bb019-450">Na przykład wartość zmiennej `ASPNETCORE_ENVIRONMENT` środowiskowej dla staje `environment` się wartością konfiguracji hosta dla klucza.</span><span class="sxs-lookup"><span data-stu-id="bb019-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="bb019-451">W poniższym przykładzie tworzy konfigurację hosta:</span><span class="sxs-lookup"><span data-stu-id="bb019-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="bb019-452">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="bb019-452">App configuration</span></span>

<span data-ttu-id="bb019-453">Konfiguracja aplikacji jest <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> `IHostBuilder`tworzona przez wywołanie .</span><span class="sxs-lookup"><span data-stu-id="bb019-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="bb019-454">`ConfigureAppConfiguration`można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="bb019-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="bb019-455">Aplikacja używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="bb019-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="bb019-456">Konfiguracja utworzona `ConfigureAppConfiguration` przez jest dostępna w [witrynie HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) dla kolejnych operacji i jako usługa z DI.</span><span class="sxs-lookup"><span data-stu-id="bb019-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="bb019-457">Konfiguracja hosta jest również dodawana do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="bb019-458">Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="bb019-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="bb019-459">Ustawienia dla wszystkich typów aplikacji</span><span class="sxs-lookup"><span data-stu-id="bb019-459">Settings for all app types</span></span>

<span data-ttu-id="bb019-460">W tej sekcji wymieniono ustawienia hosta, które dotyczą zarówno obciążeń HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb019-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="bb019-461">Domyślnie zmienne środowiskowe używane do konfigurowania `DOTNET_` `ASPNETCORE_` tych ustawień mogą mieć lub prefiks.</span><span class="sxs-lookup"><span data-stu-id="bb019-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="bb019-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="bb019-462">ApplicationName</span></span>

<span data-ttu-id="bb019-463">[Właściwość IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) jest ustawiana z konfiguracji hosta podczas konstruowaniu hosta.</span><span class="sxs-lookup"><span data-stu-id="bb019-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="bb019-464">**Klucz:**`applicationName`</span><span class="sxs-lookup"><span data-stu-id="bb019-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="bb019-465">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-465">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-466">**Domyślnie**: Nazwa zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="bb019-467">**Zmienna środowiskowa:**`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="bb019-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="bb019-468">Aby ustawić tę wartość, należy użyć zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="bb019-468">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="bb019-469">Ścieżka contentrootpath</span><span class="sxs-lookup"><span data-stu-id="bb019-469">ContentRootPath</span></span>

<span data-ttu-id="bb019-470">[Właściwość IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="bb019-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="bb019-471">Jeśli ścieżka nie istnieje, host nie uruchamia się.</span><span class="sxs-lookup"><span data-stu-id="bb019-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="bb019-472">**Klucz:**`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="bb019-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="bb019-473">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-473">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-474">**Domyślnie**: Folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="bb019-475">**Zmienna środowiskowa:**`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="bb019-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="bb019-476">Aby ustawić tę wartość, należy `UseContentRoot` użyć `IHostBuilder`zmiennej środowiskowej lub wywołać na:</span><span class="sxs-lookup"><span data-stu-id="bb019-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="bb019-477">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="bb019-477">For more information, see:</span></span>

* [<span data-ttu-id="bb019-478">Podstawy: Katalog główny treści</span><span class="sxs-lookup"><span data-stu-id="bb019-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="bb019-479">Webroot</span><span class="sxs-lookup"><span data-stu-id="bb019-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="bb019-480">Nazwa środowiska</span><span class="sxs-lookup"><span data-stu-id="bb019-480">EnvironmentName</span></span>

<span data-ttu-id="bb019-481">[Właściwość IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) można ustawić na dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="bb019-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="bb019-482">Wartości zdefiniowane `Development`przez `Staging`ramy `Production`obejmują , i .</span><span class="sxs-lookup"><span data-stu-id="bb019-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="bb019-483">W wartościach nie rozróżnia się wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="bb019-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="bb019-484">**Klucz:**`environment`</span><span class="sxs-lookup"><span data-stu-id="bb019-484">**Key**: `environment`</span></span>  
<span data-ttu-id="bb019-485">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-485">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-486">**Domyślnie**:`Production`</span><span class="sxs-lookup"><span data-stu-id="bb019-486">**Default**: `Production`</span></span>  
<span data-ttu-id="bb019-487">**Zmienna środowiskowa:**`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="bb019-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="bb019-488">Aby ustawić tę wartość, należy `UseEnvironment` użyć `IHostBuilder`zmiennej środowiskowej lub wywołać na:</span><span class="sxs-lookup"><span data-stu-id="bb019-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="bb019-489">Shutdowntimeout</span><span class="sxs-lookup"><span data-stu-id="bb019-489">ShutdownTimeout</span></span>

<span data-ttu-id="bb019-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) ustawia limit <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>czasu dla .</span><span class="sxs-lookup"><span data-stu-id="bb019-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="bb019-491">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="bb019-491">The default value is five seconds.</span></span>  <span data-ttu-id="bb019-492">W okresie limitu czasu host:</span><span class="sxs-lookup"><span data-stu-id="bb019-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="bb019-493">Wyzwala [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="bb019-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="bb019-494">Próbuje zatrzymać hostowane usługi, rejestrowanie błędów dla usług, które nie mogą się zatrzymać.</span><span class="sxs-lookup"><span data-stu-id="bb019-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="bb019-495">Jeśli limit czasu wygaśnie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="bb019-496">Usługi zatrzymują się nawet wtedy, gdy nie zakończyły przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="bb019-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="bb019-497">Jeśli usługi wymagają dodatkowego czasu, aby zatrzymać, zwiększyć limit czasu.</span><span class="sxs-lookup"><span data-stu-id="bb019-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="bb019-498">**Klucz:**`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="bb019-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="bb019-499">**Typ**:`int`</span><span class="sxs-lookup"><span data-stu-id="bb019-499">**Type**: `int`</span></span>  
<span data-ttu-id="bb019-500">**Domyślnie**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="bb019-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="bb019-501">**Zmienna środowiskowa:**`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="bb019-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="bb019-502">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub skonfiguruj `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="bb019-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="bb019-503">W poniższym przykładzie ustawia limit czasu na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="bb019-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="bb019-504">Ustawienia aplikacji internetowych</span><span class="sxs-lookup"><span data-stu-id="bb019-504">Settings for web apps</span></span>

<span data-ttu-id="bb019-505">Niektóre ustawienia hosta dotyczą tylko obciążeń HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb019-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="bb019-506">Domyślnie zmienne środowiskowe używane do konfigurowania `DOTNET_` `ASPNETCORE_` tych ustawień mogą mieć lub prefiks.</span><span class="sxs-lookup"><span data-stu-id="bb019-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="bb019-507">Dostępne `IWebHostBuilder` są metody rozszerzenia dla tych ustawień.</span><span class="sxs-lookup"><span data-stu-id="bb019-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="bb019-508">Przykłady kodu, które pokazują, jak `webBuilder` wywołać `IWebHostBuilder`metody rozszerzenia zakłada, że jest wystąpieniem , jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="bb019-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="bb019-509">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="bb019-509">CaptureStartupErrors</span></span>

<span data-ttu-id="bb019-510">Kiedy `false`, błędy podczas uruchamiania spowodować wyjście hosta.</span><span class="sxs-lookup"><span data-stu-id="bb019-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="bb019-511">Gdy `true`host przechwytuje wyjątki podczas uruchamiania i próbuje uruchomić serwer.</span><span class="sxs-lookup"><span data-stu-id="bb019-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="bb019-512">**Klucz:**`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="bb019-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="bb019-513">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="bb019-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="bb019-514">**Domyślnie:** `false` Domyślnie, chyba że aplikacja działa z Kestrel `true`za IIS, gdzie domyślnie jest .</span><span class="sxs-lookup"><span data-stu-id="bb019-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="bb019-515">**Zmienna środowiskowa:**`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="bb019-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="bb019-516">Aby ustawić tę wartość, `CaptureStartupErrors`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="bb019-517">Szczegółoweerrory</span><span class="sxs-lookup"><span data-stu-id="bb019-517">DetailedErrors</span></span>

<span data-ttu-id="bb019-518">Po włączeniu lub gdy `Development`środowisko jest , aplikacja przechwytuje szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="bb019-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="bb019-519">**Klucz:**`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="bb019-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="bb019-520">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="bb019-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="bb019-521">**Domyślnie**:`false`</span><span class="sxs-lookup"><span data-stu-id="bb019-521">**Default**: `false`</span></span>  
<span data-ttu-id="bb019-522">**Zmienna środowiskowa:**`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="bb019-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="bb019-523">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="bb019-524">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="bb019-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="bb019-525">Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do załadowania podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="bb019-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="bb019-526">Mimo że wartość konfiguracji domyślnie pusty ciąg, zestawy uruchamiania hostingu zawsze zawierają zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="bb019-527">Po udostępnieniu zestawów uruchamiania hostingu są one dodawane do zestawu aplikacji do ładowania, gdy aplikacja tworzy swoje typowe usługi podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="bb019-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="bb019-528">**Klucz:**`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="bb019-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="bb019-529">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-529">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-530">**Domyślnie**: Pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="bb019-530">**Default**: Empty string</span></span>  
<span data-ttu-id="bb019-531">**Zmienna środowiskowa:**`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="bb019-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="bb019-532">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="bb019-533">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="bb019-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="bb019-534">Ciąg rozdzielany średnikami zestawów uruchamiania hostingu do wykluczenia podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="bb019-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="bb019-535">**Klucz:**`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="bb019-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="bb019-536">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-536">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-537">**Domyślnie**: Pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="bb019-537">**Default**: Empty string</span></span>  
<span data-ttu-id="bb019-538">**Zmienna środowiskowa:**`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="bb019-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="bb019-539">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="bb019-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="bb019-540">HTTPS_Port</span></span>

<span data-ttu-id="bb019-541">Port przekierowania HTTPS.</span><span class="sxs-lookup"><span data-stu-id="bb019-541">The HTTPS redirect port.</span></span> <span data-ttu-id="bb019-542">Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="bb019-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="bb019-543">**Klucz:**`https_port`</span><span class="sxs-lookup"><span data-stu-id="bb019-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="bb019-544">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-544">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-545">**Domyślnie:** Wartość domyślna nie jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="bb019-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="bb019-546">**Zmienna środowiskowa:**`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="bb019-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="bb019-547">Aby ustawić tę wartość, `UseSetting`użyj konfiguracji lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="bb019-548">PreferHostingUrls (PreferHostingUrls)</span><span class="sxs-lookup"><span data-stu-id="bb019-548">PreferHostingUrls</span></span>

<span data-ttu-id="bb019-549">Wskazuje, czy host powinien nasłuchiwania adresów URL skonfigurowanych przy 200 000 `IWebHostBuilder` adresów URL skonfigurowanych z `IServer` implementacją.</span><span class="sxs-lookup"><span data-stu-id="bb019-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="bb019-550">**Klucz:**`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="bb019-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="bb019-551">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="bb019-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="bb019-552">**Domyślnie**:`true`</span><span class="sxs-lookup"><span data-stu-id="bb019-552">**Default**: `true`</span></span>  
<span data-ttu-id="bb019-553">**Zmienna środowiskowa:**`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="bb019-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="bb019-554">Aby ustawić tę wartość, użyj `PreferHostingUrls`zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="bb019-555">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="bb019-555">PreventHostingStartup</span></span>

<span data-ttu-id="bb019-556">Zapobiega automatycznemu obciążaniu zestawów uruchamiania hostingu, w tym zestawów uruchamiania hostingu skonfigurowanych przez zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="bb019-557">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="bb019-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="bb019-558">**Klucz:**`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="bb019-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="bb019-559">**Typ** `bool` :`true` `1`( lub )</span><span class="sxs-lookup"><span data-stu-id="bb019-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="bb019-560">**Domyślnie**:`false`</span><span class="sxs-lookup"><span data-stu-id="bb019-560">**Default**: `false`</span></span>  
<span data-ttu-id="bb019-561">**Zmienna środowiskowa:**`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="bb019-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="bb019-562">Aby ustawić tę wartość, użyj `UseSetting` zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="bb019-563">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="bb019-563">StartupAssembly</span></span>

<span data-ttu-id="bb019-564">Zestaw do wyszukiwania `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="bb019-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="bb019-565">**Klucz:**`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="bb019-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="bb019-566">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-566">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-567">**Domyślnie**: Zespół aplikacji</span><span class="sxs-lookup"><span data-stu-id="bb019-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="bb019-568">**Zmienna środowiskowa:**`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="bb019-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="bb019-569">Aby ustawić tę wartość, należy `UseStartup`użyć zmiennej środowiskowej lub wywołania .</span><span class="sxs-lookup"><span data-stu-id="bb019-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="bb019-570">`UseStartup`może przyjąć nazwę`string`zestawu ( )`TStartup`lub typ ( ).</span><span class="sxs-lookup"><span data-stu-id="bb019-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="bb019-571">Jeśli `UseStartup` wywoływanych jest wiele metod, pierwszeństwo ma ostatnia.</span><span class="sxs-lookup"><span data-stu-id="bb019-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="bb019-572">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="bb019-572">URLs</span></span>

<span data-ttu-id="bb019-573">Lista adresów IP lub adresów hostów rozdzielanych średnikami z portami i protokołami, na których serwer powinien nasłuchiwał żądań.</span><span class="sxs-lookup"><span data-stu-id="bb019-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="bb019-574">Na przykład `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="bb019-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="bb019-575">Użyj\*" ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwach hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="bb019-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="bb019-576">Protokół (`http://` `https://`lub ) musi być dołączony do każdego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="bb019-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="bb019-577">Obsługiwane formaty różnią się w zależności od serwera.</span><span class="sxs-lookup"><span data-stu-id="bb019-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="bb019-578">**Klucz:**`urls`</span><span class="sxs-lookup"><span data-stu-id="bb019-578">**Key**: `urls`</span></span>  
<span data-ttu-id="bb019-579">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-579">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-580">**Domyślnie**: `http://localhost:5000` i`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="bb019-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="bb019-581">**Zmienna środowiskowa:**`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="bb019-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="bb019-582">Aby ustawić tę wartość, użyj `UseUrls`zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="bb019-583">Pustułka ma własny interfejs API konfiguracji punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="bb019-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="bb019-584">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="bb019-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="bb019-585">Webroot</span><span class="sxs-lookup"><span data-stu-id="bb019-585">WebRoot</span></span>

<span data-ttu-id="bb019-586">Względna ścieżka do zasobów statycznych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-586">The relative path to the app's static assets.</span></span>

<span data-ttu-id="bb019-587">**Klucz:**`webroot`</span><span class="sxs-lookup"><span data-stu-id="bb019-587">**Key**: `webroot`</span></span>  
<span data-ttu-id="bb019-588">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-588">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-589">**Domyślnie**: `wwwroot`Wartość domyślna to .</span><span class="sxs-lookup"><span data-stu-id="bb019-589">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="bb019-590">Musi istnieć ścieżka do *{content root}/wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="bb019-590">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="bb019-591">Jeśli ścieżka nie istnieje, używany jest dostawca plików bez operacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-591">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="bb019-592">**Zmienna środowiskowa:**`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="bb019-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="bb019-593">Aby ustawić tę wartość, użyj `UseWebRoot`zmiennej środowiskowej lub wywołania:</span><span class="sxs-lookup"><span data-stu-id="bb019-593">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="bb019-594">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="bb019-594">For more information, see:</span></span>

* [<span data-ttu-id="bb019-595">Podstawy: Korzeń sieci Web</span><span class="sxs-lookup"><span data-stu-id="bb019-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="bb019-596">Ścieżka contentrootpath</span><span class="sxs-lookup"><span data-stu-id="bb019-596">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="bb019-597">Zarządzanie okresem istnienia hosta</span><span class="sxs-lookup"><span data-stu-id="bb019-597">Manage the host lifetime</span></span>

<span data-ttu-id="bb019-598">Wywołaj metody <xref:Microsoft.Extensions.Hosting.IHost> na wbudowanej implementacji, aby uruchomić i zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="bb019-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="bb019-599">Te metody <xref:Microsoft.Extensions.Hosting.IHostedService> mają wpływ na wszystkie implementacje, które są zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="bb019-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="bb019-600">Run</span><span class="sxs-lookup"><span data-stu-id="bb019-600">Run</span></span>

<span data-ttu-id="bb019-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący, dopóki host nie zostanie zamknięty.</span><span class="sxs-lookup"><span data-stu-id="bb019-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="bb019-602">RunAsync ( RunAsync )</span><span class="sxs-lookup"><span data-stu-id="bb019-602">RunAsync</span></span>

<span data-ttu-id="bb019-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia systemu.</span><span class="sxs-lookup"><span data-stu-id="bb019-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="bb019-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="bb019-604">RunConsoleAsync</span></span>

<span data-ttu-id="bb019-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>umożliwia obsługę konsoli, buduje i uruchamia hosta i czeka na <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM, aby zamknąć.</span><span class="sxs-lookup"><span data-stu-id="bb019-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="bb019-606">Uruchamianie</span><span class="sxs-lookup"><span data-stu-id="bb019-606">Start</span></span>

<span data-ttu-id="bb019-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="bb019-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="bb019-608">StartAsync (StartAsync)</span><span class="sxs-lookup"><span data-stu-id="bb019-608">StartAsync</span></span>

<span data-ttu-id="bb019-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia hosta i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="bb019-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="bb019-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na `StartAsync`początku , który czeka, aż zakończy się przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="bb019-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="bb019-611">Może to służyć do opóźnienia uruchamiania, dopóki nie zostanie zasygnaligozowana przez zdarzenie zewnętrzne.</span><span class="sxs-lookup"><span data-stu-id="bb019-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="bb019-612">StopAsync (StopAsync)</span><span class="sxs-lookup"><span data-stu-id="bb019-612">StopAsync</span></span>

<span data-ttu-id="bb019-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w podanym limit czasu.</span><span class="sxs-lookup"><span data-stu-id="bb019-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="bb019-614">WaitForShutdown (WaitForShutdown)</span><span class="sxs-lookup"><span data-stu-id="bb019-614">WaitForShutdown</span></span>

<span data-ttu-id="bb019-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje wątek wywołujący, dopóki zamknięcie zostanie wywołane przez IHostLifetime, na przykład przez <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="bb019-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="bb019-616">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="bb019-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="bb019-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task> zwraca, który kończy się po zamknięciu jest <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>wyzwalany za pośrednictwem danego tokenu i wywołania .</span><span class="sxs-lookup"><span data-stu-id="bb019-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="bb019-618">Sterowanie zewnętrzne</span><span class="sxs-lookup"><span data-stu-id="bb019-618">External control</span></span>

<span data-ttu-id="bb019-619">Bezpośrednią kontrolę okresu istnienia hosta można osiągnąć za pomocą metod, które można wywołać zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="bb019-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="bb019-620">ASP.NET aplikacje Core konfigurują i uruchamiają hosta.</span><span class="sxs-lookup"><span data-stu-id="bb019-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="bb019-621">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="bb019-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="bb019-622">W tym artykule omówiono ASP.NET<xref:Microsoft.Extensions.Hosting.HostBuilder>Core Generic Host ( ), który jest używany dla aplikacji, które nie przetwarzają żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb019-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="bb019-623">Celem hosta ogólnego jest oddzielenie potoku HTTP od interfejsu API hosta sieci Web, aby włączyć szerszą gamę scenariuszy hosta.</span><span class="sxs-lookup"><span data-stu-id="bb019-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="bb019-624">Wiadomości, zadania w tle i inne obciążenia inne niż HTTP oparte na generic hosta korzystają z możliwości przekrojowych, takich jak konfiguracja, iniekcja zależności (DI) i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="bb019-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="bb019-625">Generic Host jest nowy w ASP.NET Core 2.1 i nie nadaje się do scenariuszy hostingu.</span><span class="sxs-lookup"><span data-stu-id="bb019-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="bb019-626">W przypadku scenariuszy hostingu sieci Web należy użyć [hosta sieci Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="bb019-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="bb019-627">Generic Host zastąpi hosta sieci Web w przyszłej wersji i będzie działać jako podstawowy interfejs API hosta zarówno w scenariuszach HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="bb019-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="bb019-628">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bb019-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="bb019-629">Podczas uruchamiania przykładowej aplikacji w [programie Visual Studio Code](https://code.visualstudio.com/)należy użyć zewnętrznego lub *zintegrowanego terminala*.</span><span class="sxs-lookup"><span data-stu-id="bb019-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="bb019-630">Nie uruchamiaj próbki w `internalConsole`pliku .</span><span class="sxs-lookup"><span data-stu-id="bb019-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="bb019-631">Aby ustawić konsolę w programie Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="bb019-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="bb019-632">Otwórz plik *vscode/launch.json.*</span><span class="sxs-lookup"><span data-stu-id="bb019-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="bb019-633">W konfiguracji **.NET Core Launch (konsola)** znajdź wpis **konsoli.**</span><span class="sxs-lookup"><span data-stu-id="bb019-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="bb019-634">Ustaw wartość na `externalTerminal` jedną `integratedTerminal`lub .</span><span class="sxs-lookup"><span data-stu-id="bb019-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="bb019-635">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="bb019-635">Introduction</span></span>

<span data-ttu-id="bb019-636">Biblioteka hosta ogólnego <xref:Microsoft.Extensions.Hosting> jest dostępna w obszarze nazw i jest dostarczana przez pakiet [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/)</span><span class="sxs-lookup"><span data-stu-id="bb019-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="bb019-637">Pakiet `Microsoft.Extensions.Hosting` jest zawarty w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 lub nowszym).</span><span class="sxs-lookup"><span data-stu-id="bb019-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="bb019-638"><xref:Microsoft.Extensions.Hosting.IHostedService>jest punktem wejścia do wykonania kodu.</span><span class="sxs-lookup"><span data-stu-id="bb019-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="bb019-639">Każda <xref:Microsoft.Extensions.Hosting.IHostedService> implementacja jest wykonywana w kolejności [rejestracji usługi w ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="bb019-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="bb019-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>jest wywoływana <xref:Microsoft.Extensions.Hosting.IHostedService> na każdym po <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> uruchomieniu hosta i jest wywoływana w odwrotnej kolejności rejestracji, gdy host zamyka się bezpiecznie.</span><span class="sxs-lookup"><span data-stu-id="bb019-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="bb019-641">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="bb019-641">Set up a host</span></span>

<span data-ttu-id="bb019-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder>jest głównym składnikiem używanym przez biblioteki i aplikacje do inicjowania, tworzenia i uruchamiania hosta:</span><span class="sxs-lookup"><span data-stu-id="bb019-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="bb019-643">Opcje</span><span class="sxs-lookup"><span data-stu-id="bb019-643">Options</span></span>

<span data-ttu-id="bb019-644"><xref:Microsoft.Extensions.Hosting.HostOptions>skonfigurować opcje <xref:Microsoft.Extensions.Hosting.IHost>dla pliku .</span><span class="sxs-lookup"><span data-stu-id="bb019-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="bb019-645">Limit czasu zamknięcia</span><span class="sxs-lookup"><span data-stu-id="bb019-645">Shutdown timeout</span></span>

<span data-ttu-id="bb019-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>ustawia limit czasu <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>dla .</span><span class="sxs-lookup"><span data-stu-id="bb019-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="bb019-647">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="bb019-647">The default value is five seconds.</span></span>

<span data-ttu-id="bb019-648">Następująca konfiguracja `Program.Main` opcji zwiększa domyślny pięciosekundowy limit czasu zamknięcia do 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="bb019-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="bb019-649">Usługi domyślne</span><span class="sxs-lookup"><span data-stu-id="bb019-649">Default services</span></span>

<span data-ttu-id="bb019-650">Podczas inicjowania hosta rejestrowane są następujące usługi:</span><span class="sxs-lookup"><span data-stu-id="bb019-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="bb019-651">[Środowisko](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>( )</span><span class="sxs-lookup"><span data-stu-id="bb019-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="bb019-652">[Konfiguracja](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="bb019-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="bb019-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="bb019-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="bb019-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="bb019-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="bb019-655">[Opcje](xref:fundamentals/configuration/options) <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>( )</span><span class="sxs-lookup"><span data-stu-id="bb019-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="bb019-656">[Rejestrowanie](xref:fundamentals/logging/index) <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>( )</span><span class="sxs-lookup"><span data-stu-id="bb019-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="bb019-657">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="bb019-657">Host configuration</span></span>

<span data-ttu-id="bb019-658">Konfiguracja hosta jest tworzona przez:</span><span class="sxs-lookup"><span data-stu-id="bb019-658">Host configuration is created by:</span></span>

* <span data-ttu-id="bb019-659">Wywoływanie metod <xref:Microsoft.Extensions.Hosting.IHostBuilder> rozszerzenia w celu ustawienia [katalogu głównego](#content-root) zawartości i [środowiska](#environment).</span><span class="sxs-lookup"><span data-stu-id="bb019-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="bb019-660">Odczytywanie konfiguracji <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>od dostawców konfiguracji w programie .</span><span class="sxs-lookup"><span data-stu-id="bb019-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="bb019-661">Metody rozszerzeń</span><span class="sxs-lookup"><span data-stu-id="bb019-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="bb019-662">Klucz aplikacji (nazwa)</span><span class="sxs-lookup"><span data-stu-id="bb019-662">Application key (name)</span></span>

<span data-ttu-id="bb019-663">[Właściwość IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) jest ustawiona z konfiguracji hosta podczas konstruowania hosta.</span><span class="sxs-lookup"><span data-stu-id="bb019-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="bb019-664">Aby jawnie ustawić wartość, użyj [hostDefaults.ApplicationKey:](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)</span><span class="sxs-lookup"><span data-stu-id="bb019-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="bb019-665">**Klucz:**`applicationName`</span><span class="sxs-lookup"><span data-stu-id="bb019-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="bb019-666">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-666">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-667">**Domyślnie**: Nazwa zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="bb019-668">**Zestaw za pomocą**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="bb019-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="bb019-669">**Zmienna środowiskowa**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` jest [opcjonalna i zdefiniowana przez użytkownika)](#configurehostconfiguration)</span><span class="sxs-lookup"><span data-stu-id="bb019-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="bb019-670">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="bb019-670">Content root</span></span>

<span data-ttu-id="bb019-671">To ustawienie określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="bb019-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="bb019-672">**Klucz:**`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="bb019-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="bb019-673">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-673">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-674">**Domyślnie**: Domyślnie folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="bb019-675">**Zestaw za pomocą**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="bb019-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="bb019-676">**Zmienna środowiskowa**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` jest [opcjonalna i zdefiniowana przez użytkownika)](#configurehostconfiguration)</span><span class="sxs-lookup"><span data-stu-id="bb019-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="bb019-677">Jeśli ścieżka nie istnieje, host nie uruchamia się.</span><span class="sxs-lookup"><span data-stu-id="bb019-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="bb019-678">Aby uzyskać więcej informacji, zobacz [Podstawy: Katalog główny zawartości](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="bb019-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="bb019-679">Środowisko</span><span class="sxs-lookup"><span data-stu-id="bb019-679">Environment</span></span>

<span data-ttu-id="bb019-680">Ustawia [środowisko](xref:fundamentals/environments)aplikacji .</span><span class="sxs-lookup"><span data-stu-id="bb019-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="bb019-681">**Klucz:**`environment`</span><span class="sxs-lookup"><span data-stu-id="bb019-681">**Key**: `environment`</span></span>  
<span data-ttu-id="bb019-682">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="bb019-682">**Type**: `string`</span></span>  
<span data-ttu-id="bb019-683">**Domyślnie**:`Production`</span><span class="sxs-lookup"><span data-stu-id="bb019-683">**Default**: `Production`</span></span>  
<span data-ttu-id="bb019-684">**Zestaw za pomocą**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="bb019-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="bb019-685">**Zmienna środowiskowa**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` jest [opcjonalna i zdefiniowana przez użytkownika)](#configurehostconfiguration)</span><span class="sxs-lookup"><span data-stu-id="bb019-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="bb019-686">Środowisko można ustawić na dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="bb019-686">The environment can be set to any value.</span></span> <span data-ttu-id="bb019-687">Wartości zdefiniowane `Development`przez `Staging`ramy `Production`obejmują , i .</span><span class="sxs-lookup"><span data-stu-id="bb019-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="bb019-688">W wartościach nie rozróżnia się wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="bb019-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="bb019-689">Konfigurowanie konfiguracji hostowania</span><span class="sxs-lookup"><span data-stu-id="bb019-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="bb019-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>używa do <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> utworzenia <xref:Microsoft.Extensions.Configuration.IConfiguration> dla hosta.</span><span class="sxs-lookup"><span data-stu-id="bb019-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="bb019-691">Konfiguracja hosta służy do <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> inicjowania do użycia w procesie kompilacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="bb019-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="bb019-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="bb019-693">Host używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="bb019-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="bb019-694">Domyślnie nie są uwzględniane żadne dostawcy.</span><span class="sxs-lookup"><span data-stu-id="bb019-694">No providers are included by default.</span></span> <span data-ttu-id="bb019-695">Należy jawnie określić dostawców konfiguracji, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>których aplikacja wymaga w , w tym:</span><span class="sxs-lookup"><span data-stu-id="bb019-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="bb019-696">Konfiguracja pliku (na przykład z pliku *hostsettings.json).*</span><span class="sxs-lookup"><span data-stu-id="bb019-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="bb019-697">Konfiguracja zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="bb019-697">Environment variable configuration.</span></span>
* <span data-ttu-id="bb019-698">Konfiguracja argumentu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="bb019-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="bb019-699">Wszelkie inne wymagane dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="bb019-699">Any other required configuration providers.</span></span>

<span data-ttu-id="bb019-700">Konfiguracja pliku hosta jest włączona, określając ścieżkę `SetBasePath` podstawową aplikacji, po której następuje wywołanie jednego z [dostawców konfiguracji plików.](xref:fundamentals/configuration/index#file-configuration-provider)</span><span class="sxs-lookup"><span data-stu-id="bb019-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="bb019-701">Przykładowa aplikacja używa pliku JSON, *hostsettings.json*i wywołania, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> aby korzystać z ustawień konfiguracji hosta pliku.</span><span class="sxs-lookup"><span data-stu-id="bb019-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="bb019-702">Aby dodać [konfigurację zmiennej środowiskowej](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hosta, należy wywołać <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> konstruktora hosta.</span><span class="sxs-lookup"><span data-stu-id="bb019-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="bb019-703">`AddEnvironmentVariables`akceptuje opcjonalny prefiks zdefiniowany przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bb019-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="bb019-704">Przykładowa aplikacja używa prefiksu `PREFIX_`programu .</span><span class="sxs-lookup"><span data-stu-id="bb019-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="bb019-705">Prefiks jest usuwany podczas odczytu zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="bb019-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="bb019-706">Po skonfigurowaniu hosta przykładowej aplikacji wartość zmiennej środowiskowej `PREFIX_ENVIRONMENT` staje `environment` się wartością konfiguracji hosta dla klucza.</span><span class="sxs-lookup"><span data-stu-id="bb019-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="bb019-707">Podczas tworzenia podczas korzystania z programu `dotnet run`Visual [Studio](https://visualstudio.microsoft.com) lub uruchamiania aplikacji z , zmienne środowiskowe mogą być ustawione w pliku *Properties/launchSettings.json.*</span><span class="sxs-lookup"><span data-stu-id="bb019-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="bb019-708">W [programie Visual Studio Code](https://code.visualstudio.com/)zmienne środowiskowe mogą być ustawiane w pliku *vscode/launch.json* podczas tworzenia.</span><span class="sxs-lookup"><span data-stu-id="bb019-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="bb019-709">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="bb019-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="bb019-710">[Konfiguracja wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>jest dodawana przez wywołanie .</span><span class="sxs-lookup"><span data-stu-id="bb019-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="bb019-711">Konfiguracja wiersza polecenia jest dodawana jako ostatnia, aby umożliwić argumentom wiersza polecenia zastąpienie konfiguracji dostarczonej przez wcześniejszych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="bb019-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="bb019-712">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="bb019-712">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="bb019-713">Dodatkowa konfiguracja może być dostarczona z [kluczami applicationName](#application-key-name) i [contentRoot.](#content-root)</span><span class="sxs-lookup"><span data-stu-id="bb019-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="bb019-714">Przykładowa `HostBuilder` <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>konfiguracja przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="bb019-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="bb019-715">Konfigurowanie konfiguracjiappconfiguration</span><span class="sxs-lookup"><span data-stu-id="bb019-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="bb019-716">Konfiguracja aplikacji jest <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> tworzona przez wywołanie <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="bb019-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>używa do <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> utworzenia <xref:Microsoft.Extensions.Configuration.IConfiguration> aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="bb019-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="bb019-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="bb019-719">Aplikacja używa dowolnej opcji ustawia wartość ostatnio na danym kluczu.</span><span class="sxs-lookup"><span data-stu-id="bb019-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="bb019-720">Konfiguracja utworzona <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> przez jest dostępna w [witrynie HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) dla kolejnych operacji i w <xref:Microsoft.Extensions.Hosting.IHost.Services*>programie .</span><span class="sxs-lookup"><span data-stu-id="bb019-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="bb019-721">Konfiguracja aplikacji automatycznie odbiera konfigurację hosta udostępnianą przez [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="bb019-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="bb019-722">Przykładowa konfiguracja aplikacji przy użyciu: <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*></span><span class="sxs-lookup"><span data-stu-id="bb019-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="bb019-723">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="bb019-723">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="bb019-724">*appsettings. Development.json*:</span><span class="sxs-lookup"><span data-stu-id="bb019-724">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="bb019-725">*appsettings. Produkcja.json*:</span><span class="sxs-lookup"><span data-stu-id="bb019-725">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="bb019-726">Aby przenieść pliki ustawień do katalogu wyjściowego, określ pliki ustawień jako [elementy projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="bb019-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="bb019-727">Przykładowa aplikacja przenosi pliki ustawień aplikacji JSON i *hostsettings.json* z następującym `<Content>` elementem:</span><span class="sxs-lookup"><span data-stu-id="bb019-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="bb019-728">Metody rozszerzenia konfiguracji, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> takie jak i wymagają dodatkowych pakietów NuGet, takich jak [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) i [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="bb019-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="bb019-729">Chyba że aplikacja używa [microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), pakiety te muszą zostać dodane do projektu oprócz podstawowego pakietu [Microsoft.Extensions.Configuration.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration)</span><span class="sxs-lookup"><span data-stu-id="bb019-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="bb019-730">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="bb019-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="bb019-731">Konfigurowanie usług</span><span class="sxs-lookup"><span data-stu-id="bb019-731">ConfigureServices</span></span>

<span data-ttu-id="bb019-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>dodaje usługi do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="bb019-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="bb019-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="bb019-734">Usługa hostowana jest klasą z logiką <xref:Microsoft.Extensions.Hosting.IHostedService> zadań w tle, która implementuje interfejs.</span><span class="sxs-lookup"><span data-stu-id="bb019-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="bb019-735">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="bb019-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="bb019-736">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) używa `AddHostedService` metody rozszerzenia, aby dodać `LifetimeEventsHostedService`usługę dla zdarzeń okresu `TimedHostedService`istnienia i zadania w tle z odpowiednim czasem, do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="bb019-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="bb019-737">Konfigurowanie rejestrowania</span><span class="sxs-lookup"><span data-stu-id="bb019-737">ConfigureLogging</span></span>

<span data-ttu-id="bb019-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>dodaje pełnomocnika do skonfigurowania dostarczonego <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span><span class="sxs-lookup"><span data-stu-id="bb019-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="bb019-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="bb019-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="bb019-740">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="bb019-740">UseConsoleLifetime</span></span>

<span data-ttu-id="bb019-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>nasłuchuje <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM i wywołuje, <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="bb019-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="bb019-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="bb019-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="bb019-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`jest wstępnie zarejestrowany jako domyślna implementacja okresu istnienia.</span><span class="sxs-lookup"><span data-stu-id="bb019-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="bb019-744">Używany jest ostatni zarejestrowany okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="bb019-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="bb019-745">Konfiguracja kontenera</span><span class="sxs-lookup"><span data-stu-id="bb019-745">Container configuration</span></span>

<span data-ttu-id="bb019-746">Aby obsługiwać podłączanie do innych kontenerów, host może zaakceptować plik <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="bb019-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="bb019-747">Zapewnienie fabryki nie jest częścią rejestracji kontenera DI, ale zamiast tego jest nieodłącznym elementem hosta używanym do tworzenia betonowego kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="bb019-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="bb019-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder)&gt;](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) zastępuje domyślną fabrykę używaną do utworzenia dostawcy usług aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="bb019-749">Niestandardowa konfiguracja kontenera jest zarządzana <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> przez metodę.</span><span class="sxs-lookup"><span data-stu-id="bb019-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="bb019-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>zapewnia silnie typizowane środowisko konfigurowania kontenera na podstawie podstawowego interfejsu API hosta.</span><span class="sxs-lookup"><span data-stu-id="bb019-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="bb019-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>można nazwać wiele razy z addytywnymi wynikami.</span><span class="sxs-lookup"><span data-stu-id="bb019-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="bb019-752">Tworzenie kontenera usługi dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="bb019-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="bb019-753">Zapewnienie fabryki kontenerów serwisowych:</span><span class="sxs-lookup"><span data-stu-id="bb019-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="bb019-754">Użyj ustawień fabrycznych i skonfiguruj niestandardowy kontener usługi dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="bb019-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="bb019-755">Rozszerzalność</span><span class="sxs-lookup"><span data-stu-id="bb019-755">Extensibility</span></span>

<span data-ttu-id="bb019-756">Rozszerzalność hosta jest wykonywana <xref:Microsoft.Extensions.Hosting.IHostBuilder>przy metodach rozszerzeń na .</span><span class="sxs-lookup"><span data-stu-id="bb019-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="bb019-757">W poniższym przykładzie pokazano, <xref:Microsoft.Extensions.Hosting.IHostBuilder> jak metoda rozszerzenia rozszerza implementację za <xref:fundamentals/host/hosted-services>pomocą przykładu [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) przedstawionego w .</span><span class="sxs-lookup"><span data-stu-id="bb019-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="bb019-758">Aplikacja ustanawia metodę `UseHostedService` rozszerzenia, aby zarejestrować usługę `T`hosta przekazywana w:</span><span class="sxs-lookup"><span data-stu-id="bb019-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="bb019-759">Zarządzanie hostem</span><span class="sxs-lookup"><span data-stu-id="bb019-759">Manage the host</span></span>

<span data-ttu-id="bb019-760">Implementacja <xref:Microsoft.Extensions.Hosting.IHost> jest odpowiedzialny za <xref:Microsoft.Extensions.Hosting.IHostedService> uruchamianie i zatrzymywanie implementacji, które są zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="bb019-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="bb019-761">Run</span><span class="sxs-lookup"><span data-stu-id="bb019-761">Run</span></span>

<span data-ttu-id="bb019-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący, dopóki host nie zostanie zamknięty:</span><span class="sxs-lookup"><span data-stu-id="bb019-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="bb019-763">RunAsync ( RunAsync )</span><span class="sxs-lookup"><span data-stu-id="bb019-763">RunAsync</span></span>

<span data-ttu-id="bb019-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i <xref:System.Threading.Tasks.Task> zwraca, który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia systemu:</span><span class="sxs-lookup"><span data-stu-id="bb019-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="bb019-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="bb019-765">RunConsoleAsync</span></span>

<span data-ttu-id="bb019-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>umożliwia obsługę konsoli, buduje i uruchamia hosta i czeka na <kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM, aby zamknąć.</span><span class="sxs-lookup"><span data-stu-id="bb019-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="bb019-767">Start i StopAsync</span><span class="sxs-lookup"><span data-stu-id="bb019-767">Start and StopAsync</span></span>

<span data-ttu-id="bb019-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="bb019-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="bb019-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w podanym limit czasu.</span><span class="sxs-lookup"><span data-stu-id="bb019-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="bb019-770">StartAsync i StopAsync</span><span class="sxs-lookup"><span data-stu-id="bb019-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="bb019-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="bb019-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="bb019-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zatrzymuje aplikację.</span><span class="sxs-lookup"><span data-stu-id="bb019-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="bb019-773">WaitForShutdown (WaitForShutdown)</span><span class="sxs-lookup"><span data-stu-id="bb019-773">WaitForShutdown</span></span>

<span data-ttu-id="bb019-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>jest wyzwalany <xref:Microsoft.Extensions.Hosting.IHostLifetime>przez , `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` takich jak (nasłuchuje <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT lub SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="bb019-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="bb019-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>połączeń <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>telefonicznych .</span><span class="sxs-lookup"><span data-stu-id="bb019-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="bb019-776">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="bb019-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="bb019-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*><xref:System.Threading.Tasks.Task> zwraca, który kończy się po zamknięciu jest <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>wyzwalany za pośrednictwem danego tokenu i wywołania .</span><span class="sxs-lookup"><span data-stu-id="bb019-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="bb019-778">Sterowanie zewnętrzne</span><span class="sxs-lookup"><span data-stu-id="bb019-778">External control</span></span>

<span data-ttu-id="bb019-779">Zewnętrzna kontrola hosta można osiągnąć za pomocą metod, które można wywołać zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="bb019-779">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="bb019-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>początku , który czeka, aż zakończy się przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="bb019-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="bb019-781">Może to służyć do opóźnienia uruchamiania, dopóki nie zostanie zasygnaligozowana przez zdarzenie zewnętrzne.</span><span class="sxs-lookup"><span data-stu-id="bb019-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="bb019-782">Interfejs IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="bb019-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="bb019-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>zawiera informacje o środowisku hostingowym aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="bb019-784">Użyj [iniekcji konstruktora,](xref:fundamentals/dependency-injection) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> aby uzyskać w celu wykorzystania jego właściwości i metody rozszerzenia:</span><span class="sxs-lookup"><span data-stu-id="bb019-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="bb019-785">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="bb019-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="bb019-786">Interfejs IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="bb019-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="bb019-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>umożliwia działania po starcie i zamknięciu, w tym wdzięczne żądania zamknięcia systemu.</span><span class="sxs-lookup"><span data-stu-id="bb019-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="bb019-788">Trzy właściwości interfejsu są tokeny anulowania <xref:System.Action> używane do rejestrowania metod, które definiują zdarzenia uruchamiania i zamykania.</span><span class="sxs-lookup"><span data-stu-id="bb019-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="bb019-789">Token anulowania</span><span class="sxs-lookup"><span data-stu-id="bb019-789">Cancellation Token</span></span> | <span data-ttu-id="bb019-790">Wyzwalane podczas&#8230;</span><span class="sxs-lookup"><span data-stu-id="bb019-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="bb019-791">Gospodarz w pełni się rozpoczął.</span><span class="sxs-lookup"><span data-stu-id="bb019-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="bb019-792">Host kończy wdzięczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="bb019-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="bb019-793">Wszystkie wnioski powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="bb019-793">All requests should be processed.</span></span> <span data-ttu-id="bb019-794">Zamykanie bloków, dopóki to zdarzenie nie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="bb019-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="bb019-795">Host wykonuje wdzięku zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="bb019-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="bb019-796">Żądania mogą nadal być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="bb019-796">Requests may still be processing.</span></span> <span data-ttu-id="bb019-797">Zamykanie bloków, dopóki to zdarzenie nie zostanie zakończone.</span><span class="sxs-lookup"><span data-stu-id="bb019-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="bb019-798">Konstruktor-wstrzyknąć <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> usługę do dowolnej klasy.</span><span class="sxs-lookup"><span data-stu-id="bb019-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="bb019-799">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) używa iniekcji konstruktora `LifetimeEventsHostedService` do klasy <xref:Microsoft.Extensions.Hosting.IHostedService> (implementacji), aby zarejestrować zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="bb019-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="bb019-800">*LifetimeEventsHostedService.cs:*</span><span class="sxs-lookup"><span data-stu-id="bb019-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="bb019-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>żąda rozwiązania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="bb019-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="bb019-802">Następująca klasa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> używa do bezpiecznie zamknąć aplikację, `Shutdown` gdy wywoływana jest metoda klasy:</span><span class="sxs-lookup"><span data-stu-id="bb019-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="bb019-803">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="bb019-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
