---
title: Host ogólny .NET w ASP.NET Core
author: rick-anderson
description: Użyj hosta ogólnego platformy .NET Core w aplikacjach ASP.NET Core.  Host ogólny jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: 263c7713166005dfdec8ede6bfa9b03b730dede7
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035817"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="59419-104">Host ogólny .NET w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59419-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="59419-105">Szablony ASP.NET Core tworzą hosta ogólnego platformy .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="59419-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="59419-106">Ten temat zawiera informacje dotyczące korzystania z hosta ogólnego platformy .NET w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="59419-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="59419-107">Aby uzyskać informacje na temat korzystania z hosta ogólnego platformy .NET w aplikacjach konsolowych, zobacz [host ogólny programu .NET](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="59419-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="59419-108">Definicja hosta</span><span class="sxs-lookup"><span data-stu-id="59419-108">Host definition</span></span>

<span data-ttu-id="59419-109">*Host* to obiekt, który hermetyzuje zasoby aplikacji, takie jak:</span><span class="sxs-lookup"><span data-stu-id="59419-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="59419-110">Iniekcja zależności (DI)</span><span class="sxs-lookup"><span data-stu-id="59419-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="59419-111">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="59419-111">Logging</span></span>
* <span data-ttu-id="59419-112">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="59419-112">Configuration</span></span>
* <span data-ttu-id="59419-113">`IHostedService` metod</span><span class="sxs-lookup"><span data-stu-id="59419-113">`IHostedService` implementations</span></span>

<span data-ttu-id="59419-114">Po uruchomieniu hosta jest on wywoływany <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> dla każdej implementacji <xref:Microsoft.Extensions.Hosting.IHostedService> zarejestrowanej w kolekcji kontenera usługi hostowanej usług.</span><span class="sxs-lookup"><span data-stu-id="59419-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="59419-115">W aplikacji sieci Web jedną z `IHostedService` implementacji jest usługa sieci Web, która uruchamia [implementację serwera http](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="59419-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="59419-116">Główną przyczyną uwzględnienia wszystkich zasobów zależnych od aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: Kontrola uruchamiania aplikacji i bezpieczne zamykanie.</span><span class="sxs-lookup"><span data-stu-id="59419-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="59419-117">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="59419-117">Set up a host</span></span>

<span data-ttu-id="59419-118">Host jest zazwyczaj konfigurowany, zbudowany i uruchamiany przez kod w `Program` klasie.</span><span class="sxs-lookup"><span data-stu-id="59419-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="59419-119">`Main`Metoda:</span><span class="sxs-lookup"><span data-stu-id="59419-119">The `Main` method:</span></span>

* <span data-ttu-id="59419-120">Wywołuje `CreateHostBuilder` metodę, aby utworzyć i skonfigurować obiekt konstruktora.</span><span class="sxs-lookup"><span data-stu-id="59419-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="59419-121">Wywołania `Build` i `Run` metody w obiekcie konstruktora.</span><span class="sxs-lookup"><span data-stu-id="59419-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="59419-122">Szablony sieci Web ASP.NET Core generują następujący kod w celu utworzenia hosta:</span><span class="sxs-lookup"><span data-stu-id="59419-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="59419-123">Poniższy kod tworzy obciążenie inne niż HTTP z `IHostedService` implementacją dodaną do kontenera di.</span><span class="sxs-lookup"><span data-stu-id="59419-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="59419-124">W przypadku obciążeń HTTP `Main` Metoda jest taka sama, ale `CreateHostBuilder` wywołuje `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="59419-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="59419-125">Jeśli aplikacja używa Entity Framework Core, nie zmieniaj nazwy ani podpisu `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="59419-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="59419-126">[Narzędzia Entity Framework Core](/ef/core/miscellaneous/cli/) oczekują na znalezienie `CreateHostBuilder` metody, która konfiguruje hosta bez uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="59419-127">Aby uzyskać więcej informacji, zobacz [Tworzenie DbContext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="59419-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="59419-128">Ustawienia domyślnego konstruktora</span><span class="sxs-lookup"><span data-stu-id="59419-128">Default builder settings</span></span>

<span data-ttu-id="59419-129"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="59419-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="59419-130">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na ścieżkę zwracaną przez <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="59419-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="59419-131">Ładuje konfigurację hosta z:</span><span class="sxs-lookup"><span data-stu-id="59419-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="59419-132">Zmienne środowiskowe poprzedzone prefiksem `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="59419-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="59419-133">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="59419-133">Command-line arguments.</span></span>
* <span data-ttu-id="59419-134">Ładuje konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="59419-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="59419-135">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="59419-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="59419-136">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="59419-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="59419-137">Wpisy [tajne użytkownika](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku.</span><span class="sxs-lookup"><span data-stu-id="59419-137">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="59419-138">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="59419-138">Environment variables.</span></span>
  * <span data-ttu-id="59419-139">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="59419-139">Command-line arguments.</span></span>
* <span data-ttu-id="59419-140">Dodaje następujących dostawców [rejestrowania](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="59419-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="59419-141">Konsola</span><span class="sxs-lookup"><span data-stu-id="59419-141">Console</span></span>
  * <span data-ttu-id="59419-142">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="59419-142">Debug</span></span>
  * <span data-ttu-id="59419-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="59419-143">EventSource</span></span>
  * <span data-ttu-id="59419-144">EventLog (tylko w przypadku uruchamiania w systemie Windows)</span><span class="sxs-lookup"><span data-stu-id="59419-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="59419-145">Umożliwia [weryfikację zakresu](xref:fundamentals/dependency-injection#scope-validation) i [Sprawdzanie poprawności zależności](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) , gdy środowisko jest opracowywane.</span><span class="sxs-lookup"><span data-stu-id="59419-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="59419-146"><xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="59419-146">The <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method:</span></span>

* <span data-ttu-id="59419-147">Ładuje konfigurację hosta ze zmiennych środowiskowych, które są poprzedzone prefiksem `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="59419-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="59419-148">Ustawia serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci Web i konfiguruje go przy użyciu dostawców konfiguracji hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="59419-149">Aby poznać domyślne opcje serwera Kestrel, zobacz <xref:fundamentals/servers/kestrel#kestrel-options> .</span><span class="sxs-lookup"><span data-stu-id="59419-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="59419-150">Dodaje [oprogramowanie pośredniczące do filtrowania hosta](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="59419-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="59419-151">Dodaje [przekazane nagłówki — oprogramowanie pośredniczące](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest równe `true` .</span><span class="sxs-lookup"><span data-stu-id="59419-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="59419-152">Włącza integrację usług IIS.</span><span class="sxs-lookup"><span data-stu-id="59419-152">Enables IIS integration.</span></span> <span data-ttu-id="59419-153">Aby poznać domyślne opcje usług IIS, zobacz <xref:host-and-deploy/iis/index#iis-options> .</span><span class="sxs-lookup"><span data-stu-id="59419-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="59419-154">[Ustawienia dla wszystkich typów aplikacji](#settings-for-all-app-types) i [Ustawienia dla usługi Web Apps](#settings-for-web-apps) w dalszej części tego artykułu pokazują, jak zastąpić ustawienia domyślnego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="59419-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="59419-155">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="59419-155">Framework-provided services</span></span>

<span data-ttu-id="59419-156">Następujące usługi są rejestrowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="59419-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="59419-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="59419-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="59419-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="59419-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="59419-159">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="59419-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="59419-160">Aby uzyskać więcej informacji na temat usług udostępnianych przez platformę, zobacz <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="59419-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="59419-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="59419-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="59419-162">Wstrzyknąć <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> usługę (dawniej `IApplicationLifetime` ) do dowolnej klasy w celu obsługi zadań po uruchomieniu i bezpiecznego zamykania.</span><span class="sxs-lookup"><span data-stu-id="59419-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="59419-163">Trzy właściwości interfejsu to tokeny anulowania używane do rejestrowania metod obsługi zdarzeń uruchamiania i zatrzymywania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="59419-164">Interfejs zawiera również `StopApplication` metodę.</span><span class="sxs-lookup"><span data-stu-id="59419-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="59419-165">Poniższy przykład to `IHostedService` implementacja, która rejestruje `IHostApplicationLifetime` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="59419-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="59419-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="59419-166">IHostLifetime</span></span>

<span data-ttu-id="59419-167"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Implementacja kontroluje moment uruchomienia hosta i jego zatrzymania.</span><span class="sxs-lookup"><span data-stu-id="59419-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="59419-168">Używana jest Ostatnia zarejestrowana implementacja.</span><span class="sxs-lookup"><span data-stu-id="59419-168">The last implementation registered is used.</span></span>

<span data-ttu-id="59419-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` jest `IHostLifetime` implementacją domyślną.</span><span class="sxs-lookup"><span data-stu-id="59419-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="59419-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="59419-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="59419-171">Nasłuchuje <kbd>kombinacji klawiszy CTRL</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM oraz wywołań, <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> Aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="59419-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="59419-172">Odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="59419-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="59419-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="59419-173">IHostEnvironment</span></span>

<span data-ttu-id="59419-174">Wsuń <xref:Microsoft.Extensions.Hosting.IHostEnvironment> usługę do klasy, aby uzyskać informacje o następujących ustawieniach:</span><span class="sxs-lookup"><span data-stu-id="59419-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="59419-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="59419-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="59419-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="59419-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="59419-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="59419-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="59419-178">Aplikacje sieci Web implementują `IWebHostEnvironment` interfejs, który dziedziczy `IHostEnvironment` i dodaje [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="59419-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="59419-179">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="59419-179">Host configuration</span></span>

<span data-ttu-id="59419-180">Konfiguracja hosta jest używana we właściwościach <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementacji.</span><span class="sxs-lookup"><span data-stu-id="59419-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="59419-181">Konfiguracja hosta jest dostępna w [HostBuilderContext.Configwersja](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="59419-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="59419-182">Po `ConfigureAppConfiguration` program `HostBuilderContext.Configuration` zostanie zastąpiony konfiguracją aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="59419-183">Aby dodać konfigurację hosta, wywołaj polecenie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="59419-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="59419-184">`ConfigureHostConfiguration` może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="59419-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="59419-185">Host używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="59419-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="59419-186">Dostawca zmiennych środowiskowych z prefiksami `DOTNET_` i argumentami wiersza polecenia są uwzględniane przez `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="59419-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="59419-187">W przypadku aplikacji sieci Web jest dodawany dostawca zmiennych środowiskowych z prefiksem `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="59419-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="59419-188">Prefiks jest usuwany, gdy są odczytywane zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="59419-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="59419-189">Na przykład wartość zmiennej środowiskowej jest `ASPNETCORE_ENVIRONMENT` wartością konfiguracji hosta dla `environment` klucza.</span><span class="sxs-lookup"><span data-stu-id="59419-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="59419-190">Poniższy przykład umożliwia utworzenie konfiguracji hosta:</span><span class="sxs-lookup"><span data-stu-id="59419-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="59419-191">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="59419-191">App configuration</span></span>

<span data-ttu-id="59419-192">Konfiguracja aplikacji jest tworzona przez wywołanie metody <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="59419-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="59419-193">`ConfigureAppConfiguration` może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="59419-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="59419-194">Aplikacja używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="59419-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="59419-195">Konfiguracja utworzona przez program `ConfigureAppConfiguration` jest dostępna w [HostBuilderContext.Configwersja](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) na potrzeby kolejnych operacji i jako usługa z di.</span><span class="sxs-lookup"><span data-stu-id="59419-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="59419-196">Konfiguracja hosta jest również dodawana do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="59419-197">Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="59419-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="59419-198">Ustawienia dla wszystkich typów aplikacji</span><span class="sxs-lookup"><span data-stu-id="59419-198">Settings for all app types</span></span>

<span data-ttu-id="59419-199">Ta sekcja zawiera listę ustawień hosta, które dotyczą zarówno obciążeń HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="59419-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="59419-200">Domyślnie zmienne środowiskowe używane do konfigurowania tych ustawień mogą mieć `DOTNET_` `ASPNETCORE_` prefiks lub.</span><span class="sxs-lookup"><span data-stu-id="59419-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="59419-201">Aby uzyskać więcej informacji, zobacz sekcję [domyślne ustawienia konstruktora](#default-builder-settings) .</span><span class="sxs-lookup"><span data-stu-id="59419-201">For more information, see the [Default builder settings](#default-builder-settings) section.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="59419-202">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="59419-202">ApplicationName</span></span>

<span data-ttu-id="59419-203">Właściwość [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) jest ustawiana na podstawie konfiguracji hosta podczas konstruowania hosta.</span><span class="sxs-lookup"><span data-stu-id="59419-203">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="59419-204">**Klucz**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="59419-204">**Key**: `applicationName`</span></span>  
<span data-ttu-id="59419-205">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-205">**Type**: `string`</span></span>  
<span data-ttu-id="59419-206">**Wartość domyślna**: Nazwa zestawu, który zawiera punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-206">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="59419-207">**Zmienna środowiskowa**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="59419-207">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="59419-208">Aby ustawić tę wartość, należy użyć zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="59419-208">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="59419-209">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="59419-209">ContentRoot</span></span>

<span data-ttu-id="59419-210">Właściwość [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="59419-210">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="59419-211">Jeśli ścieżka nie istnieje, uruchomienie hosta nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="59419-211">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="59419-212">**Klucz**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="59419-212">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="59419-213">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-213">**Type**: `string`</span></span>  
<span data-ttu-id="59419-214">**Domyślnie**: folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-214">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="59419-215">**Zmienna środowiskowa**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="59419-215">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="59419-216">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseContentRoot` `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="59419-216">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="59419-217">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="59419-217">For more information, see:</span></span>

* [<span data-ttu-id="59419-218">Podstawy: zawartość główna</span><span class="sxs-lookup"><span data-stu-id="59419-218">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="59419-219">WebRoot</span><span class="sxs-lookup"><span data-stu-id="59419-219">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="59419-220">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="59419-220">EnvironmentName</span></span>

<span data-ttu-id="59419-221">Dla właściwości [IHostEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) można ustawić dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="59419-221">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="59419-222">Wartości zdefiniowane przez platformę obejmują `Development` , `Staging` , i `Production` .</span><span class="sxs-lookup"><span data-stu-id="59419-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="59419-223">W wartościach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="59419-223">Values aren't case-sensitive.</span></span>

<span data-ttu-id="59419-224">**Klucz**: `environment`</span><span class="sxs-lookup"><span data-stu-id="59419-224">**Key**: `environment`</span></span>  
<span data-ttu-id="59419-225">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-225">**Type**: `string`</span></span>  
<span data-ttu-id="59419-226">**Wartość domyślna**: `Production`</span><span class="sxs-lookup"><span data-stu-id="59419-226">**Default**: `Production`</span></span>  
<span data-ttu-id="59419-227">**Zmienna środowiskowa**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="59419-227">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="59419-228">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseEnvironment` `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="59419-228">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="59419-229">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="59419-229">ShutdownTimeout</span></span>

<span data-ttu-id="59419-230">[HostOptions. shutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) ustawia limit czasu dla <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="59419-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="59419-231">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="59419-231">The default value is five seconds.</span></span>  <span data-ttu-id="59419-232">Podczas okresu przekroczenia limitu czasu Host:</span><span class="sxs-lookup"><span data-stu-id="59419-232">During the timeout period, the host:</span></span>

* <span data-ttu-id="59419-233">Wyzwala [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="59419-233">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="59419-234">Próbuje zatrzymać usługi hostowane, rejestrowanie błędów dla usług, których zatrzymanie nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="59419-234">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="59419-235">Jeśli limit czasu upłynie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-235">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="59419-236">Usługi są zatrzymane nawet wtedy, gdy nie zakończyły przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="59419-236">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="59419-237">Jeśli usługi wymagają dodatkowego czasu na zatrzymanie, zwiększ limit czasu.</span><span class="sxs-lookup"><span data-stu-id="59419-237">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="59419-238">**Klucz**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="59419-238">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="59419-239">**Typ**: `int`</span><span class="sxs-lookup"><span data-stu-id="59419-239">**Type**: `int`</span></span>  
<span data-ttu-id="59419-240">**Wartość domyślna**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="59419-240">**Default**: 5 seconds</span></span>  
<span data-ttu-id="59419-241">**Zmienna środowiskowa**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="59419-241">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="59419-242">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub skonfiguruj `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="59419-242">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="59419-243">Poniższy przykład ustawia limit czasu na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="59419-243">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="59419-244">Wyłącz ponowne ładowanie konfiguracji aplikacji przy zmianie</span><span class="sxs-lookup"><span data-stu-id="59419-244">Disable app configuration reload on change</span></span>

<span data-ttu-id="59419-245">[Domyślnie](xref:fundamentals/configuration/index#default) *appsettings.json* i *appSettings. { Środowisko}. kod JSON* jest ponownie ładowany, gdy plik ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="59419-245">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="59419-246">Aby wyłączyć to zachowanie ponownego ładowania w ASP.NET Core 5,0 lub nowszym, ustaw `hostBuilder:reloadConfigOnChange` klucz na `false` .</span><span class="sxs-lookup"><span data-stu-id="59419-246">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="59419-247">**Klucz**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="59419-247">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="59419-248">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="59419-248">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="59419-249">**Wartość domyślna**: `true`</span><span class="sxs-lookup"><span data-stu-id="59419-249">**Default**: `true`</span></span>  
<span data-ttu-id="59419-250">**Argument wiersza polecenia**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="59419-250">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="59419-251">**Zmienna środowiskowa**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="59419-251">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="59419-252">Separator dwukropek ( `:` ) nie działa ze zmiennymi kluczy hierarchicznych na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="59419-252">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="59419-253">Aby uzyskać więcej informacji, zobacz [zmienne środowiskowe](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="59419-253">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="59419-254">Ustawienia usługi Web Apps</span><span class="sxs-lookup"><span data-stu-id="59419-254">Settings for web apps</span></span>

<span data-ttu-id="59419-255">Niektóre ustawienia hosta mają zastosowanie tylko do obciążeń protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="59419-255">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="59419-256">Domyślnie zmienne środowiskowe używane do konfigurowania tych ustawień mogą mieć `DOTNET_` `ASPNETCORE_` prefiks lub.</span><span class="sxs-lookup"><span data-stu-id="59419-256">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="59419-257">Metody rozszerzenia dla `IWebHostBuilder` programu są dostępne dla tych ustawień.</span><span class="sxs-lookup"><span data-stu-id="59419-257">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="59419-258">Przykłady kodu, które pokazują, jak wywołać metody rozszerzane `webBuilder` jest wystąpieniem `IWebHostBuilder` , tak jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="59419-258">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="59419-259">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="59419-259">CaptureStartupErrors</span></span>

<span data-ttu-id="59419-260">Gdy `false` , błędy podczas uruchamiania, kończy się hostem.</span><span class="sxs-lookup"><span data-stu-id="59419-260">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="59419-261">Gdy `true` host przechwytuje wyjątki podczas uruchamiania, a następnie próbuje uruchomić serwer.</span><span class="sxs-lookup"><span data-stu-id="59419-261">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="59419-262">**Klucz**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="59419-262">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="59419-263">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="59419-263">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="59419-264">**Domyślnie**: wartość domyślna to, `false` chyba że aplikacja działa z Kestrel za usługami IIS, gdzie wartość domyślna to `true` .</span><span class="sxs-lookup"><span data-stu-id="59419-264">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="59419-265">**Zmienna środowiskowa**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="59419-265">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="59419-266">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="59419-266">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="59419-267">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="59419-267">DetailedErrors</span></span>

<span data-ttu-id="59419-268">Po włączeniu lub gdy środowisko jest `Development` , aplikacja przechwytuje szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="59419-268">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="59419-269">**Klucz**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="59419-269">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="59419-270">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="59419-270">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="59419-271">**Wartość domyślna**: `false`</span><span class="sxs-lookup"><span data-stu-id="59419-271">**Default**: `false`</span></span>  
<span data-ttu-id="59419-272">**Zmienna środowiskowa**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="59419-272">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="59419-273">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="59419-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="59419-274">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="59419-274">HostingStartupAssemblies</span></span>

<span data-ttu-id="59419-275">Rozdzielany średnikami ciąg początkowych zestawów startowych do załadowania podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="59419-275">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="59419-276">Mimo że wartość konfiguracji jest domyślnie pustym ciągiem, zestaw startowy obsługujący zawsze zawiera zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-276">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="59419-277">W przypadku udostępniania zestawów startowych są one dodawane do zestawu aplikacji do załadowania, gdy aplikacja kompiluje swoje popularne usługi podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="59419-277">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="59419-278">**Klucz**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="59419-278">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="59419-279">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-279">**Type**: `string`</span></span>  
<span data-ttu-id="59419-280">**Wartość domyślna**: pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="59419-280">**Default**: Empty string</span></span>  
<span data-ttu-id="59419-281">**Zmienna środowiskowa**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="59419-281">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="59419-282">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="59419-282">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="59419-283">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="59419-283">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="59419-284">Rozdzielany średnikami ciąg początkowych zestawów uruchamiania, który ma zostać wykluczony podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="59419-284">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="59419-285">**Klucz**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="59419-285">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="59419-286">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-286">**Type**: `string`</span></span>  
<span data-ttu-id="59419-287">**Wartość domyślna**: pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="59419-287">**Default**: Empty string</span></span>  
<span data-ttu-id="59419-288">**Zmienna środowiskowa**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="59419-288">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="59419-289">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="59419-289">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="59419-290">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="59419-290">HTTPS_Port</span></span>

<span data-ttu-id="59419-291">Port przekierowania protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="59419-291">The HTTPS redirect port.</span></span> <span data-ttu-id="59419-292">Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="59419-292">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="59419-293">**Klucz**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="59419-293">**Key**: `https_port`</span></span>  
<span data-ttu-id="59419-294">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-294">**Type**: `string`</span></span>  
<span data-ttu-id="59419-295">Wartość **Domyślna**: nie ustawiono wartości domyślnej.</span><span class="sxs-lookup"><span data-stu-id="59419-295">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="59419-296">**Zmienna środowiskowa**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="59419-296">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="59419-297">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="59419-297">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="59419-298">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="59419-298">PreferHostingUrls</span></span>

<span data-ttu-id="59419-299">Wskazuje, czy host powinien nasłuchiwać adresów URL skonfigurowanych przy użyciu `IWebHostBuilder` zamiast adresów URL skonfigurowanych dla `IServer` implementacji.</span><span class="sxs-lookup"><span data-stu-id="59419-299">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="59419-300">**Klucz**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="59419-300">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="59419-301">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="59419-301">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="59419-302">**Wartość domyślna**: `true`</span><span class="sxs-lookup"><span data-stu-id="59419-302">**Default**: `true`</span></span>  
<span data-ttu-id="59419-303">**Zmienna środowiskowa**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="59419-303">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="59419-304">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="59419-304">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="59419-305">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="59419-305">PreventHostingStartup</span></span>

<span data-ttu-id="59419-306">Zapobiega automatycznemu ładowaniu zestawów startowych hostingu, w tym hostingu zestawów startowych skonfigurowanych przez zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-306">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="59419-307">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="59419-307">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="59419-308">**Klucz**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="59419-308">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="59419-309">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="59419-309">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="59419-310">**Wartość domyślna**: `false`</span><span class="sxs-lookup"><span data-stu-id="59419-310">**Default**: `false`</span></span>  
<span data-ttu-id="59419-311">**Zmienna środowiskowa**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="59419-311">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="59419-312">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="59419-312">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="59419-313">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="59419-313">StartupAssembly</span></span>

<span data-ttu-id="59419-314">Zestaw do wyszukiwania `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="59419-314">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="59419-315">**Klucz**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="59419-315">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="59419-316">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-316">**Type**: `string`</span></span>  
<span data-ttu-id="59419-317">**Domyślnie**: zestaw aplikacji</span><span class="sxs-lookup"><span data-stu-id="59419-317">**Default**: The app's assembly</span></span>  
<span data-ttu-id="59419-318">**Zmienna środowiskowa**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="59419-318">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="59419-319">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołania `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="59419-319">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="59419-320">`UseStartup` może przyjmować nazwę zestawu ( `string` ) lub typ ( `TStartup` ).</span><span class="sxs-lookup"><span data-stu-id="59419-320">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="59419-321">Jeśli `UseStartup` wywoływana jest wiele metod, pierwszeństwo ma Ostatnia.</span><span class="sxs-lookup"><span data-stu-id="59419-321">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="59419-322">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="59419-322">URLs</span></span>

<span data-ttu-id="59419-323">Rozdzielana średnikami lista adresów IP lub adresów hostów z portami i protokołami, na których serwer powinien nasłuchiwać żądań.</span><span class="sxs-lookup"><span data-stu-id="59419-323">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="59419-324">Na przykład `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="59419-324">For example, `http://localhost:123`.</span></span> <span data-ttu-id="59419-325">Użyj " \* ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwie hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000` ).</span><span class="sxs-lookup"><span data-stu-id="59419-325">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="59419-326">Protokół ( `http://` lub `https://` ) musi być dołączony do każdego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="59419-326">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="59419-327">Obsługiwane formaty różnią się między serwerami.</span><span class="sxs-lookup"><span data-stu-id="59419-327">Supported formats vary among servers.</span></span>

<span data-ttu-id="59419-328">**Klucz**: `urls`</span><span class="sxs-lookup"><span data-stu-id="59419-328">**Key**: `urls`</span></span>  
<span data-ttu-id="59419-329">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-329">**Type**: `string`</span></span>  
<span data-ttu-id="59419-330">**Wartość domyślna**: `http://localhost:5000` i `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="59419-330">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="59419-331">**Zmienna środowiskowa**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="59419-331">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="59419-332">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="59419-332">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="59419-333">Kestrel ma własny interfejs API konfiguracji punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="59419-333">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="59419-334">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="59419-334">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="59419-335">WebRoot</span><span class="sxs-lookup"><span data-stu-id="59419-335">WebRoot</span></span>

<span data-ttu-id="59419-336">Właściwość [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) określa ścieżkę względną do statycznych zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-336">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="59419-337">Jeśli ścieżka nie istnieje, jest używany dostawca plików No-op.</span><span class="sxs-lookup"><span data-stu-id="59419-337">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="59419-338">**Klucz**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="59419-338">**Key**: `webroot`</span></span>  
<span data-ttu-id="59419-339">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-339">**Type**: `string`</span></span>  
<span data-ttu-id="59419-340">**Wartość domyślna**: wartość domyślna to `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="59419-340">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="59419-341">Ścieżka do *elementu {content root}/wwwroot* musi istnieć.</span><span class="sxs-lookup"><span data-stu-id="59419-341">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="59419-342">**Zmienna środowiskowa**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="59419-342">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="59419-343">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseWebRoot` `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="59419-343">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="59419-344">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="59419-344">For more information, see:</span></span>

* [<span data-ttu-id="59419-345">Podstawy: katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="59419-345">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="59419-346">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="59419-346">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="59419-347">Zarządzanie okresem istnienia hosta</span><span class="sxs-lookup"><span data-stu-id="59419-347">Manage the host lifetime</span></span>

<span data-ttu-id="59419-348">Wywołaj metody z skompilowanej <xref:Microsoft.Extensions.Hosting.IHost> implementacji, aby uruchomić i zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="59419-348">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="59419-349">Te metody mają wpływ na wszystkie  <xref:Microsoft.Extensions.Hosting.IHostedService> implementacje zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="59419-349">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="59419-350">Uruchom</span><span class="sxs-lookup"><span data-stu-id="59419-350">Run</span></span>

<span data-ttu-id="59419-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uruchamia aplikację i blokuje wątek wywołujący do momentu wyłączenia hosta.</span><span class="sxs-lookup"><span data-stu-id="59419-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="59419-352">RunAsync</span><span class="sxs-lookup"><span data-stu-id="59419-352">RunAsync</span></span>

<span data-ttu-id="59419-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uruchamia aplikację i zwraca <xref:System.Threading.Tasks.Task> , która kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="59419-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="59419-354">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="59419-354">RunConsoleAsync</span></span>

<span data-ttu-id="59419-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>włącza obsługę konsoli, kompiluje i uruchamia hosta, a następnie czeka na zamknięcie programu <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="59419-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="59419-356">Rozpocznij</span><span class="sxs-lookup"><span data-stu-id="59419-356">Start</span></span>

<span data-ttu-id="59419-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="59419-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="59419-358">StartAsync</span><span class="sxs-lookup"><span data-stu-id="59419-358">StartAsync</span></span>

<span data-ttu-id="59419-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> uruchamia hosta i zwraca <xref:System.Threading.Tasks.Task> , który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="59419-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="59419-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> jest wywoływana na początku `StartAsync` , który czeka na zakończenie przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="59419-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="59419-361">Może to służyć do opóźnienia uruchamiania do momentu zasygnalizowania zdarzenia zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="59419-361">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="59419-362">StopAsync</span><span class="sxs-lookup"><span data-stu-id="59419-362">StopAsync</span></span>

<span data-ttu-id="59419-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> próbuje zatrzymać hosta w ramach podanego limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="59419-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="59419-364">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="59419-364">WaitForShutdown</span></span>

<span data-ttu-id="59419-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje wątek wywołujący do momentu wyzwolenia przez IHostLifetime, na przykład przez <kbd>naciśnięcie klawiszy CTRL</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="59419-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="59419-366">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="59419-366">WaitForShutdownAsync</span></span>

<span data-ttu-id="59419-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Zwraca wartość <xref:System.Threading.Tasks.Task> , która kończy się, gdy zamknięcie jest wyzwalane za pośrednictwem danego tokenu i wywołań <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="59419-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="59419-368">Zewnętrzna kontrola</span><span class="sxs-lookup"><span data-stu-id="59419-368">External control</span></span>

<span data-ttu-id="59419-369">Bezpośrednią kontrolę nad okresem istnienia hosta można uzyskać przy użyciu metod, które mogą być wywoływane zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="59419-369">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="59419-370">Szablony ASP.NET Core tworzą hosta ogólnego platformy .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="59419-370">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="59419-371">Ten temat zawiera informacje dotyczące korzystania z hosta ogólnego platformy .NET w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="59419-371">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="59419-372">Aby uzyskać informacje na temat korzystania z hosta ogólnego platformy .NET w aplikacjach konsolowych, zobacz [host ogólny programu .NET](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="59419-372">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="59419-373">Definicja hosta</span><span class="sxs-lookup"><span data-stu-id="59419-373">Host definition</span></span>

<span data-ttu-id="59419-374">*Host* to obiekt, który hermetyzuje zasoby aplikacji, takie jak:</span><span class="sxs-lookup"><span data-stu-id="59419-374">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="59419-375">Iniekcja zależności (DI)</span><span class="sxs-lookup"><span data-stu-id="59419-375">Dependency injection (DI)</span></span>
* <span data-ttu-id="59419-376">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="59419-376">Logging</span></span>
* <span data-ttu-id="59419-377">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="59419-377">Configuration</span></span>
* <span data-ttu-id="59419-378">`IHostedService` metod</span><span class="sxs-lookup"><span data-stu-id="59419-378">`IHostedService` implementations</span></span>

<span data-ttu-id="59419-379">Po uruchomieniu hosta jest on wywoływany <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> dla każdej implementacji <xref:Microsoft.Extensions.Hosting.IHostedService> zarejestrowanej w kolekcji kontenera usługi hostowanej usług.</span><span class="sxs-lookup"><span data-stu-id="59419-379">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="59419-380">W aplikacji sieci Web jedną z `IHostedService` implementacji jest usługa sieci Web, która uruchamia [implementację serwera http](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="59419-380">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="59419-381">Główną przyczyną uwzględnienia wszystkich zasobów zależnych od aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: Kontrola uruchamiania aplikacji i bezpieczne zamykanie.</span><span class="sxs-lookup"><span data-stu-id="59419-381">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="59419-382">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="59419-382">Set up a host</span></span>

<span data-ttu-id="59419-383">Host jest zazwyczaj konfigurowany, zbudowany i uruchamiany przez kod w `Program` klasie.</span><span class="sxs-lookup"><span data-stu-id="59419-383">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="59419-384">`Main`Metoda:</span><span class="sxs-lookup"><span data-stu-id="59419-384">The `Main` method:</span></span>

* <span data-ttu-id="59419-385">Wywołuje `CreateHostBuilder` metodę, aby utworzyć i skonfigurować obiekt konstruktora.</span><span class="sxs-lookup"><span data-stu-id="59419-385">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="59419-386">Wywołania `Build` i `Run` metody w obiekcie konstruktora.</span><span class="sxs-lookup"><span data-stu-id="59419-386">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="59419-387">Szablony sieci Web ASP.NET Core generują następujący kod w celu utworzenia hosta ogólnego:</span><span class="sxs-lookup"><span data-stu-id="59419-387">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="59419-388">Poniższy kod tworzy hosta ogólnego przy użyciu obciążenia innego niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="59419-388">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="59419-389">`IHostedService`Implementacja jest dodawana do kontenera di:</span><span class="sxs-lookup"><span data-stu-id="59419-389">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="59419-390">W przypadku obciążeń HTTP `Main` Metoda jest taka sama, ale `CreateHostBuilder` wywołuje `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="59419-390">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="59419-391">Poprzedni kod jest generowany przez szablony ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="59419-391">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="59419-392">Jeśli aplikacja używa Entity Framework Core, nie zmieniaj nazwy ani podpisu `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="59419-392">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="59419-393">[Narzędzia Entity Framework Core](/ef/core/miscellaneous/cli/) oczekują na znalezienie `CreateHostBuilder` metody, która konfiguruje hosta bez uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-393">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="59419-394">Aby uzyskać więcej informacji, zobacz [Tworzenie DbContext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="59419-394">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="59419-395">Ustawienia domyślnego konstruktora</span><span class="sxs-lookup"><span data-stu-id="59419-395">Default builder settings</span></span>

<span data-ttu-id="59419-396"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>Metoda:</span><span class="sxs-lookup"><span data-stu-id="59419-396">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="59419-397">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na ścieżkę zwracaną przez <xref:System.IO.Directory.GetCurrentDirectory%2A> .</span><span class="sxs-lookup"><span data-stu-id="59419-397">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="59419-398">Ładuje konfigurację hosta z:</span><span class="sxs-lookup"><span data-stu-id="59419-398">Loads host configuration from:</span></span>
  * <span data-ttu-id="59419-399">Zmienne środowiskowe poprzedzone prefiksem `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="59419-399">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="59419-400">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="59419-400">Command-line arguments.</span></span>
* <span data-ttu-id="59419-401">Ładuje konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="59419-401">Loads app configuration from:</span></span>
  * <span data-ttu-id="59419-402">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="59419-402">*appsettings.json*.</span></span>
  * <span data-ttu-id="59419-403">*appSettings. {Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="59419-403">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="59419-404">Wpisy [tajne użytkownika](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku.</span><span class="sxs-lookup"><span data-stu-id="59419-404">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="59419-405">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="59419-405">Environment variables.</span></span>
  * <span data-ttu-id="59419-406">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="59419-406">Command-line arguments.</span></span>
* <span data-ttu-id="59419-407">Dodaje następujących dostawców [rejestrowania](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="59419-407">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="59419-408">Konsola</span><span class="sxs-lookup"><span data-stu-id="59419-408">Console</span></span>
  * <span data-ttu-id="59419-409">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="59419-409">Debug</span></span>
  * <span data-ttu-id="59419-410">EventSource</span><span class="sxs-lookup"><span data-stu-id="59419-410">EventSource</span></span>
  * <span data-ttu-id="59419-411">EventLog (tylko w przypadku uruchamiania w systemie Windows)</span><span class="sxs-lookup"><span data-stu-id="59419-411">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="59419-412">Umożliwia [weryfikację zakresu](xref:fundamentals/dependency-injection#scope-validation) i [Sprawdzanie poprawności zależności](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) , gdy środowisko jest opracowywane.</span><span class="sxs-lookup"><span data-stu-id="59419-412">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="59419-413">`ConfigureWebHostDefaults`Metoda:</span><span class="sxs-lookup"><span data-stu-id="59419-413">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="59419-414">Ładuje konfigurację hosta ze zmiennych środowiskowych, które są poprzedzone prefiksem `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="59419-414">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="59419-415">Ustawia serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci Web i konfiguruje go przy użyciu dostawców konfiguracji hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-415">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="59419-416">Aby poznać domyślne opcje serwera Kestrel, zobacz <xref:fundamentals/servers/kestrel#kestrel-options> .</span><span class="sxs-lookup"><span data-stu-id="59419-416">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="59419-417">Dodaje [oprogramowanie pośredniczące do filtrowania hosta](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="59419-417">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="59419-418">Dodaje [przekazane nagłówki — oprogramowanie pośredniczące](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest równe `true` .</span><span class="sxs-lookup"><span data-stu-id="59419-418">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="59419-419">Włącza integrację usług IIS.</span><span class="sxs-lookup"><span data-stu-id="59419-419">Enables IIS integration.</span></span> <span data-ttu-id="59419-420">Aby poznać domyślne opcje usług IIS, zobacz <xref:host-and-deploy/iis/index#iis-options> .</span><span class="sxs-lookup"><span data-stu-id="59419-420">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="59419-421">[Ustawienia dla wszystkich typów aplikacji](#settings-for-all-app-types) i [Ustawienia dla usługi Web Apps](#settings-for-web-apps) w dalszej części tego artykułu pokazują, jak zastąpić ustawienia domyślnego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="59419-421">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="59419-422">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="59419-422">Framework-provided services</span></span>

<span data-ttu-id="59419-423">Następujące usługi są rejestrowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="59419-423">The following services are registered automatically:</span></span>

* [<span data-ttu-id="59419-424">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="59419-424">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="59419-425">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="59419-425">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="59419-426">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="59419-426">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="59419-427">Aby uzyskać więcej informacji na temat usług udostępnianych przez platformę, zobacz <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="59419-427">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="59419-428">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="59419-428">IHostApplicationLifetime</span></span>

<span data-ttu-id="59419-429">Wstrzyknąć <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> usługę (dawniej `IApplicationLifetime` ) do dowolnej klasy w celu obsługi zadań po uruchomieniu i bezpiecznego zamykania.</span><span class="sxs-lookup"><span data-stu-id="59419-429">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="59419-430">Trzy właściwości interfejsu to tokeny anulowania używane do rejestrowania metod obsługi zdarzeń uruchamiania i zatrzymywania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-430">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="59419-431">Interfejs zawiera również `StopApplication` metodę.</span><span class="sxs-lookup"><span data-stu-id="59419-431">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="59419-432">Poniższy przykład to `IHostedService` implementacja, która rejestruje `IHostApplicationLifetime` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="59419-432">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="59419-433">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="59419-433">IHostLifetime</span></span>

<span data-ttu-id="59419-434"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Implementacja kontroluje moment uruchomienia hosta i jego zatrzymania.</span><span class="sxs-lookup"><span data-stu-id="59419-434">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="59419-435">Używana jest Ostatnia zarejestrowana implementacja.</span><span class="sxs-lookup"><span data-stu-id="59419-435">The last implementation registered is used.</span></span>

<span data-ttu-id="59419-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` jest `IHostLifetime` implementacją domyślną.</span><span class="sxs-lookup"><span data-stu-id="59419-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="59419-437">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="59419-437">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="59419-438">Nasłuchuje <kbd>kombinacji klawiszy CTRL</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM oraz wywołań, <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> Aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="59419-438">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="59419-439">Odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="59419-439">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="59419-440">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="59419-440">IHostEnvironment</span></span>

<span data-ttu-id="59419-441">Wsuń <xref:Microsoft.Extensions.Hosting.IHostEnvironment> usługę do klasy, aby uzyskać informacje o następujących ustawieniach:</span><span class="sxs-lookup"><span data-stu-id="59419-441">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="59419-442">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="59419-442">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="59419-443">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="59419-443">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="59419-444">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="59419-444">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="59419-445">Aplikacje sieci Web implementują `IWebHostEnvironment` interfejs, który dziedziczy `IHostEnvironment` i dodaje [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="59419-445">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="59419-446">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="59419-446">Host configuration</span></span>

<span data-ttu-id="59419-447">Konfiguracja hosta jest używana we właściwościach <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementacji.</span><span class="sxs-lookup"><span data-stu-id="59419-447">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="59419-448">Konfiguracja hosta jest dostępna w [HostBuilderContext.Configwersja](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> .</span><span class="sxs-lookup"><span data-stu-id="59419-448">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="59419-449">Po `ConfigureAppConfiguration` program `HostBuilderContext.Configuration` zostanie zastąpiony konfiguracją aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-449">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="59419-450">Aby dodać konfigurację hosta, wywołaj polecenie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="59419-450">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="59419-451">`ConfigureHostConfiguration` może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="59419-451">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="59419-452">Host używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="59419-452">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="59419-453">Dostawca zmiennych środowiskowych z prefiksami `DOTNET_` i argumentami wiersza polecenia są uwzględniane przez `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="59419-453">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="59419-454">W przypadku aplikacji sieci Web jest dodawany dostawca zmiennych środowiskowych z prefiksem `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="59419-454">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="59419-455">Prefiks jest usuwany, gdy są odczytywane zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="59419-455">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="59419-456">Na przykład wartość zmiennej środowiskowej jest `ASPNETCORE_ENVIRONMENT` wartością konfiguracji hosta dla `environment` klucza.</span><span class="sxs-lookup"><span data-stu-id="59419-456">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="59419-457">Poniższy przykład umożliwia utworzenie konfiguracji hosta:</span><span class="sxs-lookup"><span data-stu-id="59419-457">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="59419-458">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="59419-458">App configuration</span></span>

<span data-ttu-id="59419-459">Konfiguracja aplikacji jest tworzona przez wywołanie metody <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="59419-459">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="59419-460">`ConfigureAppConfiguration` może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="59419-460">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="59419-461">Aplikacja używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="59419-461">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="59419-462">Konfiguracja utworzona przez program `ConfigureAppConfiguration` jest dostępna w [HostBuilderContext.Configwersja](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) na potrzeby kolejnych operacji i jako usługa z di.</span><span class="sxs-lookup"><span data-stu-id="59419-462">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="59419-463">Konfiguracja hosta jest również dodawana do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-463">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="59419-464">Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="59419-464">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="59419-465">Ustawienia dla wszystkich typów aplikacji</span><span class="sxs-lookup"><span data-stu-id="59419-465">Settings for all app types</span></span>

<span data-ttu-id="59419-466">Ta sekcja zawiera listę ustawień hosta, które dotyczą zarówno obciążeń HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="59419-466">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="59419-467">Domyślnie zmienne środowiskowe używane do konfigurowania tych ustawień mogą mieć `DOTNET_` `ASPNETCORE_` prefiks lub.</span><span class="sxs-lookup"><span data-stu-id="59419-467">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="59419-468">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="59419-468">ApplicationName</span></span>

<span data-ttu-id="59419-469">Właściwość [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) jest ustawiana na podstawie konfiguracji hosta podczas konstruowania hosta.</span><span class="sxs-lookup"><span data-stu-id="59419-469">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="59419-470">**Klucz**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="59419-470">**Key**: `applicationName`</span></span>  
<span data-ttu-id="59419-471">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-471">**Type**: `string`</span></span>  
<span data-ttu-id="59419-472">**Wartość domyślna**: Nazwa zestawu, który zawiera punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-472">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="59419-473">**Zmienna środowiskowa**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="59419-473">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="59419-474">Aby ustawić tę wartość, należy użyć zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="59419-474">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="59419-475">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="59419-475">ContentRoot</span></span>

<span data-ttu-id="59419-476">Właściwość [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="59419-476">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="59419-477">Jeśli ścieżka nie istnieje, uruchomienie hosta nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="59419-477">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="59419-478">**Klucz**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="59419-478">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="59419-479">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-479">**Type**: `string`</span></span>  
<span data-ttu-id="59419-480">**Domyślnie**: folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-480">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="59419-481">**Zmienna środowiskowa**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="59419-481">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="59419-482">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseContentRoot` `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="59419-482">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="59419-483">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="59419-483">For more information, see:</span></span>

* [<span data-ttu-id="59419-484">Podstawy: zawartość główna</span><span class="sxs-lookup"><span data-stu-id="59419-484">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="59419-485">WebRoot</span><span class="sxs-lookup"><span data-stu-id="59419-485">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="59419-486">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="59419-486">EnvironmentName</span></span>

<span data-ttu-id="59419-487">Dla właściwości [IHostEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) można ustawić dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="59419-487">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="59419-488">Wartości zdefiniowane przez platformę obejmują `Development` , `Staging` , i `Production` .</span><span class="sxs-lookup"><span data-stu-id="59419-488">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="59419-489">W wartościach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="59419-489">Values aren't case-sensitive.</span></span>

<span data-ttu-id="59419-490">**Klucz**: `environment`</span><span class="sxs-lookup"><span data-stu-id="59419-490">**Key**: `environment`</span></span>  
<span data-ttu-id="59419-491">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-491">**Type**: `string`</span></span>  
<span data-ttu-id="59419-492">**Wartość domyślna**: `Production`</span><span class="sxs-lookup"><span data-stu-id="59419-492">**Default**: `Production`</span></span>  
<span data-ttu-id="59419-493">**Zmienna środowiskowa**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="59419-493">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="59419-494">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseEnvironment` `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="59419-494">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="59419-495">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="59419-495">ShutdownTimeout</span></span>

<span data-ttu-id="59419-496">[HostOptions. shutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) ustawia limit czasu dla <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="59419-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="59419-497">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="59419-497">The default value is five seconds.</span></span>  <span data-ttu-id="59419-498">Podczas okresu przekroczenia limitu czasu Host:</span><span class="sxs-lookup"><span data-stu-id="59419-498">During the timeout period, the host:</span></span>

* <span data-ttu-id="59419-499">Wyzwala [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="59419-499">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="59419-500">Próbuje zatrzymać usługi hostowane, rejestrowanie błędów dla usług, których zatrzymanie nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="59419-500">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="59419-501">Jeśli limit czasu upłynie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-501">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="59419-502">Usługi są zatrzymane nawet wtedy, gdy nie zakończyły przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="59419-502">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="59419-503">Jeśli usługi wymagają dodatkowego czasu na zatrzymanie, zwiększ limit czasu.</span><span class="sxs-lookup"><span data-stu-id="59419-503">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="59419-504">**Klucz**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="59419-504">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="59419-505">**Typ**: `int`</span><span class="sxs-lookup"><span data-stu-id="59419-505">**Type**: `int`</span></span>  
<span data-ttu-id="59419-506">**Wartość domyślna**: 5 sekund</span><span class="sxs-lookup"><span data-stu-id="59419-506">**Default**: 5 seconds</span></span>  
<span data-ttu-id="59419-507">**Zmienna środowiskowa**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="59419-507">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="59419-508">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub skonfiguruj `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="59419-508">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="59419-509">Poniższy przykład ustawia limit czasu na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="59419-509">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="59419-510">Ustawienia usługi Web Apps</span><span class="sxs-lookup"><span data-stu-id="59419-510">Settings for web apps</span></span>

<span data-ttu-id="59419-511">Niektóre ustawienia hosta mają zastosowanie tylko do obciążeń protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="59419-511">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="59419-512">Domyślnie zmienne środowiskowe używane do konfigurowania tych ustawień mogą mieć `DOTNET_` `ASPNETCORE_` prefiks lub.</span><span class="sxs-lookup"><span data-stu-id="59419-512">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="59419-513">Metody rozszerzenia dla `IWebHostBuilder` programu są dostępne dla tych ustawień.</span><span class="sxs-lookup"><span data-stu-id="59419-513">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="59419-514">Przykłady kodu, które pokazują, jak wywołać metody rozszerzane `webBuilder` jest wystąpieniem `IWebHostBuilder` , tak jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="59419-514">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="59419-515">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="59419-515">CaptureStartupErrors</span></span>

<span data-ttu-id="59419-516">Gdy `false` , błędy podczas uruchamiania, kończy się hostem.</span><span class="sxs-lookup"><span data-stu-id="59419-516">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="59419-517">Gdy `true` host przechwytuje wyjątki podczas uruchamiania, a następnie próbuje uruchomić serwer.</span><span class="sxs-lookup"><span data-stu-id="59419-517">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="59419-518">**Klucz**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="59419-518">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="59419-519">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="59419-519">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="59419-520">**Domyślnie**: wartość domyślna to, `false` chyba że aplikacja działa z Kestrel za usługami IIS, gdzie wartość domyślna to `true` .</span><span class="sxs-lookup"><span data-stu-id="59419-520">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="59419-521">**Zmienna środowiskowa**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="59419-521">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="59419-522">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="59419-522">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="59419-523">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="59419-523">DetailedErrors</span></span>

<span data-ttu-id="59419-524">Po włączeniu lub gdy środowisko jest `Development` , aplikacja przechwytuje szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="59419-524">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="59419-525">**Klucz**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="59419-525">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="59419-526">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="59419-526">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="59419-527">**Wartość domyślna**: `false`</span><span class="sxs-lookup"><span data-stu-id="59419-527">**Default**: `false`</span></span>  
<span data-ttu-id="59419-528">**Zmienna środowiskowa**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="59419-528">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="59419-529">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="59419-529">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="59419-530">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="59419-530">HostingStartupAssemblies</span></span>

<span data-ttu-id="59419-531">Rozdzielany średnikami ciąg początkowych zestawów startowych do załadowania podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="59419-531">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="59419-532">Mimo że wartość konfiguracji jest domyślnie pustym ciągiem, zestaw startowy obsługujący zawsze zawiera zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-532">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="59419-533">W przypadku udostępniania zestawów startowych są one dodawane do zestawu aplikacji do załadowania, gdy aplikacja kompiluje swoje popularne usługi podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="59419-533">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="59419-534">**Klucz**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="59419-534">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="59419-535">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-535">**Type**: `string`</span></span>  
<span data-ttu-id="59419-536">**Wartość domyślna**: pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="59419-536">**Default**: Empty string</span></span>  
<span data-ttu-id="59419-537">**Zmienna środowiskowa**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="59419-537">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="59419-538">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="59419-538">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="59419-539">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="59419-539">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="59419-540">Rozdzielany średnikami ciąg początkowych zestawów uruchamiania, który ma zostać wykluczony podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="59419-540">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="59419-541">**Klucz**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="59419-541">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="59419-542">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-542">**Type**: `string`</span></span>  
<span data-ttu-id="59419-543">**Wartość domyślna**: pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="59419-543">**Default**: Empty string</span></span>  
<span data-ttu-id="59419-544">**Zmienna środowiskowa**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="59419-544">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="59419-545">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="59419-545">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="59419-546">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="59419-546">HTTPS_Port</span></span>

<span data-ttu-id="59419-547">Port przekierowania protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="59419-547">The HTTPS redirect port.</span></span> <span data-ttu-id="59419-548">Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="59419-548">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="59419-549">**Klucz**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="59419-549">**Key**: `https_port`</span></span>  
<span data-ttu-id="59419-550">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-550">**Type**: `string`</span></span>  
<span data-ttu-id="59419-551">Wartość **Domyślna**: nie ustawiono wartości domyślnej.</span><span class="sxs-lookup"><span data-stu-id="59419-551">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="59419-552">**Zmienna środowiskowa**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="59419-552">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="59419-553">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="59419-553">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="59419-554">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="59419-554">PreferHostingUrls</span></span>

<span data-ttu-id="59419-555">Wskazuje, czy host powinien nasłuchiwać adresów URL skonfigurowanych przy użyciu `IWebHostBuilder` zamiast adresów URL skonfigurowanych dla `IServer` implementacji.</span><span class="sxs-lookup"><span data-stu-id="59419-555">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="59419-556">**Klucz**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="59419-556">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="59419-557">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="59419-557">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="59419-558">**Wartość domyślna**: `true`</span><span class="sxs-lookup"><span data-stu-id="59419-558">**Default**: `true`</span></span>  
<span data-ttu-id="59419-559">**Zmienna środowiskowa**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="59419-559">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="59419-560">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="59419-560">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="59419-561">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="59419-561">PreventHostingStartup</span></span>

<span data-ttu-id="59419-562">Zapobiega automatycznemu ładowaniu zestawów startowych hostingu, w tym hostingu zestawów startowych skonfigurowanych przez zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-562">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="59419-563">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="59419-563">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="59419-564">**Klucz**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="59419-564">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="59419-565">**Typ**: `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="59419-565">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="59419-566">**Wartość domyślna**: `false`</span><span class="sxs-lookup"><span data-stu-id="59419-566">**Default**: `false`</span></span>  
<span data-ttu-id="59419-567">**Zmienna środowiskowa**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="59419-567">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="59419-568">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="59419-568">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="59419-569">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="59419-569">StartupAssembly</span></span>

<span data-ttu-id="59419-570">Zestaw do wyszukiwania `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="59419-570">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="59419-571">**Klucz**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="59419-571">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="59419-572">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-572">**Type**: `string`</span></span>  
<span data-ttu-id="59419-573">**Domyślnie**: zestaw aplikacji</span><span class="sxs-lookup"><span data-stu-id="59419-573">**Default**: The app's assembly</span></span>  
<span data-ttu-id="59419-574">**Zmienna środowiskowa**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="59419-574">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="59419-575">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołania `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="59419-575">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="59419-576">`UseStartup` może przyjmować nazwę zestawu ( `string` ) lub typ ( `TStartup` ).</span><span class="sxs-lookup"><span data-stu-id="59419-576">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="59419-577">Jeśli `UseStartup` wywoływana jest wiele metod, pierwszeństwo ma Ostatnia.</span><span class="sxs-lookup"><span data-stu-id="59419-577">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="59419-578">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="59419-578">URLs</span></span>

<span data-ttu-id="59419-579">Rozdzielana średnikami lista adresów IP lub adresów hostów z portami i protokołami, na których serwer powinien nasłuchiwać żądań.</span><span class="sxs-lookup"><span data-stu-id="59419-579">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="59419-580">Na przykład `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="59419-580">For example, `http://localhost:123`.</span></span> <span data-ttu-id="59419-581">Użyj " \* ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwie hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000` ).</span><span class="sxs-lookup"><span data-stu-id="59419-581">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="59419-582">Protokół ( `http://` lub `https://` ) musi być dołączony do każdego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="59419-582">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="59419-583">Obsługiwane formaty różnią się między serwerami.</span><span class="sxs-lookup"><span data-stu-id="59419-583">Supported formats vary among servers.</span></span>

<span data-ttu-id="59419-584">**Klucz**: `urls`</span><span class="sxs-lookup"><span data-stu-id="59419-584">**Key**: `urls`</span></span>  
<span data-ttu-id="59419-585">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-585">**Type**: `string`</span></span>  
<span data-ttu-id="59419-586">**Wartość domyślna**: `http://localhost:5000` i `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="59419-586">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="59419-587">**Zmienna środowiskowa**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="59419-587">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="59419-588">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="59419-588">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="59419-589">Kestrel ma własny interfejs API konfiguracji punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="59419-589">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="59419-590">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="59419-590">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="59419-591">WebRoot</span><span class="sxs-lookup"><span data-stu-id="59419-591">WebRoot</span></span>

<span data-ttu-id="59419-592">Właściwość [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) określa ścieżkę względną do statycznych zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-592">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="59419-593">Jeśli ścieżka nie istnieje, jest używany dostawca plików No-op.</span><span class="sxs-lookup"><span data-stu-id="59419-593">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="59419-594">**Klucz**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="59419-594">**Key**: `webroot`</span></span>  
<span data-ttu-id="59419-595">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-595">**Type**: `string`</span></span>  
<span data-ttu-id="59419-596">**Wartość domyślna**: wartość domyślna to `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="59419-596">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="59419-597">Ścieżka do *elementu {content root}/wwwroot* musi istnieć.</span><span class="sxs-lookup"><span data-stu-id="59419-597">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="59419-598">**Zmienna środowiskowa**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="59419-598">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="59419-599">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseWebRoot` `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="59419-599">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="59419-600">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="59419-600">For more information, see:</span></span>

* [<span data-ttu-id="59419-601">Podstawy: katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="59419-601">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="59419-602">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="59419-602">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="59419-603">Zarządzanie okresem istnienia hosta</span><span class="sxs-lookup"><span data-stu-id="59419-603">Manage the host lifetime</span></span>

<span data-ttu-id="59419-604">Wywołaj metody z skompilowanej <xref:Microsoft.Extensions.Hosting.IHost> implementacji, aby uruchomić i zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="59419-604">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="59419-605">Te metody mają wpływ na wszystkie  <xref:Microsoft.Extensions.Hosting.IHostedService> implementacje zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="59419-605">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="59419-606">Uruchom</span><span class="sxs-lookup"><span data-stu-id="59419-606">Run</span></span>

<span data-ttu-id="59419-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uruchamia aplikację i blokuje wątek wywołujący do momentu wyłączenia hosta.</span><span class="sxs-lookup"><span data-stu-id="59419-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="59419-608">RunAsync</span><span class="sxs-lookup"><span data-stu-id="59419-608">RunAsync</span></span>

<span data-ttu-id="59419-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uruchamia aplikację i zwraca <xref:System.Threading.Tasks.Task> , która kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="59419-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="59419-610">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="59419-610">RunConsoleAsync</span></span>

<span data-ttu-id="59419-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>włącza obsługę konsoli, kompiluje i uruchamia hosta, a następnie czeka na zamknięcie programu <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="59419-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="59419-612">Rozpocznij</span><span class="sxs-lookup"><span data-stu-id="59419-612">Start</span></span>

<span data-ttu-id="59419-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="59419-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="59419-614">StartAsync</span><span class="sxs-lookup"><span data-stu-id="59419-614">StartAsync</span></span>

<span data-ttu-id="59419-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> uruchamia hosta i zwraca <xref:System.Threading.Tasks.Task> , który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="59419-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="59419-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> jest wywoływana na początku `StartAsync` , który czeka na zakończenie przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="59419-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="59419-617">Może to służyć do opóźnienia uruchamiania do momentu zasygnalizowania zdarzenia zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="59419-617">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="59419-618">StopAsync</span><span class="sxs-lookup"><span data-stu-id="59419-618">StopAsync</span></span>

<span data-ttu-id="59419-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> próbuje zatrzymać hosta w ramach podanego limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="59419-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="59419-620">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="59419-620">WaitForShutdown</span></span>

<span data-ttu-id="59419-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje wątek wywołujący do momentu wyzwolenia przez IHostLifetime, na przykład przez <kbd>naciśnięcie klawiszy CTRL</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="59419-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="59419-622">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="59419-622">WaitForShutdownAsync</span></span>

<span data-ttu-id="59419-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Zwraca wartość <xref:System.Threading.Tasks.Task> , która kończy się, gdy zamknięcie jest wyzwalane za pośrednictwem danego tokenu i wywołań <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="59419-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="59419-624">Zewnętrzna kontrola</span><span class="sxs-lookup"><span data-stu-id="59419-624">External control</span></span>

<span data-ttu-id="59419-625">Bezpośrednią kontrolę nad okresem istnienia hosta można uzyskać przy użyciu metod, które mogą być wywoływane zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="59419-625">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="59419-626">ASP.NET Core aplikacje konfigurują i uruchamiają hosta.</span><span class="sxs-lookup"><span data-stu-id="59419-626">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="59419-627">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="59419-627">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="59419-628">W tym artykule opisano ASP.NET Core hosta ogólnego ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ), który jest używany w przypadku aplikacji, które nie przetwarzają żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="59419-628">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="59419-629">Przeznaczeniem hosta ogólnego jest oddzielenie potoku HTTP od interfejsu API hosta sieci Web w celu umożliwienia szerszej tablicy scenariuszy hostów.</span><span class="sxs-lookup"><span data-stu-id="59419-629">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="59419-630">Obsługa komunikatów, zadań w tle i innych obciążeń innych niż HTTP na podstawie ogólnej korzyści z hosta, takich jak konfiguracja, iniekcja zależności (DI) i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="59419-630">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="59419-631">Host ogólny jest nowy w ASP.NET Core 2,1 i nie jest odpowiedni dla scenariuszy hostingu w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="59419-631">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="59419-632">W przypadku scenariuszy hostingu w sieci Web należy użyć [hosta sieci Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="59419-632">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="59419-633">Host ogólny zastąpi hosta sieci Web w przyszłej wersji i będzie pełnić rolę podstawowego interfejsu API hosta zarówno w scenariuszach HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="59419-633">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="59419-634">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="59419-634">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="59419-635">Podczas uruchamiania przykładowej aplikacji w [Visual Studio Code](https://code.visualstudio.com/)należy użyć *zewnętrznego lub zintegrowanego terminalu*.</span><span class="sxs-lookup"><span data-stu-id="59419-635">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="59419-636">Nie uruchamiaj próbki w `internalConsole` .</span><span class="sxs-lookup"><span data-stu-id="59419-636">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="59419-637">Aby ustawić konsolę w Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="59419-637">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="59419-638">Otwórz plik *. programu vscode/launch.jsw* pliku.</span><span class="sxs-lookup"><span data-stu-id="59419-638">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="59419-639">W konfiguracji **uruchamiania programu .NET Core (konsoli)** zlokalizuj wpis **konsoli** .</span><span class="sxs-lookup"><span data-stu-id="59419-639">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="59419-640">Ustaw wartość na `externalTerminal` lub `integratedTerminal` .</span><span class="sxs-lookup"><span data-stu-id="59419-640">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="59419-641">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="59419-641">Introduction</span></span>

<span data-ttu-id="59419-642">Ogólna Biblioteka hostów jest dostępna w <xref:Microsoft.Extensions.Hosting> przestrzeni nazw i udostępniana przez pakiet [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) .</span><span class="sxs-lookup"><span data-stu-id="59419-642">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="59419-643">`Microsoft.Extensions.Hosting`Pakiet jest zawarty w [pakiecie Microsoft. AspNetCore. appbinding](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 lub nowszym).</span><span class="sxs-lookup"><span data-stu-id="59419-643">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="59419-644"><xref:Microsoft.Extensions.Hosting.IHostedService> jest punktem wejścia do wykonania kodu.</span><span class="sxs-lookup"><span data-stu-id="59419-644"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="59419-645">Każda <xref:Microsoft.Extensions.Hosting.IHostedService> implementacja jest wykonywana w kolejności [rejestracji usługi w ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="59419-645">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="59419-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> jest wywoływana przy każdym <xref:Microsoft.Extensions.Hosting.IHostedService> uruchomieniu hosta i <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> jest wywoływana w odwrotnej kolejności rejestracji, gdy host jest zamykany prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="59419-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="59419-647">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="59419-647">Set up a host</span></span>

<span data-ttu-id="59419-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> jest głównym składnikiem używanym przez biblioteki i aplikacje do inicjowania, kompilowania i uruchamiania hosta:</span><span class="sxs-lookup"><span data-stu-id="59419-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="59419-649">Opcje</span><span class="sxs-lookup"><span data-stu-id="59419-649">Options</span></span>

<span data-ttu-id="59419-650"><xref:Microsoft.Extensions.Hosting.HostOptions> Skonfiguruj opcje <xref:Microsoft.Extensions.Hosting.IHost> .</span><span class="sxs-lookup"><span data-stu-id="59419-650"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="59419-651">Limit czasu zamykania</span><span class="sxs-lookup"><span data-stu-id="59419-651">Shutdown timeout</span></span>

<span data-ttu-id="59419-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ustawia limit czasu dla <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="59419-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="59419-653">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="59419-653">The default value is five seconds.</span></span>

<span data-ttu-id="59419-654">Następująca konfiguracja opcji w programie `Program.Main` powoduje zwiększenie domyślnego limitu czasu zamykania pięciu sekund:</span><span class="sxs-lookup"><span data-stu-id="59419-654">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="59419-655">Usługi domyślne</span><span class="sxs-lookup"><span data-stu-id="59419-655">Default services</span></span>

<span data-ttu-id="59419-656">Podczas inicjowania hosta zarejestrowano następujące usługi:</span><span class="sxs-lookup"><span data-stu-id="59419-656">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="59419-657">[Środowisko](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="59419-657">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="59419-658">[Konfiguracja](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="59419-658">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="59419-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="59419-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="59419-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="59419-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="59419-661">[Opcje](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="59419-661">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="59419-662">[Rejestrowanie](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="59419-662">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="59419-663">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="59419-663">Host configuration</span></span>

<span data-ttu-id="59419-664">Konfiguracja hosta jest tworzona przez:</span><span class="sxs-lookup"><span data-stu-id="59419-664">Host configuration is created by:</span></span>

* <span data-ttu-id="59419-665">Wywoływanie metod rozszerzenia w <xref:Microsoft.Extensions.Hosting.IHostBuilder> celu ustawienia [katalogu głównego zawartości](#content-root) i [środowiska](#environment).</span><span class="sxs-lookup"><span data-stu-id="59419-665">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="59419-666">Odczytywanie konfiguracji od dostawców konfiguracji w programie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="59419-666">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="59419-667">Metody rozszerzeń</span><span class="sxs-lookup"><span data-stu-id="59419-667">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="59419-668">Klucz aplikacji (nazwa)</span><span class="sxs-lookup"><span data-stu-id="59419-668">Application key (name)</span></span>

<span data-ttu-id="59419-669">Właściwość [IHostingEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) jest ustawiana na podstawie konfiguracji hosta podczas konstruowania hosta.</span><span class="sxs-lookup"><span data-stu-id="59419-669">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="59419-670">Aby jawnie ustawić wartość, użyj [HostDefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="59419-670">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="59419-671">**Klucz**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="59419-671">**Key**: `applicationName`</span></span>  
<span data-ttu-id="59419-672">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-672">**Type**: `string`</span></span>  
<span data-ttu-id="59419-673">**Wartość domyślna**: Nazwa zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-673">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="59419-674">**Ustaw przy użyciu**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="59419-674">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="59419-675">**Zmienna środowiskowa**: `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` jest [opcjonalne i zdefiniowane przez użytkownika](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="59419-675">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="59419-676">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="59419-676">Content root</span></span>

<span data-ttu-id="59419-677">To ustawienie określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="59419-677">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="59419-678">**Klucz**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="59419-678">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="59419-679">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-679">**Type**: `string`</span></span>  
<span data-ttu-id="59419-680">**Domyślnie**: Domyślnie folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-680">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="59419-681">**Ustaw przy użyciu**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="59419-681">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="59419-682">**Zmienna środowiskowa**: `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` jest [opcjonalne i zdefiniowane przez użytkownika](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="59419-682">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="59419-683">Jeśli ścieżka nie istnieje, uruchomienie hosta nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="59419-683">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="59419-684">Aby uzyskać więcej informacji, zobacz temat [podstawy: zawartość główna](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="59419-684">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="59419-685">Środowisko</span><span class="sxs-lookup"><span data-stu-id="59419-685">Environment</span></span>

<span data-ttu-id="59419-686">Ustawia [środowisko](xref:fundamentals/environments)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-686">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="59419-687">**Klucz**: `environment`</span><span class="sxs-lookup"><span data-stu-id="59419-687">**Key**: `environment`</span></span>  
<span data-ttu-id="59419-688">**Typ**: `string`</span><span class="sxs-lookup"><span data-stu-id="59419-688">**Type**: `string`</span></span>  
<span data-ttu-id="59419-689">**Wartość domyślna**: `Production`</span><span class="sxs-lookup"><span data-stu-id="59419-689">**Default**: `Production`</span></span>  
<span data-ttu-id="59419-690">**Ustaw przy użyciu**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="59419-690">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="59419-691">**Zmienna środowiskowa**: `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` jest [opcjonalne i zdefiniowane przez użytkownika](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="59419-691">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="59419-692">Dla środowiska można ustawić dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="59419-692">The environment can be set to any value.</span></span> <span data-ttu-id="59419-693">Wartości zdefiniowane przez platformę obejmują `Development` , `Staging` , i `Production` .</span><span class="sxs-lookup"><span data-stu-id="59419-693">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="59419-694">W wartościach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="59419-694">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="59419-695">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="59419-695">ConfigureHostConfiguration</span></span>

<span data-ttu-id="59419-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> za pomocą programu <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> można utworzyć <xref:Microsoft.Extensions.Configuration.IConfiguration> dla hosta.</span><span class="sxs-lookup"><span data-stu-id="59419-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="59419-697">Konfiguracja hosta służy do inicjowania do <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> użycia w procesie kompilacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-697">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="59419-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="59419-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="59419-699">Host używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="59419-699">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="59419-700">Żaden dostawca nie jest domyślnie uwzględniany.</span><span class="sxs-lookup"><span data-stu-id="59419-700">No providers are included by default.</span></span> <span data-ttu-id="59419-701">Należy jawnie określić dostawców konfiguracji, których wymaga aplikacja, w <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> tym:</span><span class="sxs-lookup"><span data-stu-id="59419-701">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="59419-702">Konfiguracja pliku (na przykład z *hostsettings.jsw* pliku).</span><span class="sxs-lookup"><span data-stu-id="59419-702">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="59419-703">Konfiguracja zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="59419-703">Environment variable configuration.</span></span>
* <span data-ttu-id="59419-704">Konfiguracja argumentu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="59419-704">Command-line argument configuration.</span></span>
* <span data-ttu-id="59419-705">Każdy inny wymagany dostawca konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="59419-705">Any other required configuration providers.</span></span>

<span data-ttu-id="59419-706">Konfiguracja pliku hosta jest włączana przez określenie ścieżki podstawowej aplikacji, `SetBasePath` po której następuje wywołanie jednego z [dostawców konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="59419-706">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="59419-707">Przykładowa aplikacja używa pliku JSON, *hostsettings.json* i wywołuje <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> do użycia ustawienia konfiguracji hosta pliku.</span><span class="sxs-lookup"><span data-stu-id="59419-707">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="59419-708">Aby dodać [konfigurację zmiennej środowiskowej](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hosta, wywołaj polecenie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> na konstruktorze hosta.</span><span class="sxs-lookup"><span data-stu-id="59419-708">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="59419-709">`AddEnvironmentVariables` akceptuje opcjonalny prefiks zdefiniowany przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="59419-709">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="59419-710">Przykładowa aplikacja używa prefiksu `PREFIX_` .</span><span class="sxs-lookup"><span data-stu-id="59419-710">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="59419-711">Prefiks jest usuwany, gdy są odczytywane zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="59419-711">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="59419-712">Po skonfigurowaniu hosta przykładowej aplikacji wartość zmiennej środowiskowej dla `PREFIX_ENVIRONMENT` klucza będzie wartością konfiguracji hosta `environment` .</span><span class="sxs-lookup"><span data-stu-id="59419-712">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="59419-713">Podczas tworzenia w przypadku korzystania z [programu Visual Studio](https://visualstudio.microsoft.com) lub uruchamiania aplikacji w programie `dotnet run` zmienne środowiskowe mogą być ustawiane we *właściwościach/launchSettings.jsw* pliku.</span><span class="sxs-lookup"><span data-stu-id="59419-713">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="59419-714">W [Visual Studio Code](https://code.visualstudio.com/)zmienne środowiskowe mogą być ustawiane w pliku *. programu vscode/launch.js* podczas opracowywania.</span><span class="sxs-lookup"><span data-stu-id="59419-714">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="59419-715">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="59419-715">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="59419-716">[Konfiguracja wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) jest dodawana przez wywołanie <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="59419-716">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="59419-717">Konfiguracja wiersza polecenia jest dodawana jako Ostatnia, aby zezwolić na argumenty wiersza polecenia w celu przesłonięcia konfiguracji udostępnionej przez wcześniejszych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="59419-717">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="59419-718">*hostsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="59419-718">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="59419-719">Dodatkową konfigurację można uzyskać za pomocą programu [ApplicationName](#application-key-name) i kluczy [contentRoot](#content-root) .</span><span class="sxs-lookup"><span data-stu-id="59419-719">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="59419-720">Przykładowa `HostBuilder` Konfiguracja przy użyciu <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="59419-720">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="59419-721">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="59419-721">ConfigureAppConfiguration</span></span>

<span data-ttu-id="59419-722">Konfiguracja aplikacji jest tworzona przez wywołanie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementacji.</span><span class="sxs-lookup"><span data-stu-id="59419-722">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="59419-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> za pomocą programu <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> można utworzyć <xref:Microsoft.Extensions.Configuration.IConfiguration> dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="59419-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="59419-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="59419-725">Aplikacja używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="59419-725">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="59419-726">Konfiguracja utworzona przez program <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> jest dostępna w [HostBuilderContext.Configwersja](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) na potrzeby kolejnych operacji i w <xref:Microsoft.Extensions.Hosting.IHost.Services*> .</span><span class="sxs-lookup"><span data-stu-id="59419-726">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="59419-727">Konfiguracja aplikacji automatycznie otrzymuje konfigurację hosta dostarczoną przez [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="59419-727">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="59419-728">Przykładowa konfiguracja aplikacji przy użyciu <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="59419-728">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="59419-729">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="59419-729">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="59419-730">*appsettings.Development.js*:</span><span class="sxs-lookup"><span data-stu-id="59419-730">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="59419-731">*appsettings.Production.js*:</span><span class="sxs-lookup"><span data-stu-id="59419-731">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="59419-732">Aby przenieść pliki ustawień do katalogu wyjściowego, określ pliki ustawień jako [elementy projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="59419-732">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="59419-733">Aplikacja Przykładowa przenosi pliki ustawień aplikacji JSON i *hostsettings.js* przy użyciu następującego `<Content>` elementu:</span><span class="sxs-lookup"><span data-stu-id="59419-733">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="59419-734">Metody rozszerzenia konfiguracji, takie jak <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> i <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> wymagające dodatkowych pakietów NuGet, takie jak [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) i [Microsoft.Extensions.Configwersja. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="59419-734">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="59419-735">Jeśli aplikacja nie używa [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), należy dodać te pakiety do projektu oprócz podstawowego pakietu [Microsoft.Extensions.Configwersja](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) .</span><span class="sxs-lookup"><span data-stu-id="59419-735">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="59419-736">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="59419-736">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="59419-737">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="59419-737">ConfigureServices</span></span>

<span data-ttu-id="59419-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> dodaje usługi do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="59419-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="59419-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="59419-740">Usługa hostowana jest klasą z logiką zadań w tle, która implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> interfejs.</span><span class="sxs-lookup"><span data-stu-id="59419-740">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="59419-741">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="59419-741">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="59419-742">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) używa `AddHostedService` metody rozszerzającej, aby dodać usługę dla zdarzeń okresu istnienia, `LifetimeEventsHostedService` oraz zadanie w tle czasu, `TimedHostedService` do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="59419-742">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="59419-743">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="59419-743">ConfigureLogging</span></span>

<span data-ttu-id="59419-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> dodaje delegata do konfigurowania podanego elementu <xref:Microsoft.Extensions.Logging.ILoggingBuilder> .</span><span class="sxs-lookup"><span data-stu-id="59419-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="59419-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="59419-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="59419-746">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="59419-746">UseConsoleLifetime</span></span>

<span data-ttu-id="59419-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>nasłuchuje <kbd>kombinacji klawiszy CTRL</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM oraz wywołań, <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> Aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="59419-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="59419-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="59419-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="59419-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` jest wstępnie zarejestrowany jako domyślna implementacja okresu istnienia.</span><span class="sxs-lookup"><span data-stu-id="59419-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="59419-750">Używany jest ostatni zarejestrowany okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="59419-750">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="59419-751">Konfiguracja kontenera</span><span class="sxs-lookup"><span data-stu-id="59419-751">Container configuration</span></span>

<span data-ttu-id="59419-752">Aby zapewnić obsługę podłączania w innych kontenerach, host może akceptować <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601> .</span><span class="sxs-lookup"><span data-stu-id="59419-752">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="59419-753">Dostarczenie fabryki nie jest częścią rejestracji typu DI Container, ale zamiast tego jest wewnętrznym hostem używanym do tworzenia konkretnych kontenerów DI.</span><span class="sxs-lookup"><span data-stu-id="59419-753">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="59419-754">[UseServiceProviderFactory (IServiceProviderFactory &lt; TContainerBuilder &gt; )](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) zastępuje domyślną fabrykę używaną do utworzenia dostawcy usług aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="59419-755">Niestandardowa konfiguracja kontenera jest zarządzana przez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> metodę.</span><span class="sxs-lookup"><span data-stu-id="59419-755">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="59419-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> Program zapewnia silnie wpisaną funkcję konfigurowania kontenera na podstawie podstawowego interfejsu API hosta.</span><span class="sxs-lookup"><span data-stu-id="59419-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="59419-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="59419-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="59419-758">Utwórz kontener usługi dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="59419-758">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="59419-759">Podaj fabrykę kontenera usługi:</span><span class="sxs-lookup"><span data-stu-id="59419-759">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="59419-760">Użyj fabryki i skonfiguruj niestandardowy kontener usługi dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="59419-760">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="59419-761">Rozszerzalność</span><span class="sxs-lookup"><span data-stu-id="59419-761">Extensibility</span></span>

<span data-ttu-id="59419-762">Rozszerzalność hosta jest wykonywana z metodami rozszerzenia w systemie <xref:Microsoft.Extensions.Hosting.IHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="59419-762">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="59419-763">Poniższy przykład pokazuje, jak Metoda rozszerzania rozszerza <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementację z przykładem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) przedstawionym w <xref:fundamentals/host/hosted-services> .</span><span class="sxs-lookup"><span data-stu-id="59419-763">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="59419-764">Aplikacja ustanowi `UseHostedService` metodę rozszerzenia w celu zarejestrowania przesyłanej usługi hostowanej `T` :</span><span class="sxs-lookup"><span data-stu-id="59419-764">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="59419-765">Zarządzanie hostem</span><span class="sxs-lookup"><span data-stu-id="59419-765">Manage the host</span></span>

<span data-ttu-id="59419-766"><xref:Microsoft.Extensions.Hosting.IHost>Implementacja jest odpowiedzialna za uruchamianie i zatrzymywanie <xref:Microsoft.Extensions.Hosting.IHostedService> implementacji, które są zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="59419-766">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="59419-767">Uruchom</span><span class="sxs-lookup"><span data-stu-id="59419-767">Run</span></span>

<span data-ttu-id="59419-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uruchamia aplikację i blokuje wątek wywołujący do momentu wyłączenia hosta:</span><span class="sxs-lookup"><span data-stu-id="59419-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="59419-769">RunAsync</span><span class="sxs-lookup"><span data-stu-id="59419-769">RunAsync</span></span>

<span data-ttu-id="59419-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uruchamia aplikację i zwraca <xref:System.Threading.Tasks.Task> , która kończy się po wyzwoleniu tokenu anulowania lub zamknięcia:</span><span class="sxs-lookup"><span data-stu-id="59419-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="59419-771">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="59419-771">RunConsoleAsync</span></span>

<span data-ttu-id="59419-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>włącza obsługę konsoli, kompiluje i uruchamia hosta, a następnie czeka na zamknięcie programu <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="59419-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="59419-773">Uruchom i StopAsync</span><span class="sxs-lookup"><span data-stu-id="59419-773">Start and StopAsync</span></span>

<span data-ttu-id="59419-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="59419-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="59419-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> próbuje zatrzymać hosta w ramach podanego limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="59419-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="59419-776">StartAsync i StopAsync</span><span class="sxs-lookup"><span data-stu-id="59419-776">StartAsync and StopAsync</span></span>

<span data-ttu-id="59419-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="59419-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="59419-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> kończy działanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="59419-779">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="59419-779">WaitForShutdown</span></span>

<span data-ttu-id="59419-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>jest wyzwalany za pośrednictwem, na przykład <xref:Microsoft.Extensions.Hosting.IHostLifetime> `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (nasłuchuje dla <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="59419-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="59419-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> wywołania <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="59419-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="59419-782">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="59419-782">WaitForShutdownAsync</span></span>

<span data-ttu-id="59419-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Zwraca wartość <xref:System.Threading.Tasks.Task> , która kończy się, gdy zamknięcie jest wyzwalane za pośrednictwem danego tokenu i wywołań <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="59419-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="59419-784">Zewnętrzna kontrola</span><span class="sxs-lookup"><span data-stu-id="59419-784">External control</span></span>

<span data-ttu-id="59419-785">Kontrolę zewnętrzną hosta można osiągnąć przy użyciu metod, które mogą być wywoływane zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="59419-785">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="59419-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> jest wywoływana na początku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> , który czeka na zakończenie przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="59419-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="59419-787">Może to służyć do opóźnienia uruchamiania do momentu zasygnalizowania zdarzenia zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="59419-787">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="59419-788">IHostingEnvironment, interfejs</span><span class="sxs-lookup"><span data-stu-id="59419-788">IHostingEnvironment interface</span></span>

<span data-ttu-id="59419-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> zawiera informacje o środowisku hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="59419-790">Użyj [iniekcji konstruktorów](xref:fundamentals/dependency-injection) , aby uzyskać <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> w celu użycia jej właściwości i metod rozszerzających:</span><span class="sxs-lookup"><span data-stu-id="59419-790">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="59419-791">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="59419-791">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="59419-792">IApplicationLifetime, interfejs</span><span class="sxs-lookup"><span data-stu-id="59419-792">IApplicationLifetime interface</span></span>

<span data-ttu-id="59419-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> zezwala na działania po uruchomieniu i zamknięcia, w tym bezpieczne żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="59419-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="59419-794">Trzy właściwości interfejsu są tokenami anulowania używanymi do rejestrowania <xref:System.Action> metod, które definiują zdarzenia uruchamiania i zamykania.</span><span class="sxs-lookup"><span data-stu-id="59419-794">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="59419-795">Token anulowania</span><span class="sxs-lookup"><span data-stu-id="59419-795">Cancellation Token</span></span> | <span data-ttu-id="59419-796">Wyzwalane, gdy&#8230;</span><span class="sxs-lookup"><span data-stu-id="59419-796">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="59419-797">Host został w pełni uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="59419-797">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="59419-798">Host kończy bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="59419-798">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="59419-799">Wszystkie żądania powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="59419-799">All requests should be processed.</span></span> <span data-ttu-id="59419-800">Bloki zamknięcia do momentu zakończenia tego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="59419-800">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="59419-801">Host wykonuje bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="59419-801">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="59419-802">Żądania mogą nadal być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="59419-802">Requests may still be processing.</span></span> <span data-ttu-id="59419-803">Bloki zamknięcia do momentu zakończenia tego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="59419-803">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="59419-804">Konstruktor — wstrzyknięcie <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> usługi do dowolnej klasy.</span><span class="sxs-lookup"><span data-stu-id="59419-804">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="59419-805">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) używa iniekcji konstruktora do `LifetimeEventsHostedService` klasy ( <xref:Microsoft.Extensions.Hosting.IHostedService> implementacji), aby zarejestrować zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="59419-805">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="59419-806">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="59419-806">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="59419-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> żąda zakończenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="59419-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="59419-808">Następująca Klasa służy <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> do bezpiecznego zamykania aplikacji w przypadku `Shutdown` wywołania metody klasy:</span><span class="sxs-lookup"><span data-stu-id="59419-808">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="59419-809">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="59419-809">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
