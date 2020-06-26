---
title: Host ogólny .NET
author: rick-anderson
description: Dowiedz się więcej o hoście ogólnym programu .NET Core, który jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/generic-host
ms.openlocfilehash: eb0797a26393ce425d07ea9a97109b7fe8983f91
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407879"
---
# <a name="net-generic-host"></a><span data-ttu-id="ebbc4-103">Host ogólny .NET</span><span class="sxs-lookup"><span data-stu-id="ebbc4-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="ebbc4-104">Szablony ASP.NET Core tworzą hosta ogólnego platformy .NET Core, <xref:Microsoft.Extensions.Hosting.HostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="ebbc4-105">Definicja hosta</span><span class="sxs-lookup"><span data-stu-id="ebbc4-105">Host definition</span></span>

<span data-ttu-id="ebbc4-106">*Host* to obiekt, który hermetyzuje zasoby aplikacji, takie jak:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="ebbc4-107">Iniekcja zależności (DI)</span><span class="sxs-lookup"><span data-stu-id="ebbc4-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="ebbc4-108">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="ebbc4-108">Logging</span></span>
* <span data-ttu-id="ebbc4-109">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="ebbc4-109">Configuration</span></span>
* <span data-ttu-id="ebbc4-110">`IHostedService`metod</span><span class="sxs-lookup"><span data-stu-id="ebbc4-110">`IHostedService` implementations</span></span>

<span data-ttu-id="ebbc4-111">Po uruchomieniu hosta wywołuje on `IHostedService.StartAsync` każdą implementację <xref:Microsoft.Extensions.Hosting.IHostedService> , która znajduje się w kontenerze di.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="ebbc4-112">W aplikacji sieci Web jedną z `IHostedService` implementacji jest usługa sieci Web, która uruchamia [implementację serwera http](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="ebbc4-113">Główną przyczyną uwzględnienia wszystkich zasobów zależnych od aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: Kontrola uruchamiania aplikacji i bezpieczne zamykanie.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="ebbc4-114">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="ebbc4-114">Set up a host</span></span>

<span data-ttu-id="ebbc4-115">Host jest zazwyczaj konfigurowany, zbudowany i uruchamiany przez kod w `Program` klasie.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="ebbc4-116">`Main`Metoda:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-116">The `Main` method:</span></span>

* <span data-ttu-id="ebbc4-117">Wywołuje `CreateHostBuilder` metodę, aby utworzyć i skonfigurować obiekt konstruktora.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="ebbc4-118">Wywołania `Build` i `Run` metody w obiekcie konstruktora.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="ebbc4-119">Szablony sieci Web ASP.NET Core generują następujący kod w celu utworzenia hosta ogólnego:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="ebbc4-120">Poniższy kod tworzy hosta ogólnego przy użyciu obciążenia innego niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="ebbc4-121">`IHostedService`Implementacja jest dodawana do kontenera di:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-121">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="ebbc4-122">W przypadku obciążeń HTTP `Main` Metoda jest taka sama, ale `CreateHostBuilder` wywołuje `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="ebbc4-123">Poprzedni kod jest generowany przez szablony ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="ebbc4-124">Jeśli aplikacja używa Entity Framework Core, nie zmieniaj nazwy ani podpisu `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="ebbc4-125">[Narzędzia Entity Framework Core](/ef/core/miscellaneous/cli/) oczekują na znalezienie `CreateHostBuilder` metody, która konfiguruje hosta bez uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="ebbc4-126">Aby uzyskać więcej informacji, zobacz [Tworzenie DbContext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="ebbc4-127">Ustawienia domyślnego konstruktora</span><span class="sxs-lookup"><span data-stu-id="ebbc4-127">Default builder settings</span></span>

<span data-ttu-id="ebbc4-128"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="ebbc4-129">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na ścieżkę zwracaną przez <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="ebbc4-130">Ładuje konfigurację hosta z:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="ebbc4-131">Zmienne środowiskowe poprzedzone prefiksem `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="ebbc4-132">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-132">Command-line arguments.</span></span>
* <span data-ttu-id="ebbc4-133">Ładuje konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="ebbc4-134">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="ebbc4-135">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="ebbc4-136">[Secret Manager](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="ebbc4-137">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-137">Environment variables.</span></span>
  * <span data-ttu-id="ebbc4-138">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-138">Command-line arguments.</span></span>
* <span data-ttu-id="ebbc4-139">Dodaje następujących dostawców [rejestrowania](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="ebbc4-140">Konsola</span><span class="sxs-lookup"><span data-stu-id="ebbc4-140">Console</span></span>
  * <span data-ttu-id="ebbc4-141">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="ebbc4-141">Debug</span></span>
  * <span data-ttu-id="ebbc4-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="ebbc4-142">EventSource</span></span>
  * <span data-ttu-id="ebbc4-143">EventLog (tylko w przypadku uruchamiania w systemie Windows)</span><span class="sxs-lookup"><span data-stu-id="ebbc4-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="ebbc4-144">Umożliwia [weryfikację zakresu](xref:fundamentals/dependency-injection#scope-validation) i [Sprawdzanie poprawności zależności](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) , gdy środowisko jest opracowywane.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="ebbc4-145">`ConfigureWebHostDefaults`Metoda:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="ebbc4-146">Ładuje konfigurację hosta ze zmiennych środowiskowych, które są poprzedzone prefiksem `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="ebbc4-147">Ustawia serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci Web i konfiguruje go przy użyciu dostawców konfiguracji hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="ebbc4-148">Aby poznać domyślne opcje serwera Kestrel, zobacz <xref:fundamentals/servers/kestrel#kestrel-options> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="ebbc4-149">Dodaje [oprogramowanie pośredniczące do filtrowania hosta](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="ebbc4-150">Dodaje [przekazane nagłówki — oprogramowanie pośredniczące](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest równe `true` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="ebbc4-151">Włącza integrację usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-151">Enables IIS integration.</span></span> <span data-ttu-id="ebbc4-152">Aby poznać domyślne opcje usług IIS, zobacz <xref:host-and-deploy/iis/index#iis-options> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="ebbc4-153">[Ustawienia dla wszystkich typów aplikacji](#settings-for-all-app-types) i [Ustawienia dla usługi Web Apps](#settings-for-web-apps) w dalszej części tego artykułu pokazują, jak zastąpić ustawienia domyślnego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="ebbc4-154">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="ebbc4-154">Framework-provided services</span></span>

<span data-ttu-id="ebbc4-155">Następujące usługi są rejestrowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="ebbc4-156">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ebbc4-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="ebbc4-157">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="ebbc4-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="ebbc4-158">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="ebbc4-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="ebbc4-159">Aby uzyskać więcej informacji na temat usług udostępnianych przez platformę, zobacz <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="ebbc4-160">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ebbc4-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="ebbc4-161">Wstrzyknąć <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> usługę (dawniej `IApplicationLifetime` ) do dowolnej klasy w celu obsługi zadań po uruchomieniu i bezpiecznego zamykania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="ebbc4-162">Trzy właściwości interfejsu to tokeny anulowania używane do rejestrowania metod obsługi zdarzeń uruchamiania i zatrzymywania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="ebbc4-163">Interfejs zawiera również `StopApplication` metodę.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="ebbc4-164">Poniższy przykład to `IHostedService` implementacja, która rejestruje `IHostApplicationLifetime` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="ebbc4-165">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="ebbc4-165">IHostLifetime</span></span>

<span data-ttu-id="ebbc4-166"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Implementacja kontroluje moment uruchomienia hosta i jego zatrzymania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="ebbc4-167">Używana jest Ostatnia zarejestrowana implementacja.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-167">The last implementation registered is used.</span></span>

<span data-ttu-id="ebbc4-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`jest `IHostLifetime` implementacją domyślną.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="ebbc4-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="ebbc4-170">Nasłuchuje <kbd>kombinacji klawiszy CTRL</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM oraz wywołań, <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> Aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="ebbc4-171">Odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="ebbc4-172">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="ebbc4-172">IHostEnvironment</span></span>

<span data-ttu-id="ebbc4-173">Wsuń <xref:Microsoft.Extensions.Hosting.IHostEnvironment> usługę do klasy, aby uzyskać informacje o następujących ustawieniach:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="ebbc4-174">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="ebbc4-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="ebbc4-175">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="ebbc4-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="ebbc4-176">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="ebbc4-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="ebbc4-177">Aplikacje sieci Web implementują `IWebHostEnvironment` interfejs, który dziedziczy `IHostEnvironment` i dodaje [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="ebbc4-178">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="ebbc4-178">Host configuration</span></span>

<span data-ttu-id="ebbc4-179">Konfiguracja hosta jest używana we właściwościach <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="ebbc4-180">Konfiguracja hosta jest dostępna w [HostBuilderContext.Configwersja](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="ebbc4-181">Po `ConfigureAppConfiguration` program `HostBuilderContext.Configuration` zostanie zastąpiony konfiguracją aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="ebbc4-182">Aby dodać konfigurację hosta, wywołaj polecenie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="ebbc4-183">`ConfigureHostConfiguration`może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="ebbc4-184">Host używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="ebbc4-185">Dostawca zmiennych środowiskowych z prefiksami `DOTNET_` i argumentami wiersza polecenia są uwzględniane przez `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ebbc4-186">W przypadku aplikacji sieci Web jest dodawany dostawca zmiennych środowiskowych z prefiksem `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="ebbc4-187">Prefiks jest usuwany, gdy są odczytywane zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="ebbc4-188">Na przykład wartość zmiennej środowiskowej jest `ASPNETCORE_ENVIRONMENT` wartością konfiguracji hosta dla `environment` klucza.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="ebbc4-189">Poniższy przykład umożliwia utworzenie konfiguracji hosta:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="ebbc4-190">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="ebbc4-190">App configuration</span></span>

<span data-ttu-id="ebbc4-191">Konfiguracja aplikacji jest tworzona przez wywołanie metody <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="ebbc4-192">`ConfigureAppConfiguration`może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="ebbc4-193">Aplikacja używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="ebbc4-194">Konfiguracja utworzona przez program `ConfigureAppConfiguration` jest dostępna w [HostBuilderContext.Configwersja](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) na potrzeby kolejnych operacji i jako usługa z di.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="ebbc4-195">Konfiguracja hosta jest również dodawana do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="ebbc4-196">Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="ebbc4-197">Ustawienia dla wszystkich typów aplikacji</span><span class="sxs-lookup"><span data-stu-id="ebbc4-197">Settings for all app types</span></span>

<span data-ttu-id="ebbc4-198">Ta sekcja zawiera listę ustawień hosta, które dotyczą zarówno obciążeń HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="ebbc4-199">Domyślnie zmienne środowiskowe używane do konfigurowania tych ustawień mogą mieć `DOTNET_` `ASPNETCORE_` prefiks lub.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="ebbc4-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="ebbc4-200">ApplicationName</span></span>

<span data-ttu-id="ebbc4-201">Właściwość [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) jest ustawiana na podstawie konfiguracji hosta podczas konstruowania hosta.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="ebbc4-202">**Klucz**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="ebbc4-203">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-203">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-204">**Wartość domyślna**: Nazwa zestawu, który zawiera punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="ebbc4-205">**Zmienna środowiskowa**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="ebbc4-206">Aby ustawić tę wartość, należy użyć zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="ebbc4-207">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="ebbc4-207">ContentRoot</span></span>

<span data-ttu-id="ebbc4-208">Właściwość [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="ebbc4-209">Jeśli ścieżka nie istnieje, uruchomienie hosta nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="ebbc4-210">**Klucz**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="ebbc4-211">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-211">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-212">**Domyślnie**: folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="ebbc4-213">**Zmienna środowiskowa**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="ebbc4-214">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseContentRoot` `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="ebbc4-215">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-215">For more information, see:</span></span>

* [<span data-ttu-id="ebbc4-216">Podstawy: zawartość główna</span><span class="sxs-lookup"><span data-stu-id="ebbc4-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="ebbc4-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="ebbc4-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="ebbc4-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="ebbc4-218">EnvironmentName</span></span>

<span data-ttu-id="ebbc4-219">Dla właściwości [IHostEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) można ustawić dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="ebbc4-220">Wartości zdefiniowane przez platformę obejmują `Development` , `Staging` , i `Production` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="ebbc4-221">W wartościach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="ebbc4-222">**Klucz**:`environment`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-222">**Key**: `environment`</span></span>  
<span data-ttu-id="ebbc4-223">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-223">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-224">**Wartość domyślna**:`Production`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-224">**Default**: `Production`</span></span>  
<span data-ttu-id="ebbc4-225">**Zmienna środowiskowa**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="ebbc4-226">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseEnvironment` `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="ebbc4-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="ebbc4-227">ShutdownTimeout</span></span>

<span data-ttu-id="ebbc4-228">[HostOptions. shutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) ustawia limit czasu dla <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="ebbc4-229">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-229">The default value is five seconds.</span></span>  <span data-ttu-id="ebbc4-230">Podczas okresu przekroczenia limitu czasu Host:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="ebbc4-231">Wyzwala [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="ebbc4-232">Próbuje zatrzymać usługi hostowane, rejestrowanie błędów dla usług, których zatrzymanie nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="ebbc4-233">Jeśli limit czasu upłynie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="ebbc4-234">Usługi są zatrzymane nawet wtedy, gdy nie zakończyły przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="ebbc4-235">Jeśli usługi wymagają dodatkowego czasu na zatrzymanie, zwiększ limit czasu.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="ebbc4-236">**Klucz**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="ebbc4-237">**Typ**:`int`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-237">**Type**: `int`</span></span>  
<span data-ttu-id="ebbc4-238">**Wartość domyślna**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="ebbc4-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="ebbc4-239">**Zmienna środowiskowa**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="ebbc4-240">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub skonfiguruj `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="ebbc4-241">Poniższy przykład ustawia limit czasu na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="ebbc4-242">Ustawienia usługi Web Apps</span><span class="sxs-lookup"><span data-stu-id="ebbc4-242">Settings for web apps</span></span>

<span data-ttu-id="ebbc4-243">Niektóre ustawienia hosta mają zastosowanie tylko do obciążeń protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="ebbc4-244">Domyślnie zmienne środowiskowe używane do konfigurowania tych ustawień mogą mieć `DOTNET_` `ASPNETCORE_` prefiks lub.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="ebbc4-245">Metody rozszerzenia dla `IWebHostBuilder` programu są dostępne dla tych ustawień.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="ebbc4-246">Przykłady kodu, które pokazują, jak wywołać metody rozszerzane `webBuilder` jest wystąpieniem `IWebHostBuilder` , tak jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="ebbc4-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="ebbc4-247">CaptureStartupErrors</span></span>

<span data-ttu-id="ebbc4-248">Gdy `false` , błędy podczas uruchamiania, kończy się hostem.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="ebbc4-249">Gdy `true` host przechwytuje wyjątki podczas uruchamiania, a następnie próbuje uruchomić serwer.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="ebbc4-250">**Klucz**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="ebbc4-251">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="ebbc4-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebbc4-252">**Domyślnie**: wartość domyślna to, `false` chyba że aplikacja działa z Kestrel za usługami IIS, gdzie wartość domyślna to `true` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="ebbc4-253">**Zmienna środowiskowa**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="ebbc4-254">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="ebbc4-255">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="ebbc4-255">DetailedErrors</span></span>

<span data-ttu-id="ebbc4-256">Po włączeniu lub gdy środowisko jest `Development` , aplikacja przechwytuje szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="ebbc4-257">**Klucz**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="ebbc4-258">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="ebbc4-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebbc4-259">**Wartość domyślna**:`false`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-259">**Default**: `false`</span></span>  
<span data-ttu-id="ebbc4-260">**Zmienna środowiskowa**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="ebbc4-261">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="ebbc4-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="ebbc4-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="ebbc4-263">Rozdzielany średnikami ciąg początkowych zestawów startowych do załadowania podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="ebbc4-264">Mimo że wartość konfiguracji jest domyślnie pustym ciągiem, zestaw startowy obsługujący zawsze zawiera zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="ebbc4-265">W przypadku udostępniania zestawów startowych są one dodawane do zestawu aplikacji do załadowania, gdy aplikacja kompiluje swoje popularne usługi podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="ebbc4-266">**Klucz**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="ebbc4-267">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-267">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-268">**Wartość domyślna**: pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="ebbc4-268">**Default**: Empty string</span></span>  
<span data-ttu-id="ebbc4-269">**Zmienna środowiskowa**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="ebbc4-270">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="ebbc4-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="ebbc4-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="ebbc4-272">Rozdzielany średnikami ciąg początkowych zestawów uruchamiania, który ma zostać wykluczony podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="ebbc4-273">**Klucz**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="ebbc4-274">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-274">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-275">**Wartość domyślna**: pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="ebbc4-275">**Default**: Empty string</span></span>  
<span data-ttu-id="ebbc4-276">**Zmienna środowiskowa**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="ebbc4-277">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="ebbc4-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="ebbc4-278">HTTPS_Port</span></span>

<span data-ttu-id="ebbc4-279">Port przekierowania protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-279">The HTTPS redirect port.</span></span> <span data-ttu-id="ebbc4-280">Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ebbc4-281">**Klucz**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="ebbc4-282">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-282">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-283">Wartość **Domyślna**: nie ustawiono wartości domyślnej.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="ebbc4-284">**Zmienna środowiskowa**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="ebbc4-285">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="ebbc4-286">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="ebbc4-286">PreferHostingUrls</span></span>

<span data-ttu-id="ebbc4-287">Wskazuje, czy host powinien nasłuchiwać adresów URL skonfigurowanych przy użyciu `IWebHostBuilder` zamiast adresów URL skonfigurowanych dla `IServer` implementacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="ebbc4-288">**Klucz**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="ebbc4-289">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="ebbc4-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebbc4-290">**Wartość domyślna**:`true`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-290">**Default**: `true`</span></span>  
<span data-ttu-id="ebbc4-291">**Zmienna środowiskowa**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="ebbc4-292">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="ebbc4-293">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="ebbc4-293">PreventHostingStartup</span></span>

<span data-ttu-id="ebbc4-294">Zapobiega automatycznemu ładowaniu zestawów startowych hostingu, w tym hostingu zestawów startowych skonfigurowanych przez zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="ebbc4-295">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="ebbc4-296">**Klucz**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="ebbc4-297">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="ebbc4-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebbc4-298">**Wartość domyślna**:`false`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-298">**Default**: `false`</span></span>  
<span data-ttu-id="ebbc4-299">**Zmienna środowiskowa**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="ebbc4-300">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="ebbc4-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="ebbc4-301">StartupAssembly</span></span>

<span data-ttu-id="ebbc4-302">Zestaw do wyszukiwania `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="ebbc4-303">**Klucz**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="ebbc4-304">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-304">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-305">**Domyślnie**: zestaw aplikacji</span><span class="sxs-lookup"><span data-stu-id="ebbc4-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="ebbc4-306">**Zmienna środowiskowa**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="ebbc4-307">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołania `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="ebbc4-308">`UseStartup`może przyjmować nazwę zestawu ( `string` ) lub typ ( `TStartup` ).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="ebbc4-309">Jeśli `UseStartup` wywoływana jest wiele metod, pierwszeństwo ma Ostatnia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="ebbc4-310">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="ebbc4-310">URLs</span></span>

<span data-ttu-id="ebbc4-311">Rozdzielana średnikami lista adresów IP lub adresów hostów z portami i protokołami, na których serwer powinien nasłuchiwać żądań.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="ebbc4-312">Na przykład `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="ebbc4-313">Użyj " \* ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwie hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000` ).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="ebbc4-314">Protokół ( `http://` lub `https://` ) musi być dołączony do każdego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="ebbc4-315">Obsługiwane formaty różnią się między serwerami.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="ebbc4-316">**Klucz**:`urls`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-316">**Key**: `urls`</span></span>  
<span data-ttu-id="ebbc4-317">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-317">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-318">**Wartość domyślna**: `http://localhost:5000` i`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="ebbc4-319">**Zmienna środowiskowa**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="ebbc4-320">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="ebbc4-321">Kestrel ma własny interfejs API konfiguracji punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="ebbc4-322">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="ebbc4-323">WebRoot</span><span class="sxs-lookup"><span data-stu-id="ebbc4-323">WebRoot</span></span>

<span data-ttu-id="ebbc4-324">Właściwość [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) określa ścieżkę względną do statycznych zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="ebbc4-325">Jeśli ścieżka nie istnieje, jest używany dostawca plików No-op.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="ebbc4-326">**Klucz**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="ebbc4-327">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-327">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-328">**Wartość domyślna**: wartość domyślna to `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="ebbc4-329">Ścieżka do *elementu {content root}/wwwroot* musi istnieć.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="ebbc4-330">**Zmienna środowiskowa**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="ebbc4-331">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseWebRoot` `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="ebbc4-332">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-332">For more information, see:</span></span>

* [<span data-ttu-id="ebbc4-333">Podstawy: katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="ebbc4-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="ebbc4-334">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="ebbc4-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="ebbc4-335">Zarządzanie okresem istnienia hosta</span><span class="sxs-lookup"><span data-stu-id="ebbc4-335">Manage the host lifetime</span></span>

<span data-ttu-id="ebbc4-336">Wywołaj metody z skompilowanej <xref:Microsoft.Extensions.Hosting.IHost> implementacji, aby uruchomić i zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="ebbc4-337">Te metody mają wpływ na wszystkie <xref:Microsoft.Extensions.Hosting.IHostedService> implementacje zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="ebbc4-338">Uruchom</span><span class="sxs-lookup"><span data-stu-id="ebbc4-338">Run</span></span>

<span data-ttu-id="ebbc4-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący do momentu wyłączenia hosta.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="ebbc4-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="ebbc4-340">RunAsync</span></span>

<span data-ttu-id="ebbc4-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i zwraca <xref:System.Threading.Tasks.Task> , która kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="ebbc4-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="ebbc4-342">RunConsoleAsync</span></span>

<span data-ttu-id="ebbc4-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>włącza obsługę konsoli, kompiluje i uruchamia hosta, a następnie czeka na zamknięcie programu <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="ebbc4-344">Rozpocznij</span><span class="sxs-lookup"><span data-stu-id="ebbc4-344">Start</span></span>

<span data-ttu-id="ebbc4-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="ebbc4-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="ebbc4-346">StartAsync</span></span>

<span data-ttu-id="ebbc4-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia hosta i zwraca <xref:System.Threading.Tasks.Task> , który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="ebbc4-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na początku `StartAsync` , który czeka na zakończenie przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="ebbc4-349">Może to służyć do opóźnienia uruchamiania do momentu zasygnalizowania zdarzenia zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="ebbc4-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="ebbc4-350">StopAsync</span></span>

<span data-ttu-id="ebbc4-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w ramach podanego limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="ebbc4-352">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="ebbc4-352">WaitForShutdown</span></span>

<span data-ttu-id="ebbc4-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje wątek wywołujący do momentu wyzwolenia przez IHostLifetime, na przykład przez <kbd>naciśnięcie klawiszy CTRL</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="ebbc4-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="ebbc4-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="ebbc4-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>Zwraca wartość <xref:System.Threading.Tasks.Task> , która kończy się, gdy zamknięcie jest wyzwalane za pośrednictwem danego tokenu i wywołań <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="ebbc4-356">Zewnętrzna kontrola</span><span class="sxs-lookup"><span data-stu-id="ebbc4-356">External control</span></span>

<span data-ttu-id="ebbc4-357">Bezpośrednią kontrolę nad okresem istnienia hosta można uzyskać przy użyciu metod, które mogą być wywoływane zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="ebbc4-358">ASP.NET Core aplikacje konfigurują i uruchamiają hosta.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="ebbc4-359">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="ebbc4-360">W tym artykule opisano ASP.NET Core hosta ogólnego ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ), który jest używany w przypadku aplikacji, które nie przetwarzają żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="ebbc4-361">Przeznaczeniem hosta ogólnego jest oddzielenie potoku HTTP od interfejsu API hosta sieci Web w celu umożliwienia szerszej tablicy scenariuszy hostów.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="ebbc4-362">Obsługa komunikatów, zadań w tle i innych obciążeń innych niż HTTP na podstawie ogólnej korzyści z hosta, takich jak konfiguracja, iniekcja zależności (DI) i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="ebbc4-363">Host ogólny jest nowy w ASP.NET Core 2,1 i nie jest odpowiedni dla scenariuszy hostingu w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="ebbc4-364">W przypadku scenariuszy hostingu w sieci Web należy użyć [hosta sieci Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="ebbc4-365">Host ogólny zastąpi hosta sieci Web w przyszłej wersji i będzie pełnić rolę podstawowego interfejsu API hosta zarówno w scenariuszach HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="ebbc4-366">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ebbc4-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ebbc4-367">Podczas uruchamiania przykładowej aplikacji w [Visual Studio Code](https://code.visualstudio.com/)należy użyć *zewnętrznego lub zintegrowanego terminalu*.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="ebbc4-368">Nie uruchamiaj próbki w `internalConsole` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="ebbc4-369">Aby ustawić konsolę w Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="ebbc4-370">Otwórz plik *. programu vscode/launch.jsw* pliku.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="ebbc4-371">W konfiguracji **uruchamiania programu .NET Core (konsoli)** zlokalizuj wpis **konsoli** .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="ebbc4-372">Ustaw wartość na `externalTerminal` lub `integratedTerminal` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="ebbc4-373">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="ebbc4-373">Introduction</span></span>

<span data-ttu-id="ebbc4-374">Ogólna Biblioteka hostów jest dostępna w <xref:Microsoft.Extensions.Hosting> przestrzeni nazw i udostępniana przez pakiet [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="ebbc4-375">`Microsoft.Extensions.Hosting`Pakiet jest zawarty w [pakiecie Microsoft. AspNetCore. appbinding](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 lub nowszym).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="ebbc4-376"><xref:Microsoft.Extensions.Hosting.IHostedService>jest punktem wejścia do wykonania kodu.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="ebbc4-377">Każda <xref:Microsoft.Extensions.Hosting.IHostedService> implementacja jest wykonywana w kolejności [rejestracji usługi w ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="ebbc4-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>jest wywoływana przy każdym <xref:Microsoft.Extensions.Hosting.IHostedService> uruchomieniu hosta i <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> jest wywoływana w odwrotnej kolejności rejestracji, gdy host jest zamykany prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="ebbc4-379">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="ebbc4-379">Set up a host</span></span>

<span data-ttu-id="ebbc4-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder>jest głównym składnikiem używanym przez biblioteki i aplikacje do inicjowania, kompilowania i uruchamiania hosta:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="ebbc4-381">Opcje</span><span class="sxs-lookup"><span data-stu-id="ebbc4-381">Options</span></span>

<span data-ttu-id="ebbc4-382"><xref:Microsoft.Extensions.Hosting.HostOptions>Skonfiguruj opcje <xref:Microsoft.Extensions.Hosting.IHost> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="ebbc4-383">Limit czasu zamykania</span><span class="sxs-lookup"><span data-stu-id="ebbc4-383">Shutdown timeout</span></span>

<span data-ttu-id="ebbc4-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>ustawia limit czasu dla <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="ebbc4-385">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-385">The default value is five seconds.</span></span>

<span data-ttu-id="ebbc4-386">Następująca konfiguracja opcji w programie `Program.Main` powoduje zwiększenie domyślnego limitu czasu zamykania pięciu sekund:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="ebbc4-387">Usługi domyślne</span><span class="sxs-lookup"><span data-stu-id="ebbc4-387">Default services</span></span>

<span data-ttu-id="ebbc4-388">Podczas inicjowania hosta zarejestrowano następujące usługi:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="ebbc4-389">[Środowisko](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="ebbc4-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="ebbc4-390">[Konfiguracja](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="ebbc4-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="ebbc4-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="ebbc4-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="ebbc4-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="ebbc4-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="ebbc4-393">[Opcje](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="ebbc4-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="ebbc4-394">[Rejestrowanie](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="ebbc4-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="ebbc4-395">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="ebbc4-395">Host configuration</span></span>

<span data-ttu-id="ebbc4-396">Konfiguracja hosta jest tworzona przez:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-396">Host configuration is created by:</span></span>

* <span data-ttu-id="ebbc4-397">Wywoływanie metod rozszerzenia w <xref:Microsoft.Extensions.Hosting.IHostBuilder> celu ustawienia [katalogu głównego zawartości](#content-root) i [środowiska](#environment).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="ebbc4-398">Odczytywanie konfiguracji od dostawców konfiguracji w programie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="ebbc4-399">Metody rozszerzeń</span><span class="sxs-lookup"><span data-stu-id="ebbc4-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="ebbc4-400">Klucz aplikacji (nazwa)</span><span class="sxs-lookup"><span data-stu-id="ebbc4-400">Application key (name)</span></span>

<span data-ttu-id="ebbc4-401">Właściwość [IHostingEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) jest ustawiana na podstawie konfiguracji hosta podczas konstruowania hosta.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="ebbc4-402">Aby jawnie ustawić wartość, użyj [HostDefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="ebbc4-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="ebbc4-403">**Klucz**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="ebbc4-404">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-404">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-405">**Wartość domyślna**: Nazwa zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="ebbc4-406">**Ustaw przy użyciu**:`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="ebbc4-407">**Zmienna środowiskowa**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` jest [opcjonalne i zdefiniowane przez użytkownika](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="ebbc4-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="ebbc4-408">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="ebbc4-408">Content root</span></span>

<span data-ttu-id="ebbc4-409">To ustawienie określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="ebbc4-410">**Klucz**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="ebbc4-411">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-411">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-412">**Domyślnie**: Domyślnie folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="ebbc4-413">**Ustaw przy użyciu**:`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="ebbc4-414">**Zmienna środowiskowa**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` jest [opcjonalne i zdefiniowane przez użytkownika](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="ebbc4-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="ebbc4-415">Jeśli ścieżka nie istnieje, uruchomienie hosta nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="ebbc4-416">Aby uzyskać więcej informacji, zobacz temat [podstawy: zawartość główna](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="ebbc4-417">Środowisko</span><span class="sxs-lookup"><span data-stu-id="ebbc4-417">Environment</span></span>

<span data-ttu-id="ebbc4-418">Ustawia [środowisko](xref:fundamentals/environments)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="ebbc4-419">**Klucz**:`environment`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-419">**Key**: `environment`</span></span>  
<span data-ttu-id="ebbc4-420">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-420">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-421">**Wartość domyślna**:`Production`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-421">**Default**: `Production`</span></span>  
<span data-ttu-id="ebbc4-422">**Ustaw przy użyciu**:`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="ebbc4-423">**Zmienna środowiskowa**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` jest [opcjonalne i zdefiniowane przez użytkownika](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="ebbc4-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="ebbc4-424">Dla środowiska można ustawić dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-424">The environment can be set to any value.</span></span> <span data-ttu-id="ebbc4-425">Wartości zdefiniowane przez platformę obejmują `Development` , `Staging` , i `Production` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="ebbc4-426">W wartościach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="ebbc4-427">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="ebbc4-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="ebbc4-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>za pomocą programu <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> można utworzyć <xref:Microsoft.Extensions.Configuration.IConfiguration> dla hosta.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="ebbc4-429">Konfiguracja hosta służy do inicjowania do <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> użycia w procesie kompilacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="ebbc4-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="ebbc4-431">Host używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="ebbc4-432">Żaden dostawca nie jest domyślnie uwzględniany.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-432">No providers are included by default.</span></span> <span data-ttu-id="ebbc4-433">Należy jawnie określić dostawców konfiguracji, których wymaga aplikacja, w <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> tym:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="ebbc4-434">Konfiguracja pliku (na przykład z *hostsettings.jsw* pliku).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="ebbc4-435">Konfiguracja zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-435">Environment variable configuration.</span></span>
* <span data-ttu-id="ebbc4-436">Konfiguracja argumentu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="ebbc4-437">Każdy inny wymagany dostawca konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-437">Any other required configuration providers.</span></span>

<span data-ttu-id="ebbc4-438">Konfiguracja pliku hosta jest włączana przez określenie ścieżki podstawowej aplikacji, `SetBasePath` po której następuje wywołanie jednego z [dostawców konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="ebbc4-439">Przykładowa aplikacja używa pliku JSON, *hostsettings.json*i wywołuje <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> do użycia ustawienia konfiguracji hosta pliku.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="ebbc4-440">Aby dodać [konfigurację zmiennej środowiskowej](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hosta, wywołaj polecenie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> na konstruktorze hosta.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="ebbc4-441">`AddEnvironmentVariables`akceptuje opcjonalny prefiks zdefiniowany przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="ebbc4-442">Przykładowa aplikacja używa prefiksu `PREFIX_` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="ebbc4-443">Prefiks jest usuwany, gdy są odczytywane zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="ebbc4-444">Po skonfigurowaniu hosta przykładowej aplikacji wartość zmiennej środowiskowej dla `PREFIX_ENVIRONMENT` klucza będzie wartością konfiguracji hosta `environment` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="ebbc4-445">Podczas tworzenia w przypadku korzystania z [programu Visual Studio](https://visualstudio.microsoft.com) lub uruchamiania aplikacji w programie `dotnet run` zmienne środowiskowe mogą być ustawiane we *właściwościach/launchSettings.jsw* pliku.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="ebbc4-446">W [Visual Studio Code](https://code.visualstudio.com/)zmienne środowiskowe mogą być ustawiane w pliku *. programu vscode/launch.js* podczas opracowywania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="ebbc4-447">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="ebbc4-448">[Konfiguracja wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) jest dodawana przez wywołanie <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="ebbc4-449">Konfiguracja wiersza polecenia jest dodawana jako Ostatnia, aby zezwolić na argumenty wiersza polecenia w celu przesłonięcia konfiguracji udostępnionej przez wcześniejszych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="ebbc4-450">*hostsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-450">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="ebbc4-451">Dodatkową konfigurację można uzyskać za pomocą programu [ApplicationName](#application-key-name) i kluczy [contentRoot](#content-root) .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="ebbc4-452">Przykładowa `HostBuilder` Konfiguracja przy użyciu <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="ebbc4-453">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="ebbc4-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="ebbc4-454">Konfiguracja aplikacji jest tworzona przez wywołanie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="ebbc4-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>za pomocą programu <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> można utworzyć <xref:Microsoft.Extensions.Configuration.IConfiguration> dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="ebbc4-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="ebbc4-457">Aplikacja używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="ebbc4-458">Konfiguracja utworzona przez program <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> jest dostępna w [HostBuilderContext.Configwersja](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) na potrzeby kolejnych operacji i w <xref:Microsoft.Extensions.Hosting.IHost.Services*> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="ebbc4-459">Konfiguracja aplikacji automatycznie otrzymuje konfigurację hosta dostarczoną przez [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="ebbc4-460">Przykładowa konfiguracja aplikacji przy użyciu <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="ebbc4-461">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-461">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="ebbc4-462">*appsettings.Development.js*:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-462">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="ebbc4-463">*appsettings.Production.js*:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-463">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="ebbc4-464">Aby przenieść pliki ustawień do katalogu wyjściowego, określ pliki ustawień jako [elementy projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="ebbc4-465">Aplikacja Przykładowa przenosi pliki ustawień aplikacji JSON i *hostsettings.js* przy użyciu następującego `<Content>` elementu:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="ebbc4-466">Metody rozszerzenia konfiguracji, takie jak <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> i <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> wymagające dodatkowych pakietów NuGet, takie jak [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) i [Microsoft.Extensions.Configwersja. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="ebbc4-467">Jeśli aplikacja nie używa [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), należy dodać te pakiety do projektu oprócz podstawowego pakietu [Microsoft.Extensions.Configwersja](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="ebbc4-468">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="ebbc4-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="ebbc4-469">ConfigureServices</span></span>

<span data-ttu-id="ebbc4-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>dodaje usługi do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ebbc4-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="ebbc4-472">Usługa hostowana jest klasą z logiką zadań w tle, która implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> interfejs.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="ebbc4-473">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="ebbc4-474">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) używa `AddHostedService` metody rozszerzającej, aby dodać usługę dla zdarzeń okresu istnienia, `LifetimeEventsHostedService` oraz zadanie w tle czasu, `TimedHostedService` do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="ebbc4-475">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="ebbc4-475">ConfigureLogging</span></span>

<span data-ttu-id="ebbc4-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>dodaje delegata do konfigurowania podanego elementu <xref:Microsoft.Extensions.Logging.ILoggingBuilder> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="ebbc4-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="ebbc4-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="ebbc4-478">UseConsoleLifetime</span></span>

<span data-ttu-id="ebbc4-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>nasłuchuje <kbd>kombinacji klawiszy CTRL</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM oraz wywołań, <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> Aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="ebbc4-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="ebbc4-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`jest wstępnie zarejestrowany jako domyślna implementacja okresu istnienia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="ebbc4-482">Używany jest ostatni zarejestrowany okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="ebbc4-483">Konfiguracja kontenera</span><span class="sxs-lookup"><span data-stu-id="ebbc4-483">Container configuration</span></span>

<span data-ttu-id="ebbc4-484">Aby zapewnić obsługę podłączania w innych kontenerach, host może akceptować <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="ebbc4-485">Dostarczenie fabryki nie jest częścią rejestracji typu DI Container, ale zamiast tego jest wewnętrznym hostem używanym do tworzenia konkretnych kontenerów DI.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="ebbc4-486">[UseServiceProviderFactory (IServiceProviderFactory &lt; TContainerBuilder &gt; )](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) zastępuje domyślną fabrykę używaną do utworzenia dostawcy usług aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="ebbc4-487">Niestandardowa konfiguracja kontenera jest zarządzana przez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> metodę.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="ebbc4-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>Program zapewnia silnie wpisaną funkcję konfigurowania kontenera na podstawie podstawowego interfejsu API hosta.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="ebbc4-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="ebbc4-490">Utwórz kontener usługi dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="ebbc4-491">Podaj fabrykę kontenera usługi:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="ebbc4-492">Użyj fabryki i skonfiguruj niestandardowy kontener usługi dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="ebbc4-493">Rozszerzalność</span><span class="sxs-lookup"><span data-stu-id="ebbc4-493">Extensibility</span></span>

<span data-ttu-id="ebbc4-494">Rozszerzalność hosta jest wykonywana z metodami rozszerzenia w systemie <xref:Microsoft.Extensions.Hosting.IHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="ebbc4-495">Poniższy przykład pokazuje, jak Metoda rozszerzania rozszerza <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementację z przykładem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) przedstawionym w <xref:fundamentals/host/hosted-services> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="ebbc4-496">Aplikacja ustanowi `UseHostedService` metodę rozszerzenia w celu zarejestrowania przesyłanej usługi hostowanej `T` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="ebbc4-497">Zarządzanie hostem</span><span class="sxs-lookup"><span data-stu-id="ebbc4-497">Manage the host</span></span>

<span data-ttu-id="ebbc4-498"><xref:Microsoft.Extensions.Hosting.IHost>Implementacja jest odpowiedzialna za uruchamianie i zatrzymywanie <xref:Microsoft.Extensions.Hosting.IHostedService> implementacji, które są zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="ebbc4-499">Uruchom</span><span class="sxs-lookup"><span data-stu-id="ebbc4-499">Run</span></span>

<span data-ttu-id="ebbc4-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący do momentu wyłączenia hosta:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="ebbc4-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="ebbc4-501">RunAsync</span></span>

<span data-ttu-id="ebbc4-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i zwraca <xref:System.Threading.Tasks.Task> , która kończy się po wyzwoleniu tokenu anulowania lub zamknięcia:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="ebbc4-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="ebbc4-503">RunConsoleAsync</span></span>

<span data-ttu-id="ebbc4-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>włącza obsługę konsoli, kompiluje i uruchamia hosta, a następnie czeka na zamknięcie programu <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="ebbc4-505">Uruchom i StopAsync</span><span class="sxs-lookup"><span data-stu-id="ebbc4-505">Start and StopAsync</span></span>

<span data-ttu-id="ebbc4-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="ebbc4-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w ramach podanego limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="ebbc4-508">StartAsync i StopAsync</span><span class="sxs-lookup"><span data-stu-id="ebbc4-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="ebbc4-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="ebbc4-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>kończy działanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="ebbc4-511">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="ebbc4-511">WaitForShutdown</span></span>

<span data-ttu-id="ebbc4-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>jest wyzwalany za pośrednictwem, na przykład <xref:Microsoft.Extensions.Hosting.IHostLifetime> `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (nasłuchuje dla <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="ebbc4-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>wywołania <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="ebbc4-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="ebbc4-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="ebbc4-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>Zwraca wartość <xref:System.Threading.Tasks.Task> , która kończy się, gdy zamknięcie jest wyzwalane za pośrednictwem danego tokenu i wywołań <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="ebbc4-516">Zewnętrzna kontrola</span><span class="sxs-lookup"><span data-stu-id="ebbc4-516">External control</span></span>

<span data-ttu-id="ebbc4-517">Kontrolę zewnętrzną hosta można osiągnąć przy użyciu metod, które mogą być wywoływane zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-517">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="ebbc4-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na początku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> , który czeka na zakończenie przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="ebbc4-519">Może to służyć do opóźnienia uruchamiania do momentu zasygnalizowania zdarzenia zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="ebbc4-520">IHostingEnvironment, interfejs</span><span class="sxs-lookup"><span data-stu-id="ebbc4-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="ebbc4-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>zawiera informacje o środowisku hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="ebbc4-522">Użyj [iniekcji konstruktorów](xref:fundamentals/dependency-injection) , aby uzyskać <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> w celu użycia jej właściwości i metod rozszerzających:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="ebbc4-523">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="ebbc4-524">IApplicationLifetime, interfejs</span><span class="sxs-lookup"><span data-stu-id="ebbc4-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="ebbc4-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>zezwala na działania po uruchomieniu i zamknięcia, w tym bezpieczne żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="ebbc4-526">Trzy właściwości interfejsu są tokenami anulowania używanymi do rejestrowania <xref:System.Action> metod, które definiują zdarzenia uruchamiania i zamykania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="ebbc4-527">Token anulowania</span><span class="sxs-lookup"><span data-stu-id="ebbc4-527">Cancellation Token</span></span> | <span data-ttu-id="ebbc4-528">Wyzwalane, gdy&#8230;</span><span class="sxs-lookup"><span data-stu-id="ebbc4-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="ebbc4-529">Host został w pełni uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="ebbc4-530">Host kończy bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="ebbc4-531">Wszystkie żądania powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-531">All requests should be processed.</span></span> <span data-ttu-id="ebbc4-532">Bloki zamknięcia do momentu zakończenia tego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="ebbc4-533">Host wykonuje bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="ebbc4-534">Żądania mogą nadal być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-534">Requests may still be processing.</span></span> <span data-ttu-id="ebbc4-535">Bloki zamknięcia do momentu zakończenia tego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="ebbc4-536">Konstruktor — wstrzyknięcie <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> usługi do dowolnej klasy.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="ebbc4-537">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) używa iniekcji konstruktora do `LifetimeEventsHostedService` klasy ( <xref:Microsoft.Extensions.Hosting.IHostedService> implementacji), aby zarejestrować zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="ebbc4-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="ebbc4-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>żąda zakończenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="ebbc4-540">Następująca Klasa służy <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> do bezpiecznego zamykania aplikacji w przypadku `Shutdown` wywołania metody klasy:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

<span data-ttu-id="ebbc4-541">Szablony ASP.NET Core tworzą hosta ogólnego platformy .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="ebbc4-542">Definicja hosta</span><span class="sxs-lookup"><span data-stu-id="ebbc4-542">Host definition</span></span>

<span data-ttu-id="ebbc4-543">*Host* to obiekt, który hermetyzuje zasoby aplikacji, takie jak:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="ebbc4-544">Iniekcja zależności (DI)</span><span class="sxs-lookup"><span data-stu-id="ebbc4-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="ebbc4-545">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="ebbc4-545">Logging</span></span>
* <span data-ttu-id="ebbc4-546">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="ebbc4-546">Configuration</span></span>
* <span data-ttu-id="ebbc4-547">`IHostedService`metod</span><span class="sxs-lookup"><span data-stu-id="ebbc4-547">`IHostedService` implementations</span></span>

<span data-ttu-id="ebbc4-548">Po uruchomieniu hosta wywołuje on `IHostedService.StartAsync` każdą implementację <xref:Microsoft.Extensions.Hosting.IHostedService> , która znajduje się w kontenerze di.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-548">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="ebbc4-549">W aplikacji sieci Web jedną z `IHostedService` implementacji jest usługa sieci Web, która uruchamia [implementację serwera http](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="ebbc4-550">Główną przyczyną uwzględnienia wszystkich zasobów zależnych od aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: Kontrola uruchamiania aplikacji i bezpieczne zamykanie.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="ebbc4-551">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="ebbc4-551">Set up a host</span></span>

<span data-ttu-id="ebbc4-552">Host jest zazwyczaj konfigurowany, zbudowany i uruchamiany przez kod w `Program` klasie.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="ebbc4-553">`Main`Metoda:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-553">The `Main` method:</span></span>

* <span data-ttu-id="ebbc4-554">Wywołuje `CreateHostBuilder` metodę, aby utworzyć i skonfigurować obiekt konstruktora.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="ebbc4-555">Wywołania `Build` i `Run` metody w obiekcie konstruktora.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="ebbc4-556">Szablony sieci Web ASP.NET Core generują następujący kod w celu utworzenia hosta:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="ebbc4-557">Poniższy kod tworzy obciążenie inne niż HTTP z `IHostedService` implementacją dodaną do kontenera di.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="ebbc4-558">W przypadku obciążeń HTTP `Main` Metoda jest taka sama, ale `CreateHostBuilder` wywołuje `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="ebbc4-559">Jeśli aplikacja używa Entity Framework Core, nie zmieniaj nazwy ani podpisu `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="ebbc4-560">[Narzędzia Entity Framework Core](/ef/core/miscellaneous/cli/) oczekują na znalezienie `CreateHostBuilder` metody, która konfiguruje hosta bez uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="ebbc4-561">Aby uzyskać więcej informacji, zobacz [Tworzenie DbContext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="ebbc4-562">Ustawienia domyślnego konstruktora</span><span class="sxs-lookup"><span data-stu-id="ebbc4-562">Default builder settings</span></span>

<span data-ttu-id="ebbc4-563"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="ebbc4-564">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na ścieżkę zwracaną przez <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="ebbc4-565">Ładuje konfigurację hosta z:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="ebbc4-566">Zmienne środowiskowe poprzedzone prefiksem `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="ebbc4-567">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-567">Command-line arguments.</span></span>
* <span data-ttu-id="ebbc4-568">Ładuje konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="ebbc4-569">*appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="ebbc4-570">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="ebbc4-571">[Secret Manager](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="ebbc4-572">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-572">Environment variables.</span></span>
  * <span data-ttu-id="ebbc4-573">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-573">Command-line arguments.</span></span>
* <span data-ttu-id="ebbc4-574">Dodaje następujących dostawców [rejestrowania](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="ebbc4-575">Konsola</span><span class="sxs-lookup"><span data-stu-id="ebbc4-575">Console</span></span>
  * <span data-ttu-id="ebbc4-576">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="ebbc4-576">Debug</span></span>
  * <span data-ttu-id="ebbc4-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="ebbc4-577">EventSource</span></span>
  * <span data-ttu-id="ebbc4-578">EventLog (tylko w przypadku uruchamiania w systemie Windows)</span><span class="sxs-lookup"><span data-stu-id="ebbc4-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="ebbc4-579">Umożliwia [weryfikację zakresu](xref:fundamentals/dependency-injection#scope-validation) i [Sprawdzanie poprawności zależności](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) , gdy środowisko jest opracowywane.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="ebbc4-580">`ConfigureWebHostDefaults`Metoda:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="ebbc4-581">Ładuje konfigurację hosta ze zmiennych środowiskowych, które są poprzedzone prefiksem `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="ebbc4-582">Ustawia serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci Web i konfiguruje go przy użyciu dostawców konfiguracji hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="ebbc4-583">Aby poznać domyślne opcje serwera Kestrel, zobacz <xref:fundamentals/servers/kestrel#kestrel-options> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="ebbc4-584">Dodaje [oprogramowanie pośredniczące do filtrowania hosta](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="ebbc4-585">Dodaje [przekazane nagłówki — oprogramowanie pośredniczące](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest równe `true` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="ebbc4-586">Włącza integrację usług IIS.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-586">Enables IIS integration.</span></span> <span data-ttu-id="ebbc4-587">Aby poznać domyślne opcje usług IIS, zobacz <xref:host-and-deploy/iis/index#iis-options> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="ebbc4-588">[Ustawienia dla wszystkich typów aplikacji](#settings-for-all-app-types) i [Ustawienia dla usługi Web Apps](#settings-for-web-apps) w dalszej części tego artykułu pokazują, jak zastąpić ustawienia domyślnego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="ebbc4-589">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="ebbc4-589">Framework-provided services</span></span>

<span data-ttu-id="ebbc4-590">Następujące usługi są rejestrowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="ebbc4-591">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ebbc4-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="ebbc4-592">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="ebbc4-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="ebbc4-593">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="ebbc4-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="ebbc4-594">Aby uzyskać więcej informacji na temat usług udostępnianych przez platformę, zobacz <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="ebbc4-595">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="ebbc4-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="ebbc4-596">Wstrzyknąć <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> usługę (dawniej `IApplicationLifetime` ) do dowolnej klasy w celu obsługi zadań po uruchomieniu i bezpiecznego zamykania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="ebbc4-597">Trzy właściwości interfejsu to tokeny anulowania używane do rejestrowania metod obsługi zdarzeń uruchamiania i zatrzymywania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="ebbc4-598">Interfejs zawiera również `StopApplication` metodę.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="ebbc4-599">Poniższy przykład to `IHostedService` implementacja, która rejestruje `IHostApplicationLifetime` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="ebbc4-600">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="ebbc4-600">IHostLifetime</span></span>

<span data-ttu-id="ebbc4-601"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Implementacja kontroluje moment uruchomienia hosta i jego zatrzymania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="ebbc4-602">Używana jest Ostatnia zarejestrowana implementacja.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-602">The last implementation registered is used.</span></span>

<span data-ttu-id="ebbc4-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`jest `IHostLifetime` implementacją domyślną.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="ebbc4-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="ebbc4-605">Nasłuchuje <kbd>kombinacji klawiszy CTRL</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM oraz wywołań, <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> Aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="ebbc4-606">Odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="ebbc4-607">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="ebbc4-607">IHostEnvironment</span></span>

<span data-ttu-id="ebbc4-608">Wsuń <xref:Microsoft.Extensions.Hosting.IHostEnvironment> usługę do klasy, aby uzyskać informacje o następujących ustawieniach:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="ebbc4-609">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="ebbc4-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="ebbc4-610">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="ebbc4-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="ebbc4-611">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="ebbc4-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="ebbc4-612">Aplikacje sieci Web implementują `IWebHostEnvironment` interfejs, który dziedziczy `IHostEnvironment` i dodaje [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="ebbc4-613">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="ebbc4-613">Host configuration</span></span>

<span data-ttu-id="ebbc4-614">Konfiguracja hosta jest używana we właściwościach <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="ebbc4-615">Konfiguracja hosta jest dostępna w [HostBuilderContext.Configwersja](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="ebbc4-616">Po `ConfigureAppConfiguration` program `HostBuilderContext.Configuration` zostanie zastąpiony konfiguracją aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="ebbc4-617">Aby dodać konfigurację hosta, wywołaj polecenie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="ebbc4-618">`ConfigureHostConfiguration`może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="ebbc4-619">Host używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="ebbc4-620">Dostawca zmiennych środowiskowych z prefiksami `DOTNET_` i argumentami wiersza polecenia są uwzględniane przez `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ebbc4-621">W przypadku aplikacji sieci Web jest dodawany dostawca zmiennych środowiskowych z prefiksem `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="ebbc4-622">Prefiks jest usuwany, gdy są odczytywane zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="ebbc4-623">Na przykład wartość zmiennej środowiskowej jest `ASPNETCORE_ENVIRONMENT` wartością konfiguracji hosta dla `environment` klucza.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="ebbc4-624">Poniższy przykład umożliwia utworzenie konfiguracji hosta:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="ebbc4-625">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="ebbc4-625">App configuration</span></span>

<span data-ttu-id="ebbc4-626">Konfiguracja aplikacji jest tworzona przez wywołanie metody <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="ebbc4-627">`ConfigureAppConfiguration`może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="ebbc4-628">Aplikacja używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="ebbc4-629">Konfiguracja utworzona przez program `ConfigureAppConfiguration` jest dostępna w [HostBuilderContext.Configwersja](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) na potrzeby kolejnych operacji i jako usługa z di.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="ebbc4-630">Konfiguracja hosta jest również dodawana do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="ebbc4-631">Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="ebbc4-632">Ustawienia dla wszystkich typów aplikacji</span><span class="sxs-lookup"><span data-stu-id="ebbc4-632">Settings for all app types</span></span>

<span data-ttu-id="ebbc4-633">Ta sekcja zawiera listę ustawień hosta, które dotyczą zarówno obciążeń HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="ebbc4-634">Domyślnie zmienne środowiskowe używane do konfigurowania tych ustawień mogą mieć `DOTNET_` `ASPNETCORE_` prefiks lub.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="ebbc4-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="ebbc4-635">ApplicationName</span></span>

<span data-ttu-id="ebbc4-636">Właściwość [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) jest ustawiana na podstawie konfiguracji hosta podczas konstruowania hosta.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="ebbc4-637">**Klucz**:`applicationName`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="ebbc4-638">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-638">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-639">**Wartość domyślna**: Nazwa zestawu, który zawiera punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="ebbc4-640">**Zmienna środowiskowa**:`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="ebbc4-641">Aby ustawić tę wartość, należy użyć zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="ebbc4-642">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="ebbc4-642">ContentRoot</span></span>

<span data-ttu-id="ebbc4-643">Właściwość [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="ebbc4-644">Jeśli ścieżka nie istnieje, uruchomienie hosta nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="ebbc4-645">**Klucz**:`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="ebbc4-646">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-646">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-647">**Domyślnie**: folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="ebbc4-648">**Zmienna środowiskowa**:`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="ebbc4-649">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseContentRoot` `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="ebbc4-650">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-650">For more information, see:</span></span>

* [<span data-ttu-id="ebbc4-651">Podstawy: zawartość główna</span><span class="sxs-lookup"><span data-stu-id="ebbc4-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="ebbc4-652">WebRoot</span><span class="sxs-lookup"><span data-stu-id="ebbc4-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="ebbc4-653">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="ebbc4-653">EnvironmentName</span></span>

<span data-ttu-id="ebbc4-654">Dla właściwości [IHostEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) można ustawić dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="ebbc4-655">Wartości zdefiniowane przez platformę obejmują `Development` , `Staging` , i `Production` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="ebbc4-656">W wartościach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="ebbc4-657">**Klucz**:`environment`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-657">**Key**: `environment`</span></span>  
<span data-ttu-id="ebbc4-658">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-658">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-659">**Wartość domyślna**:`Production`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-659">**Default**: `Production`</span></span>  
<span data-ttu-id="ebbc4-660">**Zmienna środowiskowa**:`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="ebbc4-661">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseEnvironment` `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="ebbc4-662">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="ebbc4-662">ShutdownTimeout</span></span>

<span data-ttu-id="ebbc4-663">[HostOptions. shutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) ustawia limit czasu dla <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="ebbc4-664">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-664">The default value is five seconds.</span></span>  <span data-ttu-id="ebbc4-665">Podczas okresu przekroczenia limitu czasu Host:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="ebbc4-666">Wyzwala [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="ebbc4-667">Próbuje zatrzymać usługi hostowane, rejestrowanie błędów dla usług, których zatrzymanie nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="ebbc4-668">Jeśli limit czasu upłynie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="ebbc4-669">Usługi są zatrzymane nawet wtedy, gdy nie zakończyły przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="ebbc4-670">Jeśli usługi wymagają dodatkowego czasu na zatrzymanie, zwiększ limit czasu.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="ebbc4-671">**Klucz**:`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="ebbc4-672">**Typ**:`int`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-672">**Type**: `int`</span></span>  
<span data-ttu-id="ebbc4-673">**Wartość domyślna**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="ebbc4-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="ebbc4-674">**Zmienna środowiskowa**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="ebbc4-675">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub skonfiguruj `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="ebbc4-676">Poniższy przykład ustawia limit czasu na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="ebbc4-677">Wyłącz ponowne ładowanie konfiguracji aplikacji przy zmianie</span><span class="sxs-lookup"><span data-stu-id="ebbc4-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="ebbc4-678">[Domyślnie](xref:fundamentals/configuration/index#default) *appsettings.json* i *appSettings. { Środowisko}. kod JSON* jest ponownie ładowany, gdy plik ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="ebbc4-679">Aby wyłączyć to zachowanie ponownego ładowania w ASP.NET Core 5,0 w wersji zapoznawczej 3 lub nowszej, ustaw `hostBuilder:reloadConfigOnChange` klucz na `false` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="ebbc4-680">**Klucz**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="ebbc4-681">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="ebbc4-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebbc4-682">**Wartość domyślna**:`true`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-682">**Default**: `true`</span></span>  
<span data-ttu-id="ebbc4-683">**Argument wiersza polecenia**:`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="ebbc4-684">**Zmienna środowiskowa**:`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="ebbc4-685">Separator dwukropek ( `:` ) nie działa ze zmiennymi kluczy hierarchicznych na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="ebbc4-686">Aby uzyskać więcej informacji, zobacz [zmienne środowiskowe](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="ebbc4-687">Ustawienia usługi Web Apps</span><span class="sxs-lookup"><span data-stu-id="ebbc4-687">Settings for web apps</span></span>

<span data-ttu-id="ebbc4-688">Niektóre ustawienia hosta mają zastosowanie tylko do obciążeń protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="ebbc4-689">Domyślnie zmienne środowiskowe używane do konfigurowania tych ustawień mogą mieć `DOTNET_` `ASPNETCORE_` prefiks lub.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="ebbc4-690">Metody rozszerzenia dla `IWebHostBuilder` programu są dostępne dla tych ustawień.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="ebbc4-691">Przykłady kodu, które pokazują, jak wywołać metody rozszerzane `webBuilder` jest wystąpieniem `IWebHostBuilder` , tak jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="ebbc4-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="ebbc4-692">CaptureStartupErrors</span></span>

<span data-ttu-id="ebbc4-693">Gdy `false` , błędy podczas uruchamiania, kończy się hostem.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="ebbc4-694">Gdy `true` host przechwytuje wyjątki podczas uruchamiania, a następnie próbuje uruchomić serwer.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="ebbc4-695">**Klucz**:`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="ebbc4-696">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="ebbc4-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebbc4-697">**Domyślnie**: wartość domyślna to, `false` chyba że aplikacja działa z Kestrel za usługami IIS, gdzie wartość domyślna to `true` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="ebbc4-698">**Zmienna środowiskowa**:`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="ebbc4-699">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="ebbc4-700">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="ebbc4-700">DetailedErrors</span></span>

<span data-ttu-id="ebbc4-701">Po włączeniu lub gdy środowisko jest `Development` , aplikacja przechwytuje szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="ebbc4-702">**Klucz**:`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="ebbc4-703">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="ebbc4-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebbc4-704">**Wartość domyślna**:`false`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-704">**Default**: `false`</span></span>  
<span data-ttu-id="ebbc4-705">**Zmienna środowiskowa**:`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="ebbc4-706">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="ebbc4-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="ebbc4-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="ebbc4-708">Rozdzielany średnikami ciąg początkowych zestawów startowych do załadowania podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="ebbc4-709">Mimo że wartość konfiguracji jest domyślnie pustym ciągiem, zestaw startowy obsługujący zawsze zawiera zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="ebbc4-710">W przypadku udostępniania zestawów startowych są one dodawane do zestawu aplikacji do załadowania, gdy aplikacja kompiluje swoje popularne usługi podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="ebbc4-711">**Klucz**:`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="ebbc4-712">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-712">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-713">**Wartość domyślna**: pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="ebbc4-713">**Default**: Empty string</span></span>  
<span data-ttu-id="ebbc4-714">**Zmienna środowiskowa**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="ebbc4-715">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="ebbc4-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="ebbc4-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="ebbc4-717">Rozdzielany średnikami ciąg początkowych zestawów uruchamiania, który ma zostać wykluczony podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="ebbc4-718">**Klucz**:`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="ebbc4-719">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-719">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-720">**Wartość domyślna**: pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="ebbc4-720">**Default**: Empty string</span></span>  
<span data-ttu-id="ebbc4-721">**Zmienna środowiskowa**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="ebbc4-722">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="ebbc4-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="ebbc4-723">HTTPS_Port</span></span>

<span data-ttu-id="ebbc4-724">Port przekierowania protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-724">The HTTPS redirect port.</span></span> <span data-ttu-id="ebbc4-725">Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ebbc4-726">**Klucz**:`https_port`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="ebbc4-727">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-727">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-728">Wartość **Domyślna**: nie ustawiono wartości domyślnej.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="ebbc4-729">**Zmienna środowiskowa**:`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="ebbc4-730">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="ebbc4-731">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="ebbc4-731">PreferHostingUrls</span></span>

<span data-ttu-id="ebbc4-732">Wskazuje, czy host powinien nasłuchiwać adresów URL skonfigurowanych przy użyciu `IWebHostBuilder` zamiast adresów URL skonfigurowanych dla `IServer` implementacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="ebbc4-733">**Klucz**:`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="ebbc4-734">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="ebbc4-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebbc4-735">**Wartość domyślna**:`true`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-735">**Default**: `true`</span></span>  
<span data-ttu-id="ebbc4-736">**Zmienna środowiskowa**:`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="ebbc4-737">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="ebbc4-738">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="ebbc4-738">PreventHostingStartup</span></span>

<span data-ttu-id="ebbc4-739">Zapobiega automatycznemu ładowaniu zestawów startowych hostingu, w tym hostingu zestawów startowych skonfigurowanych przez zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="ebbc4-740">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="ebbc4-741">**Klucz**:`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="ebbc4-742">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="ebbc4-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="ebbc4-743">**Wartość domyślna**:`false`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-743">**Default**: `false`</span></span>  
<span data-ttu-id="ebbc4-744">**Zmienna środowiskowa**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="ebbc4-745">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="ebbc4-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="ebbc4-746">StartupAssembly</span></span>

<span data-ttu-id="ebbc4-747">Zestaw do wyszukiwania `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="ebbc4-748">**Klucz**:`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="ebbc4-749">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-749">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-750">**Domyślnie**: zestaw aplikacji</span><span class="sxs-lookup"><span data-stu-id="ebbc4-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="ebbc4-751">**Zmienna środowiskowa**:`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="ebbc4-752">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołania `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="ebbc4-753">`UseStartup`może przyjmować nazwę zestawu ( `string` ) lub typ ( `TStartup` ).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="ebbc4-754">Jeśli `UseStartup` wywoływana jest wiele metod, pierwszeństwo ma Ostatnia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="ebbc4-755">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="ebbc4-755">URLs</span></span>

<span data-ttu-id="ebbc4-756">Rozdzielana średnikami lista adresów IP lub adresów hostów z portami i protokołami, na których serwer powinien nasłuchiwać żądań.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="ebbc4-757">Na przykład `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="ebbc4-758">Użyj " \* ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwie hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000` ).</span><span class="sxs-lookup"><span data-stu-id="ebbc4-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="ebbc4-759">Protokół ( `http://` lub `https://` ) musi być dołączony do każdego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="ebbc4-760">Obsługiwane formaty różnią się między serwerami.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="ebbc4-761">**Klucz**:`urls`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-761">**Key**: `urls`</span></span>  
<span data-ttu-id="ebbc4-762">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-762">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-763">**Wartość domyślna**: `http://localhost:5000` i`https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="ebbc4-764">**Zmienna środowiskowa**:`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="ebbc4-765">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="ebbc4-766">Kestrel ma własny interfejs API konfiguracji punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="ebbc4-767">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="ebbc4-768">WebRoot</span><span class="sxs-lookup"><span data-stu-id="ebbc4-768">WebRoot</span></span>

<span data-ttu-id="ebbc4-769">Właściwość [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) określa ścieżkę względną do statycznych zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="ebbc4-770">Jeśli ścieżka nie istnieje, jest używany dostawca plików No-op.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="ebbc4-771">**Klucz**:`webroot`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="ebbc4-772">**Typ**:`string`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-772">**Type**: `string`</span></span>  
<span data-ttu-id="ebbc4-773">**Wartość domyślna**: wartość domyślna to `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="ebbc4-774">Ścieżka do *elementu {content root}/wwwroot* musi istnieć.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="ebbc4-775">**Zmienna środowiskowa**:`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="ebbc4-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="ebbc4-776">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseWebRoot` `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="ebbc4-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="ebbc4-777">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-777">For more information, see:</span></span>

* [<span data-ttu-id="ebbc4-778">Podstawy: katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="ebbc4-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="ebbc4-779">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="ebbc4-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="ebbc4-780">Zarządzanie okresem istnienia hosta</span><span class="sxs-lookup"><span data-stu-id="ebbc4-780">Manage the host lifetime</span></span>

<span data-ttu-id="ebbc4-781">Wywołaj metody z skompilowanej <xref:Microsoft.Extensions.Hosting.IHost> implementacji, aby uruchomić i zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="ebbc4-782">Te metody mają wpływ na wszystkie <xref:Microsoft.Extensions.Hosting.IHostedService> implementacje zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="ebbc4-783">Uruchom</span><span class="sxs-lookup"><span data-stu-id="ebbc4-783">Run</span></span>

<span data-ttu-id="ebbc4-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uruchamia aplikację i blokuje wątek wywołujący do momentu wyłączenia hosta.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="ebbc4-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="ebbc4-785">RunAsync</span></span>

<span data-ttu-id="ebbc4-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uruchamia aplikację i zwraca <xref:System.Threading.Tasks.Task> , która kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="ebbc4-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="ebbc4-787">RunConsoleAsync</span></span>

<span data-ttu-id="ebbc4-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>włącza obsługę konsoli, kompiluje i uruchamia hosta, a następnie czeka na zamknięcie programu <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="ebbc4-789">Rozpocznij</span><span class="sxs-lookup"><span data-stu-id="ebbc4-789">Start</span></span>

<span data-ttu-id="ebbc4-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="ebbc4-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="ebbc4-791">StartAsync</span></span>

<span data-ttu-id="ebbc4-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uruchamia hosta i zwraca <xref:System.Threading.Tasks.Task> , który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="ebbc4-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>jest wywoływana na początku `StartAsync` , który czeka na zakończenie przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="ebbc4-794">Może to służyć do opóźnienia uruchamiania do momentu zasygnalizowania zdarzenia zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="ebbc4-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="ebbc4-795">StopAsync</span></span>

<span data-ttu-id="ebbc4-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>próbuje zatrzymać hosta w ramach podanego limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="ebbc4-797">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="ebbc4-797">WaitForShutdown</span></span>

<span data-ttu-id="ebbc4-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje wątek wywołujący do momentu wyzwolenia przez IHostLifetime, na przykład przez <kbd>naciśnięcie klawiszy CTRL</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="ebbc4-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="ebbc4-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="ebbc4-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="ebbc4-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>Zwraca wartość <xref:System.Threading.Tasks.Task> , która kończy się, gdy zamknięcie jest wyzwalane za pośrednictwem danego tokenu i wywołań <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="ebbc4-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="ebbc4-801">Zewnętrzna kontrola</span><span class="sxs-lookup"><span data-stu-id="ebbc4-801">External control</span></span>

<span data-ttu-id="ebbc4-802">Bezpośrednią kontrolę nad okresem istnienia hosta można uzyskać przy użyciu metod, które mogą być wywoływane zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="ebbc4-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="ebbc4-803">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="ebbc4-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
