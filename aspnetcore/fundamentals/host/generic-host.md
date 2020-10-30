---
title: Host ogólny .NET w ASP.NET Core
author: rick-anderson
description: Użyj hosta ogólnego platformy .NET Core w aplikacjach ASP.NET Core.  Host ogólny jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/host/generic-host
ms.openlocfilehash: 3020734917fbf4d093420ad99114633d04e2a31b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060498"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="28f0f-104">Host ogólny .NET w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="28f0f-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="28f0f-105">Szablony ASP.NET Core tworzą hosta ogólnego platformy .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="28f0f-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="28f0f-106">Ten temat zawiera informacje dotyczące korzystania z hosta ogólnego platformy .NET w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28f0f-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="28f0f-107">Aby uzyskać informacje na temat korzystania z hosta ogólnego platformy .NET w aplikacjach konsolowych, zobacz [host ogólny programu .NET](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="28f0f-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="28f0f-108">Definicja hosta</span><span class="sxs-lookup"><span data-stu-id="28f0f-108">Host definition</span></span>

<span data-ttu-id="28f0f-109">*Host* to obiekt, który hermetyzuje zasoby aplikacji, takie jak:</span><span class="sxs-lookup"><span data-stu-id="28f0f-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="28f0f-110">Iniekcja zależności (DI)</span><span class="sxs-lookup"><span data-stu-id="28f0f-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="28f0f-111">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="28f0f-111">Logging</span></span>
* <span data-ttu-id="28f0f-112">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="28f0f-112">Configuration</span></span>
* <span data-ttu-id="28f0f-113">`IHostedService` metod</span><span class="sxs-lookup"><span data-stu-id="28f0f-113">`IHostedService` implementations</span></span>

<span data-ttu-id="28f0f-114">Po uruchomieniu hosta jest on wywoływany <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> dla każdej implementacji <xref:Microsoft.Extensions.Hosting.IHostedService> zarejestrowanej w kolekcji kontenera usługi hostowanej usług.</span><span class="sxs-lookup"><span data-stu-id="28f0f-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="28f0f-115">W aplikacji sieci Web jedną z `IHostedService` implementacji jest usługa sieci Web, która uruchamia [implementację serwera http](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="28f0f-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="28f0f-116">Główną przyczyną uwzględnienia wszystkich zasobów zależnych od aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: Kontrola uruchamiania aplikacji i bezpieczne zamykanie.</span><span class="sxs-lookup"><span data-stu-id="28f0f-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="28f0f-117">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="28f0f-117">Set up a host</span></span>

<span data-ttu-id="28f0f-118">Host jest zazwyczaj konfigurowany, zbudowany i uruchamiany przez kod w `Program` klasie.</span><span class="sxs-lookup"><span data-stu-id="28f0f-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="28f0f-119">`Main`Metoda:</span><span class="sxs-lookup"><span data-stu-id="28f0f-119">The `Main` method:</span></span>

* <span data-ttu-id="28f0f-120">Wywołuje `CreateHostBuilder` metodę, aby utworzyć i skonfigurować obiekt konstruktora.</span><span class="sxs-lookup"><span data-stu-id="28f0f-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="28f0f-121">Wywołania `Build` i `Run` metody w obiekcie konstruktora.</span><span class="sxs-lookup"><span data-stu-id="28f0f-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="28f0f-122">Szablony sieci Web ASP.NET Core generują następujący kod w celu utworzenia hosta:</span><span class="sxs-lookup"><span data-stu-id="28f0f-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="28f0f-123">Poniższy kod tworzy obciążenie inne niż HTTP z `IHostedService` implementacją dodaną do kontenera di.</span><span class="sxs-lookup"><span data-stu-id="28f0f-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="28f0f-124">W przypadku obciążeń HTTP `Main` Metoda jest taka sama, ale `CreateHostBuilder` wywołuje `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="28f0f-125">Jeśli aplikacja używa Entity Framework Core, nie zmieniaj nazwy ani podpisu `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="28f0f-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="28f0f-126">[Narzędzia Entity Framework Core](/ef/core/miscellaneous/cli/) oczekują na znalezienie `CreateHostBuilder` metody, która konfiguruje hosta bez uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="28f0f-127">Aby uzyskać więcej informacji, zobacz [Tworzenie DbContext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="28f0f-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="28f0f-128">Ustawienia domyślnego konstruktora</span><span class="sxs-lookup"><span data-stu-id="28f0f-128">Default builder settings</span></span>

<span data-ttu-id="28f0f-129"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="28f0f-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="28f0f-130">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na ścieżkę zwracaną przez <xref:System.IO.Directory.GetCurrentDirectory*> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="28f0f-131">Ładuje konfigurację hosta z:</span><span class="sxs-lookup"><span data-stu-id="28f0f-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="28f0f-132">Zmienne środowiskowe poprzedzone prefiksem `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="28f0f-133">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-133">Command-line arguments.</span></span>
* <span data-ttu-id="28f0f-134">Ładuje konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="28f0f-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="28f0f-135">*:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="28f0f-135">*:::no-loc(appsettings.json):::* .</span></span>
  * <span data-ttu-id="28f0f-136">*appSettings. {Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="28f0f-136">*appsettings.{Environment}.json* .</span></span>
  * <span data-ttu-id="28f0f-137">[Secret Manager](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku.</span><span class="sxs-lookup"><span data-stu-id="28f0f-137">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="28f0f-138">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="28f0f-138">Environment variables.</span></span>
  * <span data-ttu-id="28f0f-139">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-139">Command-line arguments.</span></span>
* <span data-ttu-id="28f0f-140">Dodaje następujących dostawców [rejestrowania](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="28f0f-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="28f0f-141">Konsola</span><span class="sxs-lookup"><span data-stu-id="28f0f-141">Console</span></span>
  * <span data-ttu-id="28f0f-142">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="28f0f-142">Debug</span></span>
  * <span data-ttu-id="28f0f-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="28f0f-143">EventSource</span></span>
  * <span data-ttu-id="28f0f-144">EventLog (tylko w przypadku uruchamiania w systemie Windows)</span><span class="sxs-lookup"><span data-stu-id="28f0f-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="28f0f-145">Umożliwia [weryfikację zakresu](xref:fundamentals/dependency-injection#scope-validation) i [Sprawdzanie poprawności zależności](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) , gdy środowisko jest opracowywane.</span><span class="sxs-lookup"><span data-stu-id="28f0f-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="28f0f-146">`ConfigureWebHostDefaults`Metoda:</span><span class="sxs-lookup"><span data-stu-id="28f0f-146">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="28f0f-147">Ładuje konfigurację hosta ze zmiennych środowiskowych, które są poprzedzone prefiksem `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="28f0f-148">Ustawia serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci Web i konfiguruje go przy użyciu dostawców konfiguracji hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="28f0f-149">Aby poznać domyślne opcje serwera Kestrel, zobacz <xref:fundamentals/servers/kestrel#kestrel-options> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="28f0f-150">Dodaje [oprogramowanie pośredniczące do filtrowania hosta](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="28f0f-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="28f0f-151">Dodaje [przekazane nagłówki — oprogramowanie pośredniczące](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest równe `true` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="28f0f-152">Włącza integrację usług IIS.</span><span class="sxs-lookup"><span data-stu-id="28f0f-152">Enables IIS integration.</span></span> <span data-ttu-id="28f0f-153">Aby poznać domyślne opcje usług IIS, zobacz <xref:host-and-deploy/iis/index#iis-options> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="28f0f-154">[Ustawienia dla wszystkich typów aplikacji](#settings-for-all-app-types) i [Ustawienia dla usługi Web Apps](#settings-for-web-apps) w dalszej części tego artykułu pokazują, jak zastąpić ustawienia domyślnego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="28f0f-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="28f0f-155">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="28f0f-155">Framework-provided services</span></span>

<span data-ttu-id="28f0f-156">Następujące usługi są rejestrowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="28f0f-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="28f0f-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="28f0f-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="28f0f-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="28f0f-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="28f0f-159">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="28f0f-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="28f0f-160">Aby uzyskać więcej informacji na temat usług udostępnianych przez platformę, zobacz <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="28f0f-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="28f0f-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="28f0f-162">Wstrzyknąć <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> usługę (dawniej `IApplicationLifetime` ) do dowolnej klasy w celu obsługi zadań po uruchomieniu i bezpiecznego zamykania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="28f0f-163">Trzy właściwości interfejsu to tokeny anulowania używane do rejestrowania metod obsługi zdarzeń uruchamiania i zatrzymywania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="28f0f-164">Interfejs zawiera również `StopApplication` metodę.</span><span class="sxs-lookup"><span data-stu-id="28f0f-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="28f0f-165">Poniższy przykład to `IHostedService` implementacja, która rejestruje `IHostApplicationLifetime` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="28f0f-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="28f0f-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="28f0f-166">IHostLifetime</span></span>

<span data-ttu-id="28f0f-167"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Implementacja kontroluje moment uruchomienia hosta i jego zatrzymania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="28f0f-168">Używana jest Ostatnia zarejestrowana implementacja.</span><span class="sxs-lookup"><span data-stu-id="28f0f-168">The last implementation registered is used.</span></span>

<span data-ttu-id="28f0f-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` jest `IHostLifetime` implementacją domyślną.</span><span class="sxs-lookup"><span data-stu-id="28f0f-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="28f0f-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="28f0f-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="28f0f-171">Nasłuchuje <kbd>kombinacji klawiszy CTRL</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM oraz wywołań, <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> Aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="28f0f-172">Odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="28f0f-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="28f0f-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="28f0f-173">IHostEnvironment</span></span>

<span data-ttu-id="28f0f-174">Wsuń <xref:Microsoft.Extensions.Hosting.IHostEnvironment> usługę do klasy, aby uzyskać informacje o następujących ustawieniach:</span><span class="sxs-lookup"><span data-stu-id="28f0f-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="28f0f-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="28f0f-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="28f0f-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="28f0f-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="28f0f-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="28f0f-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="28f0f-178">Aplikacje sieci Web implementują `IWebHostEnvironment` interfejs, który dziedziczy `IHostEnvironment` i dodaje [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="28f0f-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="28f0f-179">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="28f0f-179">Host configuration</span></span>

<span data-ttu-id="28f0f-180">Konfiguracja hosta jest używana we właściwościach <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="28f0f-181">Konfiguracja hosta jest dostępna w [HostBuilderContext.Configwersja](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="28f0f-182">Po `ConfigureAppConfiguration` program `HostBuilderContext.Configuration` zostanie zastąpiony konfiguracją aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="28f0f-183">Aby dodać konfigurację hosta, wywołaj polecenie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="28f0f-184">`ConfigureHostConfiguration` może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="28f0f-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="28f0f-185">Host używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="28f0f-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="28f0f-186">Dostawca zmiennych środowiskowych z prefiksami `DOTNET_` i argumentami wiersza polecenia są uwzględniane przez `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="28f0f-187">W przypadku aplikacji sieci Web jest dodawany dostawca zmiennych środowiskowych z prefiksem `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="28f0f-188">Prefiks jest usuwany, gdy są odczytywane zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="28f0f-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="28f0f-189">Na przykład wartość zmiennej środowiskowej jest `ASPNETCORE_ENVIRONMENT` wartością konfiguracji hosta dla `environment` klucza.</span><span class="sxs-lookup"><span data-stu-id="28f0f-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="28f0f-190">Poniższy przykład umożliwia utworzenie konfiguracji hosta:</span><span class="sxs-lookup"><span data-stu-id="28f0f-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="28f0f-191">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="28f0f-191">App configuration</span></span>

<span data-ttu-id="28f0f-192">Konfiguracja aplikacji jest tworzona przez wywołanie metody <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="28f0f-193">`ConfigureAppConfiguration` może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="28f0f-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="28f0f-194">Aplikacja używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="28f0f-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="28f0f-195">Konfiguracja utworzona przez program `ConfigureAppConfiguration` jest dostępna w [HostBuilderContext.Configwersja](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) na potrzeby kolejnych operacji i jako usługa z di.</span><span class="sxs-lookup"><span data-stu-id="28f0f-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="28f0f-196">Konfiguracja hosta jest również dodawana do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="28f0f-197">Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="28f0f-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="28f0f-198">Ustawienia dla wszystkich typów aplikacji</span><span class="sxs-lookup"><span data-stu-id="28f0f-198">Settings for all app types</span></span>

<span data-ttu-id="28f0f-199">Ta sekcja zawiera listę ustawień hosta, które dotyczą zarówno obciążeń HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="28f0f-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="28f0f-200">Domyślnie zmienne środowiskowe używane do konfigurowania tych ustawień mogą mieć `DOTNET_` `ASPNETCORE_` prefiks lub.</span><span class="sxs-lookup"><span data-stu-id="28f0f-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="28f0f-201">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="28f0f-201">ApplicationName</span></span>

<span data-ttu-id="28f0f-202">Właściwość [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) jest ustawiana na podstawie konfiguracji hosta podczas konstruowania hosta.</span><span class="sxs-lookup"><span data-stu-id="28f0f-202">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="28f0f-203">**Klucz** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="28f0f-203">**Key** : `applicationName`</span></span>  
<span data-ttu-id="28f0f-204">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-204">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-205">**Wartość domyślna** : Nazwa zestawu, który zawiera punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-205">**Default** : The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="28f0f-206">**Zmienna środowiskowa** : `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="28f0f-206">**Environment variable** : `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="28f0f-207">Aby ustawić tę wartość, należy użyć zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="28f0f-207">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="28f0f-208">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="28f0f-208">ContentRoot</span></span>

<span data-ttu-id="28f0f-209">Właściwość [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="28f0f-209">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="28f0f-210">Jeśli ścieżka nie istnieje, uruchomienie hosta nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="28f0f-210">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="28f0f-211">**Klucz** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="28f0f-211">**Key** : `contentRoot`</span></span>  
<span data-ttu-id="28f0f-212">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-212">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-213">**Domyślnie** : folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-213">**Default** : The folder where the app assembly resides.</span></span>  
<span data-ttu-id="28f0f-214">**Zmienna środowiskowa** : `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="28f0f-214">**Environment variable** : `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="28f0f-215">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseContentRoot` `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-215">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="28f0f-216">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="28f0f-216">For more information, see:</span></span>

* [<span data-ttu-id="28f0f-217">Podstawy: zawartość główna</span><span class="sxs-lookup"><span data-stu-id="28f0f-217">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="28f0f-218">WebRoot</span><span class="sxs-lookup"><span data-stu-id="28f0f-218">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="28f0f-219">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="28f0f-219">EnvironmentName</span></span>

<span data-ttu-id="28f0f-220">Dla właściwości [IHostEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) można ustawić dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="28f0f-220">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="28f0f-221">Wartości zdefiniowane przez platformę obejmują `Development` , `Staging` , i `Production` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-221">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="28f0f-222">W wartościach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="28f0f-222">Values aren't case-sensitive.</span></span>

<span data-ttu-id="28f0f-223">**Klucz** : `environment`</span><span class="sxs-lookup"><span data-stu-id="28f0f-223">**Key** : `environment`</span></span>  
<span data-ttu-id="28f0f-224">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-224">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-225">**Wartość domyślna** : `Production`</span><span class="sxs-lookup"><span data-stu-id="28f0f-225">**Default** : `Production`</span></span>  
<span data-ttu-id="28f0f-226">**Zmienna środowiskowa** : `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="28f0f-226">**Environment variable** : `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="28f0f-227">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseEnvironment` `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-227">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="28f0f-228">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="28f0f-228">ShutdownTimeout</span></span>

<span data-ttu-id="28f0f-229">[HostOptions. shutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) ustawia limit czasu dla <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="28f0f-230">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="28f0f-230">The default value is five seconds.</span></span>  <span data-ttu-id="28f0f-231">Podczas okresu przekroczenia limitu czasu Host:</span><span class="sxs-lookup"><span data-stu-id="28f0f-231">During the timeout period, the host:</span></span>

* <span data-ttu-id="28f0f-232">Wyzwala [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="28f0f-232">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="28f0f-233">Próbuje zatrzymać usługi hostowane, rejestrowanie błędów dla usług, których zatrzymanie nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="28f0f-233">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="28f0f-234">Jeśli limit czasu upłynie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-234">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="28f0f-235">Usługi są zatrzymane nawet wtedy, gdy nie zakończyły przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-235">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="28f0f-236">Jeśli usługi wymagają dodatkowego czasu na zatrzymanie, zwiększ limit czasu.</span><span class="sxs-lookup"><span data-stu-id="28f0f-236">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="28f0f-237">**Klucz** : `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="28f0f-237">**Key** : `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="28f0f-238">**Typ** : `int`</span><span class="sxs-lookup"><span data-stu-id="28f0f-238">**Type** : `int`</span></span>  
<span data-ttu-id="28f0f-239">**Wartość domyślna** : 5 sekund</span><span class="sxs-lookup"><span data-stu-id="28f0f-239">**Default** : 5 seconds</span></span>  
<span data-ttu-id="28f0f-240">**Zmienna środowiskowa** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="28f0f-240">**Environment variable** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="28f0f-241">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub skonfiguruj `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-241">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="28f0f-242">Poniższy przykład ustawia limit czasu na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="28f0f-242">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="28f0f-243">Wyłącz ponowne ładowanie konfiguracji aplikacji przy zmianie</span><span class="sxs-lookup"><span data-stu-id="28f0f-243">Disable app configuration reload on change</span></span>

<span data-ttu-id="28f0f-244">[Domyślnie](xref:fundamentals/configuration/index#default) *:::no-loc(appsettings.json):::* i *appSettings. { Środowisko}. kod JSON* jest ponownie ładowany, gdy plik ulegnie zmianie.</span><span class="sxs-lookup"><span data-stu-id="28f0f-244">By [default](xref:fundamentals/configuration/index#default), *:::no-loc(appsettings.json):::* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="28f0f-245">Aby wyłączyć to zachowanie ponownego ładowania w ASP.NET Core 5,0 lub nowszym, ustaw `hostBuilder:reloadConfigOnChange` klucz na `false` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-245">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="28f0f-246">**Klucz** : `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="28f0f-246">**Key** : `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="28f0f-247">**Typ** : `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="28f0f-247">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="28f0f-248">**Wartość domyślna** : `true`</span><span class="sxs-lookup"><span data-stu-id="28f0f-248">**Default** : `true`</span></span>  
<span data-ttu-id="28f0f-249">**Argument wiersza polecenia** : `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="28f0f-249">**Command-line argument** : `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="28f0f-250">**Zmienna środowiskowa** : `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="28f0f-250">**Environment variable** : `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="28f0f-251">Separator dwukropek ( `:` ) nie działa ze zmiennymi kluczy hierarchicznych na wszystkich platformach.</span><span class="sxs-lookup"><span data-stu-id="28f0f-251">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="28f0f-252">Aby uzyskać więcej informacji, zobacz [zmienne środowiskowe](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="28f0f-252">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="28f0f-253">Ustawienia usługi Web Apps</span><span class="sxs-lookup"><span data-stu-id="28f0f-253">Settings for web apps</span></span>

<span data-ttu-id="28f0f-254">Niektóre ustawienia hosta mają zastosowanie tylko do obciążeń protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="28f0f-254">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="28f0f-255">Domyślnie zmienne środowiskowe używane do konfigurowania tych ustawień mogą mieć `DOTNET_` `ASPNETCORE_` prefiks lub.</span><span class="sxs-lookup"><span data-stu-id="28f0f-255">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="28f0f-256">Metody rozszerzenia dla `IWebHostBuilder` programu są dostępne dla tych ustawień.</span><span class="sxs-lookup"><span data-stu-id="28f0f-256">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="28f0f-257">Przykłady kodu, które pokazują, jak wywołać metody rozszerzane `webBuilder` jest wystąpieniem `IWebHostBuilder` , tak jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="28f0f-257">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="28f0f-258">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="28f0f-258">CaptureStartupErrors</span></span>

<span data-ttu-id="28f0f-259">Gdy `false` , błędy podczas uruchamiania, kończy się hostem.</span><span class="sxs-lookup"><span data-stu-id="28f0f-259">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="28f0f-260">Gdy `true` host przechwytuje wyjątki podczas uruchamiania, a następnie próbuje uruchomić serwer.</span><span class="sxs-lookup"><span data-stu-id="28f0f-260">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="28f0f-261">**Klucz** : `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="28f0f-261">**Key** : `captureStartupErrors`</span></span>  
<span data-ttu-id="28f0f-262">**Typ** : `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="28f0f-262">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="28f0f-263">**Domyślnie** : wartość domyślna to, `false` chyba że aplikacja działa z Kestrel za usługami IIS, gdzie wartość domyślna to `true` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-263">**Default** : Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="28f0f-264">**Zmienna środowiskowa** : `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="28f0f-264">**Environment variable** : `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="28f0f-265">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-265">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="28f0f-266">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="28f0f-266">DetailedErrors</span></span>

<span data-ttu-id="28f0f-267">Po włączeniu lub gdy środowisko jest `Development` , aplikacja przechwytuje szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="28f0f-267">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="28f0f-268">**Klucz** : `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="28f0f-268">**Key** : `detailedErrors`</span></span>  
<span data-ttu-id="28f0f-269">**Typ** : `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="28f0f-269">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="28f0f-270">**Wartość domyślna** : `false`</span><span class="sxs-lookup"><span data-stu-id="28f0f-270">**Default** : `false`</span></span>  
<span data-ttu-id="28f0f-271">**Zmienna środowiskowa** : `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="28f0f-271">**Environment variable** : `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="28f0f-272">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-272">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="28f0f-273">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="28f0f-273">HostingStartupAssemblies</span></span>

<span data-ttu-id="28f0f-274">Rozdzielany średnikami ciąg początkowych zestawów startowych do załadowania podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-274">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="28f0f-275">Mimo że wartość konfiguracji jest domyślnie pustym ciągiem, zestaw startowy obsługujący zawsze zawiera zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-275">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="28f0f-276">W przypadku udostępniania zestawów startowych są one dodawane do zestawu aplikacji do załadowania, gdy aplikacja kompiluje swoje popularne usługi podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-276">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="28f0f-277">**Klucz** : `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="28f0f-277">**Key** : `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="28f0f-278">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-278">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-279">**Wartość domyślna** : pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="28f0f-279">**Default** : Empty string</span></span>  
<span data-ttu-id="28f0f-280">**Zmienna środowiskowa** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="28f0f-280">**Environment variable** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="28f0f-281">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-281">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="28f0f-282">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="28f0f-282">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="28f0f-283">Rozdzielany średnikami ciąg początkowych zestawów uruchamiania, który ma zostać wykluczony podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-283">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="28f0f-284">**Klucz** : `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="28f0f-284">**Key** : `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="28f0f-285">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-285">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-286">**Wartość domyślna** : pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="28f0f-286">**Default** : Empty string</span></span>  
<span data-ttu-id="28f0f-287">**Zmienna środowiskowa** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="28f0f-287">**Environment variable** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="28f0f-288">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-288">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="28f0f-289">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="28f0f-289">HTTPS_Port</span></span>

<span data-ttu-id="28f0f-290">Port przekierowania protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="28f0f-290">The HTTPS redirect port.</span></span> <span data-ttu-id="28f0f-291">Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="28f0f-291">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="28f0f-292">**Klucz** : `https_port`</span><span class="sxs-lookup"><span data-stu-id="28f0f-292">**Key** : `https_port`</span></span>  
<span data-ttu-id="28f0f-293">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-293">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-294">Wartość **Domyślna** : nie ustawiono wartości domyślnej.</span><span class="sxs-lookup"><span data-stu-id="28f0f-294">**Default** : A default value isn't set.</span></span>  
<span data-ttu-id="28f0f-295">**Zmienna środowiskowa** : `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="28f0f-295">**Environment variable** : `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="28f0f-296">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-296">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="28f0f-297">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="28f0f-297">PreferHostingUrls</span></span>

<span data-ttu-id="28f0f-298">Wskazuje, czy host powinien nasłuchiwać adresów URL skonfigurowanych przy użyciu `IWebHostBuilder` zamiast adresów URL skonfigurowanych dla `IServer` implementacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-298">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="28f0f-299">**Klucz** : `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="28f0f-299">**Key** : `preferHostingUrls`</span></span>  
<span data-ttu-id="28f0f-300">**Typ** : `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="28f0f-300">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="28f0f-301">**Wartość domyślna** : `true`</span><span class="sxs-lookup"><span data-stu-id="28f0f-301">**Default** : `true`</span></span>  
<span data-ttu-id="28f0f-302">**Zmienna środowiskowa** : `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="28f0f-302">**Environment variable** : `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="28f0f-303">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-303">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="28f0f-304">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="28f0f-304">PreventHostingStartup</span></span>

<span data-ttu-id="28f0f-305">Zapobiega automatycznemu ładowaniu zestawów startowych hostingu, w tym hostingu zestawów startowych skonfigurowanych przez zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-305">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="28f0f-306">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="28f0f-306">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="28f0f-307">**Klucz** : `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="28f0f-307">**Key** : `preventHostingStartup`</span></span>  
<span data-ttu-id="28f0f-308">**Typ** : `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="28f0f-308">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="28f0f-309">**Wartość domyślna** : `false`</span><span class="sxs-lookup"><span data-stu-id="28f0f-309">**Default** : `false`</span></span>  
<span data-ttu-id="28f0f-310">**Zmienna środowiskowa** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="28f0f-310">**Environment variable** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="28f0f-311">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-311">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="28f0f-312">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="28f0f-312">StartupAssembly</span></span>

<span data-ttu-id="28f0f-313">Zestaw do wyszukiwania `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="28f0f-313">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="28f0f-314">**Klucz** : `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="28f0f-314">**Key** : `startupAssembly`</span></span>  
<span data-ttu-id="28f0f-315">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-315">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-316">**Domyślnie** : zestaw aplikacji</span><span class="sxs-lookup"><span data-stu-id="28f0f-316">**Default** : The app's assembly</span></span>  
<span data-ttu-id="28f0f-317">**Zmienna środowiskowa** : `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="28f0f-317">**Environment variable** : `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="28f0f-318">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołania `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-318">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="28f0f-319">`UseStartup` może przyjmować nazwę zestawu ( `string` ) lub typ ( `TStartup` ).</span><span class="sxs-lookup"><span data-stu-id="28f0f-319">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="28f0f-320">Jeśli `UseStartup` wywoływana jest wiele metod, pierwszeństwo ma Ostatnia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-320">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="28f0f-321">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="28f0f-321">URLs</span></span>

<span data-ttu-id="28f0f-322">Rozdzielana średnikami lista adresów IP lub adresów hostów z portami i protokołami, na których serwer powinien nasłuchiwać żądań.</span><span class="sxs-lookup"><span data-stu-id="28f0f-322">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="28f0f-323">Na przykład `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="28f0f-323">For example, `http://localhost:123`.</span></span> <span data-ttu-id="28f0f-324">Użyj " \* ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwie hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000` ).</span><span class="sxs-lookup"><span data-stu-id="28f0f-324">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="28f0f-325">Protokół ( `http://` lub `https://` ) musi być dołączony do każdego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="28f0f-325">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="28f0f-326">Obsługiwane formaty różnią się między serwerami.</span><span class="sxs-lookup"><span data-stu-id="28f0f-326">Supported formats vary among servers.</span></span>

<span data-ttu-id="28f0f-327">**Klucz** : `urls`</span><span class="sxs-lookup"><span data-stu-id="28f0f-327">**Key** : `urls`</span></span>  
<span data-ttu-id="28f0f-328">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-328">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-329">**Wartość domyślna** : `http://localhost:5000` i `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="28f0f-329">**Default** : `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="28f0f-330">**Zmienna środowiskowa** : `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="28f0f-330">**Environment variable** : `<PREFIX_>URLS`</span></span>

<span data-ttu-id="28f0f-331">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-331">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="28f0f-332">Kestrel ma własny interfejs API konfiguracji punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="28f0f-332">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="28f0f-333">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="28f0f-333">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="28f0f-334">WebRoot</span><span class="sxs-lookup"><span data-stu-id="28f0f-334">WebRoot</span></span>

<span data-ttu-id="28f0f-335">Właściwość [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) określa ścieżkę względną do statycznych zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-335">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="28f0f-336">Jeśli ścieżka nie istnieje, jest używany dostawca plików No-op.</span><span class="sxs-lookup"><span data-stu-id="28f0f-336">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="28f0f-337">**Klucz** : `webroot`</span><span class="sxs-lookup"><span data-stu-id="28f0f-337">**Key** : `webroot`</span></span>  
<span data-ttu-id="28f0f-338">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-338">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-339">**Wartość domyślna** : wartość domyślna to `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-339">**Default** : The default is `wwwroot`.</span></span> <span data-ttu-id="28f0f-340">Ścieżka do *elementu {content root}/wwwroot* musi istnieć.</span><span class="sxs-lookup"><span data-stu-id="28f0f-340">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="28f0f-341">**Zmienna środowiskowa** : `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="28f0f-341">**Environment variable** : `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="28f0f-342">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseWebRoot` `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-342">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="28f0f-343">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="28f0f-343">For more information, see:</span></span>

* [<span data-ttu-id="28f0f-344">Podstawy: katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="28f0f-344">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="28f0f-345">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="28f0f-345">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="28f0f-346">Zarządzanie okresem istnienia hosta</span><span class="sxs-lookup"><span data-stu-id="28f0f-346">Manage the host lifetime</span></span>

<span data-ttu-id="28f0f-347">Wywołaj metody z skompilowanej <xref:Microsoft.Extensions.Hosting.IHost> implementacji, aby uruchomić i zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="28f0f-347">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="28f0f-348">Te metody mają wpływ na wszystkie  <xref:Microsoft.Extensions.Hosting.IHostedService> implementacje zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="28f0f-348">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="28f0f-349">Uruchom</span><span class="sxs-lookup"><span data-stu-id="28f0f-349">Run</span></span>

<span data-ttu-id="28f0f-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uruchamia aplikację i blokuje wątek wywołujący do momentu wyłączenia hosta.</span><span class="sxs-lookup"><span data-stu-id="28f0f-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="28f0f-351">RunAsync</span><span class="sxs-lookup"><span data-stu-id="28f0f-351">RunAsync</span></span>

<span data-ttu-id="28f0f-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uruchamia aplikację i zwraca <xref:System.Threading.Tasks.Task> , która kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="28f0f-353">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="28f0f-353">RunConsoleAsync</span></span>

<span data-ttu-id="28f0f-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>włącza obsługę konsoli, kompiluje i uruchamia hosta, a następnie czeka na zamknięcie programu <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="28f0f-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="28f0f-355">Rozpocznij</span><span class="sxs-lookup"><span data-stu-id="28f0f-355">Start</span></span>

<span data-ttu-id="28f0f-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="28f0f-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="28f0f-357">StartAsync</span><span class="sxs-lookup"><span data-stu-id="28f0f-357">StartAsync</span></span>

<span data-ttu-id="28f0f-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> uruchamia hosta i zwraca <xref:System.Threading.Tasks.Task> , który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="28f0f-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> jest wywoływana na początku `StartAsync` , który czeka na zakończenie przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="28f0f-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="28f0f-360">Może to służyć do opóźnienia uruchamiania do momentu zasygnalizowania zdarzenia zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="28f0f-360">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="28f0f-361">StopAsync</span><span class="sxs-lookup"><span data-stu-id="28f0f-361">StopAsync</span></span>

<span data-ttu-id="28f0f-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> próbuje zatrzymać hosta w ramach podanego limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="28f0f-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="28f0f-363">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="28f0f-363">WaitForShutdown</span></span>

<span data-ttu-id="28f0f-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje wątek wywołujący do momentu wyzwolenia przez IHostLifetime, na przykład przez <kbd>naciśnięcie klawiszy CTRL</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="28f0f-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="28f0f-365">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="28f0f-365">WaitForShutdownAsync</span></span>

<span data-ttu-id="28f0f-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Zwraca wartość <xref:System.Threading.Tasks.Task> , która kończy się, gdy zamknięcie jest wyzwalane za pośrednictwem danego tokenu i wywołań <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="28f0f-367">Zewnętrzna kontrola</span><span class="sxs-lookup"><span data-stu-id="28f0f-367">External control</span></span>

<span data-ttu-id="28f0f-368">Bezpośrednią kontrolę nad okresem istnienia hosta można uzyskać przy użyciu metod, które mogą być wywoływane zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="28f0f-368">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="28f0f-369">Szablony ASP.NET Core tworzą hosta ogólnego platformy .NET Core ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ).</span><span class="sxs-lookup"><span data-stu-id="28f0f-369">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="28f0f-370">Ten temat zawiera informacje dotyczące korzystania z hosta ogólnego platformy .NET w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28f0f-370">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="28f0f-371">Aby uzyskać informacje na temat korzystania z hosta ogólnego platformy .NET w aplikacjach konsolowych, zobacz [host ogólny programu .NET](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="28f0f-371">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="28f0f-372">Definicja hosta</span><span class="sxs-lookup"><span data-stu-id="28f0f-372">Host definition</span></span>

<span data-ttu-id="28f0f-373">*Host* to obiekt, który hermetyzuje zasoby aplikacji, takie jak:</span><span class="sxs-lookup"><span data-stu-id="28f0f-373">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="28f0f-374">Iniekcja zależności (DI)</span><span class="sxs-lookup"><span data-stu-id="28f0f-374">Dependency injection (DI)</span></span>
* <span data-ttu-id="28f0f-375">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="28f0f-375">Logging</span></span>
* <span data-ttu-id="28f0f-376">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="28f0f-376">Configuration</span></span>
* <span data-ttu-id="28f0f-377">`IHostedService` metod</span><span class="sxs-lookup"><span data-stu-id="28f0f-377">`IHostedService` implementations</span></span>

<span data-ttu-id="28f0f-378">Po uruchomieniu hosta jest on wywoływany <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> dla każdej implementacji <xref:Microsoft.Extensions.Hosting.IHostedService> zarejestrowanej w kolekcji kontenera usługi hostowanej usług.</span><span class="sxs-lookup"><span data-stu-id="28f0f-378">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="28f0f-379">W aplikacji sieci Web jedną z `IHostedService` implementacji jest usługa sieci Web, która uruchamia [implementację serwera http](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="28f0f-379">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="28f0f-380">Główną przyczyną uwzględnienia wszystkich zasobów zależnych od aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: Kontrola uruchamiania aplikacji i bezpieczne zamykanie.</span><span class="sxs-lookup"><span data-stu-id="28f0f-380">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="28f0f-381">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="28f0f-381">Set up a host</span></span>

<span data-ttu-id="28f0f-382">Host jest zazwyczaj konfigurowany, zbudowany i uruchamiany przez kod w `Program` klasie.</span><span class="sxs-lookup"><span data-stu-id="28f0f-382">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="28f0f-383">`Main`Metoda:</span><span class="sxs-lookup"><span data-stu-id="28f0f-383">The `Main` method:</span></span>

* <span data-ttu-id="28f0f-384">Wywołuje `CreateHostBuilder` metodę, aby utworzyć i skonfigurować obiekt konstruktora.</span><span class="sxs-lookup"><span data-stu-id="28f0f-384">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="28f0f-385">Wywołania `Build` i `Run` metody w obiekcie konstruktora.</span><span class="sxs-lookup"><span data-stu-id="28f0f-385">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="28f0f-386">Szablony sieci Web ASP.NET Core generują następujący kod w celu utworzenia hosta ogólnego:</span><span class="sxs-lookup"><span data-stu-id="28f0f-386">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="28f0f-387">Poniższy kod tworzy hosta ogólnego przy użyciu obciążenia innego niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="28f0f-387">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="28f0f-388">`IHostedService`Implementacja jest dodawana do kontenera di:</span><span class="sxs-lookup"><span data-stu-id="28f0f-388">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="28f0f-389">W przypadku obciążeń HTTP `Main` Metoda jest taka sama, ale `CreateHostBuilder` wywołuje `ConfigureWebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-389">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="28f0f-390">Poprzedni kod jest generowany przez szablony ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28f0f-390">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="28f0f-391">Jeśli aplikacja używa Entity Framework Core, nie zmieniaj nazwy ani podpisu `CreateHostBuilder` metody.</span><span class="sxs-lookup"><span data-stu-id="28f0f-391">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="28f0f-392">[Narzędzia Entity Framework Core](/ef/core/miscellaneous/cli/) oczekują na znalezienie `CreateHostBuilder` metody, która konfiguruje hosta bez uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-392">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="28f0f-393">Aby uzyskać więcej informacji, zobacz [Tworzenie DbContext w czasie projektowania](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="28f0f-393">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="28f0f-394">Ustawienia domyślnego konstruktora</span><span class="sxs-lookup"><span data-stu-id="28f0f-394">Default builder settings</span></span>

<span data-ttu-id="28f0f-395"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>Metoda:</span><span class="sxs-lookup"><span data-stu-id="28f0f-395">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="28f0f-396">Ustawia [katalog główny zawartości](xref:fundamentals/index#content-root) na ścieżkę zwracaną przez <xref:System.IO.Directory.GetCurrentDirectory%2A> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-396">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="28f0f-397">Ładuje konfigurację hosta z:</span><span class="sxs-lookup"><span data-stu-id="28f0f-397">Loads host configuration from:</span></span>
  * <span data-ttu-id="28f0f-398">Zmienne środowiskowe poprzedzone prefiksem `DOTNET_` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-398">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="28f0f-399">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-399">Command-line arguments.</span></span>
* <span data-ttu-id="28f0f-400">Ładuje konfigurację aplikacji z:</span><span class="sxs-lookup"><span data-stu-id="28f0f-400">Loads app configuration from:</span></span>
  * <span data-ttu-id="28f0f-401">*:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="28f0f-401">*:::no-loc(appsettings.json):::* .</span></span>
  * <span data-ttu-id="28f0f-402">*appSettings. {Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="28f0f-402">*appsettings.{Environment}.json* .</span></span>
  * <span data-ttu-id="28f0f-403">[Secret Manager](xref:security/app-secrets) , gdy aplikacja jest uruchamiana w `Development` środowisku.</span><span class="sxs-lookup"><span data-stu-id="28f0f-403">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="28f0f-404">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="28f0f-404">Environment variables.</span></span>
  * <span data-ttu-id="28f0f-405">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-405">Command-line arguments.</span></span>
* <span data-ttu-id="28f0f-406">Dodaje następujących dostawców [rejestrowania](xref:fundamentals/logging/index) :</span><span class="sxs-lookup"><span data-stu-id="28f0f-406">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="28f0f-407">Konsola</span><span class="sxs-lookup"><span data-stu-id="28f0f-407">Console</span></span>
  * <span data-ttu-id="28f0f-408">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="28f0f-408">Debug</span></span>
  * <span data-ttu-id="28f0f-409">EventSource</span><span class="sxs-lookup"><span data-stu-id="28f0f-409">EventSource</span></span>
  * <span data-ttu-id="28f0f-410">EventLog (tylko w przypadku uruchamiania w systemie Windows)</span><span class="sxs-lookup"><span data-stu-id="28f0f-410">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="28f0f-411">Umożliwia [weryfikację zakresu](xref:fundamentals/dependency-injection#scope-validation) i [Sprawdzanie poprawności zależności](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) , gdy środowisko jest opracowywane.</span><span class="sxs-lookup"><span data-stu-id="28f0f-411">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="28f0f-412">`ConfigureWebHostDefaults`Metoda:</span><span class="sxs-lookup"><span data-stu-id="28f0f-412">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="28f0f-413">Ładuje konfigurację hosta ze zmiennych środowiskowych, które są poprzedzone prefiksem `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-413">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="28f0f-414">Ustawia serwer [Kestrel](xref:fundamentals/servers/kestrel) jako serwer sieci Web i konfiguruje go przy użyciu dostawców konfiguracji hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-414">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="28f0f-415">Aby poznać domyślne opcje serwera Kestrel, zobacz <xref:fundamentals/servers/kestrel#kestrel-options> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-415">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="28f0f-416">Dodaje [oprogramowanie pośredniczące do filtrowania hosta](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="28f0f-416">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="28f0f-417">Dodaje [przekazane nagłówki — oprogramowanie pośredniczące](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) , jeśli `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest równe `true` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-417">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="28f0f-418">Włącza integrację usług IIS.</span><span class="sxs-lookup"><span data-stu-id="28f0f-418">Enables IIS integration.</span></span> <span data-ttu-id="28f0f-419">Aby poznać domyślne opcje usług IIS, zobacz <xref:host-and-deploy/iis/index#iis-options> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-419">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="28f0f-420">[Ustawienia dla wszystkich typów aplikacji](#settings-for-all-app-types) i [Ustawienia dla usługi Web Apps](#settings-for-web-apps) w dalszej części tego artykułu pokazują, jak zastąpić ustawienia domyślnego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="28f0f-420">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="28f0f-421">Usługi udostępniane przez platformę</span><span class="sxs-lookup"><span data-stu-id="28f0f-421">Framework-provided services</span></span>

<span data-ttu-id="28f0f-422">Następujące usługi są rejestrowane automatycznie:</span><span class="sxs-lookup"><span data-stu-id="28f0f-422">The following services are registered automatically:</span></span>

* [<span data-ttu-id="28f0f-423">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="28f0f-423">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="28f0f-424">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="28f0f-424">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="28f0f-425">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="28f0f-425">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="28f0f-426">Aby uzyskać więcej informacji na temat usług udostępnianych przez platformę, zobacz <xref:fundamentals/dependency-injection#framework-provided-services> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-426">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="28f0f-427">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="28f0f-427">IHostApplicationLifetime</span></span>

<span data-ttu-id="28f0f-428">Wstrzyknąć <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> usługę (dawniej `IApplicationLifetime` ) do dowolnej klasy w celu obsługi zadań po uruchomieniu i bezpiecznego zamykania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-428">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="28f0f-429">Trzy właściwości interfejsu to tokeny anulowania używane do rejestrowania metod obsługi zdarzeń uruchamiania i zatrzymywania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-429">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="28f0f-430">Interfejs zawiera również `StopApplication` metodę.</span><span class="sxs-lookup"><span data-stu-id="28f0f-430">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="28f0f-431">Poniższy przykład to `IHostedService` implementacja, która rejestruje `IHostApplicationLifetime` zdarzenia:</span><span class="sxs-lookup"><span data-stu-id="28f0f-431">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="28f0f-432">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="28f0f-432">IHostLifetime</span></span>

<span data-ttu-id="28f0f-433"><xref:Microsoft.Extensions.Hosting.IHostLifetime>Implementacja kontroluje moment uruchomienia hosta i jego zatrzymania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-433">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="28f0f-434">Używana jest Ostatnia zarejestrowana implementacja.</span><span class="sxs-lookup"><span data-stu-id="28f0f-434">The last implementation registered is used.</span></span>

<span data-ttu-id="28f0f-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` jest `IHostLifetime` implementacją domyślną.</span><span class="sxs-lookup"><span data-stu-id="28f0f-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="28f0f-436">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="28f0f-436">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="28f0f-437">Nasłuchuje <kbd>kombinacji klawiszy CTRL</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM oraz wywołań, <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> Aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-437">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="28f0f-438">Odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="28f0f-438">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="28f0f-439">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="28f0f-439">IHostEnvironment</span></span>

<span data-ttu-id="28f0f-440">Wsuń <xref:Microsoft.Extensions.Hosting.IHostEnvironment> usługę do klasy, aby uzyskać informacje o następujących ustawieniach:</span><span class="sxs-lookup"><span data-stu-id="28f0f-440">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="28f0f-441">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="28f0f-441">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="28f0f-442">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="28f0f-442">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="28f0f-443">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="28f0f-443">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="28f0f-444">Aplikacje sieci Web implementują `IWebHostEnvironment` interfejs, który dziedziczy `IHostEnvironment` i dodaje [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="28f0f-444">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="28f0f-445">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="28f0f-445">Host configuration</span></span>

<span data-ttu-id="28f0f-446">Konfiguracja hosta jest używana we właściwościach <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-446">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="28f0f-447">Konfiguracja hosta jest dostępna w [HostBuilderContext.Configwersja](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-447">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="28f0f-448">Po `ConfigureAppConfiguration` program `HostBuilderContext.Configuration` zostanie zastąpiony konfiguracją aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-448">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="28f0f-449">Aby dodać konfigurację hosta, wywołaj polecenie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-449">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="28f0f-450">`ConfigureHostConfiguration` może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="28f0f-450">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="28f0f-451">Host używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="28f0f-451">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="28f0f-452">Dostawca zmiennych środowiskowych z prefiksami `DOTNET_` i argumentami wiersza polecenia są uwzględniane przez `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-452">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="28f0f-453">W przypadku aplikacji sieci Web jest dodawany dostawca zmiennych środowiskowych z prefiksem `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-453">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="28f0f-454">Prefiks jest usuwany, gdy są odczytywane zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="28f0f-454">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="28f0f-455">Na przykład wartość zmiennej środowiskowej jest `ASPNETCORE_ENVIRONMENT` wartością konfiguracji hosta dla `environment` klucza.</span><span class="sxs-lookup"><span data-stu-id="28f0f-455">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="28f0f-456">Poniższy przykład umożliwia utworzenie konfiguracji hosta:</span><span class="sxs-lookup"><span data-stu-id="28f0f-456">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="28f0f-457">Konfiguracja aplikacji</span><span class="sxs-lookup"><span data-stu-id="28f0f-457">App configuration</span></span>

<span data-ttu-id="28f0f-458">Konfiguracja aplikacji jest tworzona przez wywołanie metody <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-458">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="28f0f-459">`ConfigureAppConfiguration` może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="28f0f-459">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="28f0f-460">Aplikacja używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="28f0f-460">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="28f0f-461">Konfiguracja utworzona przez program `ConfigureAppConfiguration` jest dostępna w [HostBuilderContext.Configwersja](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) na potrzeby kolejnych operacji i jako usługa z di.</span><span class="sxs-lookup"><span data-stu-id="28f0f-461">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="28f0f-462">Konfiguracja hosta jest również dodawana do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-462">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="28f0f-463">Aby uzyskać więcej informacji, zobacz [Konfiguracja w ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="28f0f-463">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="28f0f-464">Ustawienia dla wszystkich typów aplikacji</span><span class="sxs-lookup"><span data-stu-id="28f0f-464">Settings for all app types</span></span>

<span data-ttu-id="28f0f-465">Ta sekcja zawiera listę ustawień hosta, które dotyczą zarówno obciążeń HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="28f0f-465">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="28f0f-466">Domyślnie zmienne środowiskowe używane do konfigurowania tych ustawień mogą mieć `DOTNET_` `ASPNETCORE_` prefiks lub.</span><span class="sxs-lookup"><span data-stu-id="28f0f-466">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="28f0f-467">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="28f0f-467">ApplicationName</span></span>

<span data-ttu-id="28f0f-468">Właściwość [IHostEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) jest ustawiana na podstawie konfiguracji hosta podczas konstruowania hosta.</span><span class="sxs-lookup"><span data-stu-id="28f0f-468">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="28f0f-469">**Klucz** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="28f0f-469">**Key** : `applicationName`</span></span>  
<span data-ttu-id="28f0f-470">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-470">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-471">**Wartość domyślna** : Nazwa zestawu, który zawiera punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-471">**Default** : The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="28f0f-472">**Zmienna środowiskowa** : `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="28f0f-472">**Environment variable** : `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="28f0f-473">Aby ustawić tę wartość, należy użyć zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="28f0f-473">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="28f0f-474">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="28f0f-474">ContentRoot</span></span>

<span data-ttu-id="28f0f-475">Właściwość [IHostEnvironment. ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="28f0f-475">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="28f0f-476">Jeśli ścieżka nie istnieje, uruchomienie hosta nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="28f0f-476">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="28f0f-477">**Klucz** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="28f0f-477">**Key** : `contentRoot`</span></span>  
<span data-ttu-id="28f0f-478">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-478">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-479">**Domyślnie** : folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-479">**Default** : The folder where the app assembly resides.</span></span>  
<span data-ttu-id="28f0f-480">**Zmienna środowiskowa** : `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="28f0f-480">**Environment variable** : `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="28f0f-481">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseContentRoot` `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-481">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="28f0f-482">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="28f0f-482">For more information, see:</span></span>

* [<span data-ttu-id="28f0f-483">Podstawy: zawartość główna</span><span class="sxs-lookup"><span data-stu-id="28f0f-483">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="28f0f-484">WebRoot</span><span class="sxs-lookup"><span data-stu-id="28f0f-484">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="28f0f-485">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="28f0f-485">EnvironmentName</span></span>

<span data-ttu-id="28f0f-486">Dla właściwości [IHostEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) można ustawić dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="28f0f-486">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="28f0f-487">Wartości zdefiniowane przez platformę obejmują `Development` , `Staging` , i `Production` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-487">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="28f0f-488">W wartościach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="28f0f-488">Values aren't case-sensitive.</span></span>

<span data-ttu-id="28f0f-489">**Klucz** : `environment`</span><span class="sxs-lookup"><span data-stu-id="28f0f-489">**Key** : `environment`</span></span>  
<span data-ttu-id="28f0f-490">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-490">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-491">**Wartość domyślna** : `Production`</span><span class="sxs-lookup"><span data-stu-id="28f0f-491">**Default** : `Production`</span></span>  
<span data-ttu-id="28f0f-492">**Zmienna środowiskowa** : `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="28f0f-492">**Environment variable** : `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="28f0f-493">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseEnvironment` `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-493">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="28f0f-494">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="28f0f-494">ShutdownTimeout</span></span>

<span data-ttu-id="28f0f-495">[HostOptions. shutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) ustawia limit czasu dla <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="28f0f-496">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="28f0f-496">The default value is five seconds.</span></span>  <span data-ttu-id="28f0f-497">Podczas okresu przekroczenia limitu czasu Host:</span><span class="sxs-lookup"><span data-stu-id="28f0f-497">During the timeout period, the host:</span></span>

* <span data-ttu-id="28f0f-498">Wyzwala [IHostApplicationLifetime. ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="28f0f-498">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="28f0f-499">Próbuje zatrzymać usługi hostowane, rejestrowanie błędów dla usług, których zatrzymanie nie powiodło się.</span><span class="sxs-lookup"><span data-stu-id="28f0f-499">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="28f0f-500">Jeśli limit czasu upłynie przed zatrzymaniem wszystkich usług hostowanych, wszystkie pozostałe aktywne usługi zostaną zatrzymane po zamknięciu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-500">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="28f0f-501">Usługi są zatrzymane nawet wtedy, gdy nie zakończyły przetwarzania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-501">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="28f0f-502">Jeśli usługi wymagają dodatkowego czasu na zatrzymanie, zwiększ limit czasu.</span><span class="sxs-lookup"><span data-stu-id="28f0f-502">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="28f0f-503">**Klucz** : `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="28f0f-503">**Key** : `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="28f0f-504">**Typ** : `int`</span><span class="sxs-lookup"><span data-stu-id="28f0f-504">**Type** : `int`</span></span>  
<span data-ttu-id="28f0f-505">**Wartość domyślna** : 5 sekund</span><span class="sxs-lookup"><span data-stu-id="28f0f-505">**Default** : 5 seconds</span></span>  
<span data-ttu-id="28f0f-506">**Zmienna środowiskowa** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="28f0f-506">**Environment variable** : `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="28f0f-507">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub skonfiguruj `HostOptions` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-507">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="28f0f-508">Poniższy przykład ustawia limit czasu na 20 sekund:</span><span class="sxs-lookup"><span data-stu-id="28f0f-508">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="28f0f-509">Ustawienia usługi Web Apps</span><span class="sxs-lookup"><span data-stu-id="28f0f-509">Settings for web apps</span></span>

<span data-ttu-id="28f0f-510">Niektóre ustawienia hosta mają zastosowanie tylko do obciążeń protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="28f0f-510">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="28f0f-511">Domyślnie zmienne środowiskowe używane do konfigurowania tych ustawień mogą mieć `DOTNET_` `ASPNETCORE_` prefiks lub.</span><span class="sxs-lookup"><span data-stu-id="28f0f-511">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="28f0f-512">Metody rozszerzenia dla `IWebHostBuilder` programu są dostępne dla tych ustawień.</span><span class="sxs-lookup"><span data-stu-id="28f0f-512">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="28f0f-513">Przykłady kodu, które pokazują, jak wywołać metody rozszerzane `webBuilder` jest wystąpieniem `IWebHostBuilder` , tak jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="28f0f-513">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="28f0f-514">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="28f0f-514">CaptureStartupErrors</span></span>

<span data-ttu-id="28f0f-515">Gdy `false` , błędy podczas uruchamiania, kończy się hostem.</span><span class="sxs-lookup"><span data-stu-id="28f0f-515">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="28f0f-516">Gdy `true` host przechwytuje wyjątki podczas uruchamiania, a następnie próbuje uruchomić serwer.</span><span class="sxs-lookup"><span data-stu-id="28f0f-516">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="28f0f-517">**Klucz** : `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="28f0f-517">**Key** : `captureStartupErrors`</span></span>  
<span data-ttu-id="28f0f-518">**Typ** : `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="28f0f-518">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="28f0f-519">**Domyślnie** : wartość domyślna to, `false` chyba że aplikacja działa z Kestrel za usługami IIS, gdzie wartość domyślna to `true` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-519">**Default** : Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="28f0f-520">**Zmienna środowiskowa** : `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="28f0f-520">**Environment variable** : `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="28f0f-521">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `CaptureStartupErrors` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-521">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="28f0f-522">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="28f0f-522">DetailedErrors</span></span>

<span data-ttu-id="28f0f-523">Po włączeniu lub gdy środowisko jest `Development` , aplikacja przechwytuje szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="28f0f-523">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="28f0f-524">**Klucz** : `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="28f0f-524">**Key** : `detailedErrors`</span></span>  
<span data-ttu-id="28f0f-525">**Typ** : `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="28f0f-525">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="28f0f-526">**Wartość domyślna** : `false`</span><span class="sxs-lookup"><span data-stu-id="28f0f-526">**Default** : `false`</span></span>  
<span data-ttu-id="28f0f-527">**Zmienna środowiskowa** : `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="28f0f-527">**Environment variable** : `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="28f0f-528">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-528">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="28f0f-529">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="28f0f-529">HostingStartupAssemblies</span></span>

<span data-ttu-id="28f0f-530">Rozdzielany średnikami ciąg początkowych zestawów startowych do załadowania podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-530">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="28f0f-531">Mimo że wartość konfiguracji jest domyślnie pustym ciągiem, zestaw startowy obsługujący zawsze zawiera zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-531">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="28f0f-532">W przypadku udostępniania zestawów startowych są one dodawane do zestawu aplikacji do załadowania, gdy aplikacja kompiluje swoje popularne usługi podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-532">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="28f0f-533">**Klucz** : `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="28f0f-533">**Key** : `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="28f0f-534">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-534">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-535">**Wartość domyślna** : pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="28f0f-535">**Default** : Empty string</span></span>  
<span data-ttu-id="28f0f-536">**Zmienna środowiskowa** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="28f0f-536">**Environment variable** : `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="28f0f-537">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-537">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="28f0f-538">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="28f0f-538">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="28f0f-539">Rozdzielany średnikami ciąg początkowych zestawów uruchamiania, który ma zostać wykluczony podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-539">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="28f0f-540">**Klucz** : `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="28f0f-540">**Key** : `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="28f0f-541">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-541">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-542">**Wartość domyślna** : pusty ciąg</span><span class="sxs-lookup"><span data-stu-id="28f0f-542">**Default** : Empty string</span></span>  
<span data-ttu-id="28f0f-543">**Zmienna środowiskowa** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="28f0f-543">**Environment variable** : `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="28f0f-544">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-544">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="28f0f-545">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="28f0f-545">HTTPS_Port</span></span>

<span data-ttu-id="28f0f-546">Port przekierowania protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="28f0f-546">The HTTPS redirect port.</span></span> <span data-ttu-id="28f0f-547">Używany do [wymuszania protokołu HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="28f0f-547">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="28f0f-548">**Klucz** : `https_port`</span><span class="sxs-lookup"><span data-stu-id="28f0f-548">**Key** : `https_port`</span></span>  
<span data-ttu-id="28f0f-549">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-549">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-550">Wartość **Domyślna** : nie ustawiono wartości domyślnej.</span><span class="sxs-lookup"><span data-stu-id="28f0f-550">**Default** : A default value isn't set.</span></span>  
<span data-ttu-id="28f0f-551">**Zmienna środowiskowa** : `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="28f0f-551">**Environment variable** : `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="28f0f-552">Aby ustawić tę wartość, użyj konfiguracji lub wywołania `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-552">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="28f0f-553">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="28f0f-553">PreferHostingUrls</span></span>

<span data-ttu-id="28f0f-554">Wskazuje, czy host powinien nasłuchiwać adresów URL skonfigurowanych przy użyciu `IWebHostBuilder` zamiast adresów URL skonfigurowanych dla `IServer` implementacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-554">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="28f0f-555">**Klucz** : `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="28f0f-555">**Key** : `preferHostingUrls`</span></span>  
<span data-ttu-id="28f0f-556">**Typ** : `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="28f0f-556">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="28f0f-557">**Wartość domyślna** : `true`</span><span class="sxs-lookup"><span data-stu-id="28f0f-557">**Default** : `true`</span></span>  
<span data-ttu-id="28f0f-558">**Zmienna środowiskowa** : `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="28f0f-558">**Environment variable** : `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="28f0f-559">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `PreferHostingUrls` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-559">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="28f0f-560">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="28f0f-560">PreventHostingStartup</span></span>

<span data-ttu-id="28f0f-561">Zapobiega automatycznemu ładowaniu zestawów startowych hostingu, w tym hostingu zestawów startowych skonfigurowanych przez zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-561">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="28f0f-562">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="28f0f-562">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="28f0f-563">**Klucz** : `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="28f0f-563">**Key** : `preventHostingStartup`</span></span>  
<span data-ttu-id="28f0f-564">**Typ** : `bool` ( `true` lub `1` )</span><span class="sxs-lookup"><span data-stu-id="28f0f-564">**Type** : `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="28f0f-565">**Wartość domyślna** : `false`</span><span class="sxs-lookup"><span data-stu-id="28f0f-565">**Default** : `false`</span></span>  
<span data-ttu-id="28f0f-566">**Zmienna środowiskowa** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="28f0f-566">**Environment variable** : `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="28f0f-567">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseSetting` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-567">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="28f0f-568">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="28f0f-568">StartupAssembly</span></span>

<span data-ttu-id="28f0f-569">Zestaw do wyszukiwania `Startup` klasy.</span><span class="sxs-lookup"><span data-stu-id="28f0f-569">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="28f0f-570">**Klucz** : `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="28f0f-570">**Key** : `startupAssembly`</span></span>  
<span data-ttu-id="28f0f-571">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-571">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-572">**Domyślnie** : zestaw aplikacji</span><span class="sxs-lookup"><span data-stu-id="28f0f-572">**Default** : The app's assembly</span></span>  
<span data-ttu-id="28f0f-573">**Zmienna środowiskowa** : `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="28f0f-573">**Environment variable** : `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="28f0f-574">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołania `UseStartup` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-574">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="28f0f-575">`UseStartup` może przyjmować nazwę zestawu ( `string` ) lub typ ( `TStartup` ).</span><span class="sxs-lookup"><span data-stu-id="28f0f-575">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="28f0f-576">Jeśli `UseStartup` wywoływana jest wiele metod, pierwszeństwo ma Ostatnia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-576">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="28f0f-577">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="28f0f-577">URLs</span></span>

<span data-ttu-id="28f0f-578">Rozdzielana średnikami lista adresów IP lub adresów hostów z portami i protokołami, na których serwer powinien nasłuchiwać żądań.</span><span class="sxs-lookup"><span data-stu-id="28f0f-578">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="28f0f-579">Na przykład `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="28f0f-579">For example, `http://localhost:123`.</span></span> <span data-ttu-id="28f0f-580">Użyj " \* ", aby wskazać, że serwer powinien nasłuchiwać żądań na dowolnym adresie IP lub nazwie hosta przy użyciu określonego portu i protokołu (na przykład `http://*:5000` ).</span><span class="sxs-lookup"><span data-stu-id="28f0f-580">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="28f0f-581">Protokół ( `http://` lub `https://` ) musi być dołączony do każdego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="28f0f-581">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="28f0f-582">Obsługiwane formaty różnią się między serwerami.</span><span class="sxs-lookup"><span data-stu-id="28f0f-582">Supported formats vary among servers.</span></span>

<span data-ttu-id="28f0f-583">**Klucz** : `urls`</span><span class="sxs-lookup"><span data-stu-id="28f0f-583">**Key** : `urls`</span></span>  
<span data-ttu-id="28f0f-584">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-584">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-585">**Wartość domyślna** : `http://localhost:5000` i `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="28f0f-585">**Default** : `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="28f0f-586">**Zmienna środowiskowa** : `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="28f0f-586">**Environment variable** : `<PREFIX_>URLS`</span></span>

<span data-ttu-id="28f0f-587">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseUrls` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-587">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="28f0f-588">Kestrel ma własny interfejs API konfiguracji punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="28f0f-588">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="28f0f-589">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="28f0f-589">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="28f0f-590">WebRoot</span><span class="sxs-lookup"><span data-stu-id="28f0f-590">WebRoot</span></span>

<span data-ttu-id="28f0f-591">Właściwość [IWebHostEnvironment. WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) określa ścieżkę względną do statycznych zasobów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-591">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="28f0f-592">Jeśli ścieżka nie istnieje, jest używany dostawca plików No-op.</span><span class="sxs-lookup"><span data-stu-id="28f0f-592">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="28f0f-593">**Klucz** : `webroot`</span><span class="sxs-lookup"><span data-stu-id="28f0f-593">**Key** : `webroot`</span></span>  
<span data-ttu-id="28f0f-594">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-594">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-595">**Wartość domyślna** : wartość domyślna to `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-595">**Default** : The default is `wwwroot`.</span></span> <span data-ttu-id="28f0f-596">Ścieżka do *elementu {content root}/wwwroot* musi istnieć.</span><span class="sxs-lookup"><span data-stu-id="28f0f-596">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="28f0f-597">**Zmienna środowiskowa** : `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="28f0f-597">**Environment variable** : `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="28f0f-598">Aby ustawić tę wartość, użyj zmiennej środowiskowej lub wywołaj `UseWebRoot` `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-598">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="28f0f-599">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="28f0f-599">For more information, see:</span></span>

* [<span data-ttu-id="28f0f-600">Podstawy: katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="28f0f-600">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="28f0f-601">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="28f0f-601">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="28f0f-602">Zarządzanie okresem istnienia hosta</span><span class="sxs-lookup"><span data-stu-id="28f0f-602">Manage the host lifetime</span></span>

<span data-ttu-id="28f0f-603">Wywołaj metody z skompilowanej <xref:Microsoft.Extensions.Hosting.IHost> implementacji, aby uruchomić i zatrzymać aplikację.</span><span class="sxs-lookup"><span data-stu-id="28f0f-603">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="28f0f-604">Te metody mają wpływ na wszystkie  <xref:Microsoft.Extensions.Hosting.IHostedService> implementacje zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="28f0f-604">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="28f0f-605">Uruchom</span><span class="sxs-lookup"><span data-stu-id="28f0f-605">Run</span></span>

<span data-ttu-id="28f0f-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uruchamia aplikację i blokuje wątek wywołujący do momentu wyłączenia hosta.</span><span class="sxs-lookup"><span data-stu-id="28f0f-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="28f0f-607">RunAsync</span><span class="sxs-lookup"><span data-stu-id="28f0f-607">RunAsync</span></span>

<span data-ttu-id="28f0f-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uruchamia aplikację i zwraca <xref:System.Threading.Tasks.Task> , która kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="28f0f-609">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="28f0f-609">RunConsoleAsync</span></span>

<span data-ttu-id="28f0f-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>włącza obsługę konsoli, kompiluje i uruchamia hosta, a następnie czeka na zamknięcie programu <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="28f0f-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="28f0f-611">Rozpocznij</span><span class="sxs-lookup"><span data-stu-id="28f0f-611">Start</span></span>

<span data-ttu-id="28f0f-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="28f0f-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="28f0f-613">StartAsync</span><span class="sxs-lookup"><span data-stu-id="28f0f-613">StartAsync</span></span>

<span data-ttu-id="28f0f-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> uruchamia hosta i zwraca <xref:System.Threading.Tasks.Task> , który kończy się po wyzwoleniu tokenu anulowania lub zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="28f0f-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> jest wywoływana na początku `StartAsync` , który czeka na zakończenie przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="28f0f-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="28f0f-616">Może to służyć do opóźnienia uruchamiania do momentu zasygnalizowania zdarzenia zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="28f0f-616">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="28f0f-617">StopAsync</span><span class="sxs-lookup"><span data-stu-id="28f0f-617">StopAsync</span></span>

<span data-ttu-id="28f0f-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> próbuje zatrzymać hosta w ramach podanego limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="28f0f-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="28f0f-619">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="28f0f-619">WaitForShutdown</span></span>

<span data-ttu-id="28f0f-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>blokuje wątek wywołujący do momentu wyzwolenia przez IHostLifetime, na przykład przez <kbd>naciśnięcie klawiszy CTRL</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="28f0f-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="28f0f-621">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="28f0f-621">WaitForShutdownAsync</span></span>

<span data-ttu-id="28f0f-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Zwraca wartość <xref:System.Threading.Tasks.Task> , która kończy się, gdy zamknięcie jest wyzwalane za pośrednictwem danego tokenu i wywołań <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="28f0f-623">Zewnętrzna kontrola</span><span class="sxs-lookup"><span data-stu-id="28f0f-623">External control</span></span>

<span data-ttu-id="28f0f-624">Bezpośrednią kontrolę nad okresem istnienia hosta można uzyskać przy użyciu metod, które mogą być wywoływane zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="28f0f-624">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="28f0f-625">ASP.NET Core aplikacje konfigurują i uruchamiają hosta.</span><span class="sxs-lookup"><span data-stu-id="28f0f-625">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="28f0f-626">Host jest odpowiedzialny za uruchamianie aplikacji i zarządzanie okresem istnienia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-626">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="28f0f-627">W tym artykule opisano ASP.NET Core hosta ogólnego ( <xref:Microsoft.Extensions.Hosting.HostBuilder> ), który jest używany w przypadku aplikacji, które nie przetwarzają żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="28f0f-627">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="28f0f-628">Przeznaczeniem hosta ogólnego jest oddzielenie potoku HTTP od interfejsu API hosta sieci Web w celu umożliwienia szerszej tablicy scenariuszy hostów.</span><span class="sxs-lookup"><span data-stu-id="28f0f-628">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="28f0f-629">Obsługa komunikatów, zadań w tle i innych obciążeń innych niż HTTP na podstawie ogólnej korzyści z hosta, takich jak konfiguracja, iniekcja zależności (DI) i rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="28f0f-629">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="28f0f-630">Host ogólny jest nowy w ASP.NET Core 2,1 i nie jest odpowiedni dla scenariuszy hostingu w sieci Web.</span><span class="sxs-lookup"><span data-stu-id="28f0f-630">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="28f0f-631">W przypadku scenariuszy hostingu w sieci Web należy użyć [hosta sieci Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="28f0f-631">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="28f0f-632">Host ogólny zastąpi hosta sieci Web w przyszłej wersji i będzie pełnić rolę podstawowego interfejsu API hosta zarówno w scenariuszach HTTP, jak i innych niż HTTP.</span><span class="sxs-lookup"><span data-stu-id="28f0f-632">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="28f0f-633">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="28f0f-633">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="28f0f-634">Podczas uruchamiania przykładowej aplikacji w [Visual Studio Code](https://code.visualstudio.com/)należy użyć *zewnętrznego lub zintegrowanego terminalu* .</span><span class="sxs-lookup"><span data-stu-id="28f0f-634">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal* .</span></span> <span data-ttu-id="28f0f-635">Nie uruchamiaj próbki w `internalConsole` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-635">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="28f0f-636">Aby ustawić konsolę w Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="28f0f-636">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="28f0f-637">Otwórz plik *. programu vscode/launch.jsw* pliku.</span><span class="sxs-lookup"><span data-stu-id="28f0f-637">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="28f0f-638">W konfiguracji **uruchamiania programu .NET Core (konsoli)** zlokalizuj wpis **konsoli** .</span><span class="sxs-lookup"><span data-stu-id="28f0f-638">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="28f0f-639">Ustaw wartość na `externalTerminal` lub `integratedTerminal` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-639">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="28f0f-640">Wprowadzenie</span><span class="sxs-lookup"><span data-stu-id="28f0f-640">Introduction</span></span>

<span data-ttu-id="28f0f-641">Ogólna Biblioteka hostów jest dostępna w <xref:Microsoft.Extensions.Hosting> przestrzeni nazw i udostępniana przez pakiet [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) .</span><span class="sxs-lookup"><span data-stu-id="28f0f-641">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="28f0f-642">`Microsoft.Extensions.Hosting`Pakiet jest zawarty w [pakiecie Microsoft. AspNetCore. appbinding](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 lub nowszym).</span><span class="sxs-lookup"><span data-stu-id="28f0f-642">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="28f0f-643"><xref:Microsoft.Extensions.Hosting.IHostedService> jest punktem wejścia do wykonania kodu.</span><span class="sxs-lookup"><span data-stu-id="28f0f-643"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="28f0f-644">Każda <xref:Microsoft.Extensions.Hosting.IHostedService> implementacja jest wykonywana w kolejności [rejestracji usługi w ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="28f0f-644">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="28f0f-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> jest wywoływana przy każdym <xref:Microsoft.Extensions.Hosting.IHostedService> uruchomieniu hosta i <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> jest wywoływana w odwrotnej kolejności rejestracji, gdy host jest zamykany prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="28f0f-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="28f0f-646">Konfigurowanie hosta</span><span class="sxs-lookup"><span data-stu-id="28f0f-646">Set up a host</span></span>

<span data-ttu-id="28f0f-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> jest głównym składnikiem używanym przez biblioteki i aplikacje do inicjowania, kompilowania i uruchamiania hosta:</span><span class="sxs-lookup"><span data-stu-id="28f0f-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="28f0f-648">Opcje</span><span class="sxs-lookup"><span data-stu-id="28f0f-648">Options</span></span>

<span data-ttu-id="28f0f-649"><xref:Microsoft.Extensions.Hosting.HostOptions> Skonfiguruj opcje <xref:Microsoft.Extensions.Hosting.IHost> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-649"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="28f0f-650">Limit czasu zamykania</span><span class="sxs-lookup"><span data-stu-id="28f0f-650">Shutdown timeout</span></span>

<span data-ttu-id="28f0f-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ustawia limit czasu dla <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="28f0f-652">Wartość domyślna to pięć sekund.</span><span class="sxs-lookup"><span data-stu-id="28f0f-652">The default value is five seconds.</span></span>

<span data-ttu-id="28f0f-653">Następująca konfiguracja opcji w programie `Program.Main` powoduje zwiększenie domyślnego limitu czasu zamykania pięciu sekund:</span><span class="sxs-lookup"><span data-stu-id="28f0f-653">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="28f0f-654">Usługi domyślne</span><span class="sxs-lookup"><span data-stu-id="28f0f-654">Default services</span></span>

<span data-ttu-id="28f0f-655">Podczas inicjowania hosta zarejestrowano następujące usługi:</span><span class="sxs-lookup"><span data-stu-id="28f0f-655">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="28f0f-656">[Środowisko](xref:fundamentals/environments) ( <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> )</span><span class="sxs-lookup"><span data-stu-id="28f0f-656">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="28f0f-657">[Konfiguracja](xref:fundamentals/configuration/index) ( <xref:Microsoft.Extensions.Configuration.IConfiguration> )</span><span class="sxs-lookup"><span data-stu-id="28f0f-657">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="28f0f-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="28f0f-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="28f0f-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="28f0f-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="28f0f-660">[Opcje](xref:fundamentals/configuration/options) ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*> )</span><span class="sxs-lookup"><span data-stu-id="28f0f-660">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="28f0f-661">[Rejestrowanie](xref:fundamentals/logging/index) ( <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*> )</span><span class="sxs-lookup"><span data-stu-id="28f0f-661">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="28f0f-662">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="28f0f-662">Host configuration</span></span>

<span data-ttu-id="28f0f-663">Konfiguracja hosta jest tworzona przez:</span><span class="sxs-lookup"><span data-stu-id="28f0f-663">Host configuration is created by:</span></span>

* <span data-ttu-id="28f0f-664">Wywoływanie metod rozszerzenia w <xref:Microsoft.Extensions.Hosting.IHostBuilder> celu ustawienia [katalogu głównego zawartości](#content-root) i [środowiska](#environment).</span><span class="sxs-lookup"><span data-stu-id="28f0f-664">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="28f0f-665">Odczytywanie konfiguracji od dostawców konfiguracji w programie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-665">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="28f0f-666">Metody rozszerzeń</span><span class="sxs-lookup"><span data-stu-id="28f0f-666">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="28f0f-667">Klucz aplikacji (nazwa)</span><span class="sxs-lookup"><span data-stu-id="28f0f-667">Application key (name)</span></span>

<span data-ttu-id="28f0f-668">Właściwość [IHostingEnvironment. ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) jest ustawiana na podstawie konfiguracji hosta podczas konstruowania hosta.</span><span class="sxs-lookup"><span data-stu-id="28f0f-668">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="28f0f-669">Aby jawnie ustawić wartość, użyj [HostDefaults. ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="28f0f-669">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="28f0f-670">**Klucz** : `applicationName`</span><span class="sxs-lookup"><span data-stu-id="28f0f-670">**Key** : `applicationName`</span></span>  
<span data-ttu-id="28f0f-671">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-671">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-672">**Wartość domyślna** : Nazwa zestawu zawierającego punkt wejścia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-672">**Default** : The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="28f0f-673">**Ustaw przy użyciu** : `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="28f0f-673">**Set using** : `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="28f0f-674">**Zmienna środowiskowa** : `<PREFIX_>APPLICATIONNAME` ( `<PREFIX_>` jest [opcjonalne i zdefiniowane przez użytkownika](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="28f0f-674">**Environment variable** : `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="28f0f-675">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="28f0f-675">Content root</span></span>

<span data-ttu-id="28f0f-676">To ustawienie określa, gdzie host rozpoczyna wyszukiwanie plików zawartości.</span><span class="sxs-lookup"><span data-stu-id="28f0f-676">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="28f0f-677">**Klucz** : `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="28f0f-677">**Key** : `contentRoot`</span></span>  
<span data-ttu-id="28f0f-678">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-678">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-679">**Domyślnie** : Domyślnie folder, w którym znajduje się zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-679">**Default** : Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="28f0f-680">**Ustaw przy użyciu** : `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="28f0f-680">**Set using** : `UseContentRoot`</span></span>  
<span data-ttu-id="28f0f-681">**Zmienna środowiskowa** : `<PREFIX_>CONTENTROOT` ( `<PREFIX_>` jest [opcjonalne i zdefiniowane przez użytkownika](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="28f0f-681">**Environment variable** : `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="28f0f-682">Jeśli ścieżka nie istnieje, uruchomienie hosta nie powiedzie się.</span><span class="sxs-lookup"><span data-stu-id="28f0f-682">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="28f0f-683">Aby uzyskać więcej informacji, zobacz temat [podstawy: zawartość główna](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="28f0f-683">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="28f0f-684">Środowisko</span><span class="sxs-lookup"><span data-stu-id="28f0f-684">Environment</span></span>

<span data-ttu-id="28f0f-685">Ustawia [środowisko](xref:fundamentals/environments)aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-685">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="28f0f-686">**Klucz** : `environment`</span><span class="sxs-lookup"><span data-stu-id="28f0f-686">**Key** : `environment`</span></span>  
<span data-ttu-id="28f0f-687">**Typ** : `string`</span><span class="sxs-lookup"><span data-stu-id="28f0f-687">**Type** : `string`</span></span>  
<span data-ttu-id="28f0f-688">**Wartość domyślna** : `Production`</span><span class="sxs-lookup"><span data-stu-id="28f0f-688">**Default** : `Production`</span></span>  
<span data-ttu-id="28f0f-689">**Ustaw przy użyciu** : `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="28f0f-689">**Set using** : `UseEnvironment`</span></span>  
<span data-ttu-id="28f0f-690">**Zmienna środowiskowa** : `<PREFIX_>ENVIRONMENT` ( `<PREFIX_>` jest [opcjonalne i zdefiniowane przez użytkownika](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="28f0f-690">**Environment variable** : `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="28f0f-691">Dla środowiska można ustawić dowolną wartość.</span><span class="sxs-lookup"><span data-stu-id="28f0f-691">The environment can be set to any value.</span></span> <span data-ttu-id="28f0f-692">Wartości zdefiniowane przez platformę obejmują `Development` , `Staging` , i `Production` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-692">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="28f0f-693">W wartościach nie jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="28f0f-693">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="28f0f-694">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="28f0f-694">ConfigureHostConfiguration</span></span>

<span data-ttu-id="28f0f-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> za pomocą programu <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> można utworzyć <xref:Microsoft.Extensions.Configuration.IConfiguration> dla hosta.</span><span class="sxs-lookup"><span data-stu-id="28f0f-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="28f0f-696">Konfiguracja hosta służy do inicjowania do <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> użycia w procesie kompilacji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-696">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="28f0f-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="28f0f-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="28f0f-698">Host używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="28f0f-698">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="28f0f-699">Żaden dostawca nie jest domyślnie uwzględniany.</span><span class="sxs-lookup"><span data-stu-id="28f0f-699">No providers are included by default.</span></span> <span data-ttu-id="28f0f-700">Należy jawnie określić dostawców konfiguracji, których wymaga aplikacja, w <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> tym:</span><span class="sxs-lookup"><span data-stu-id="28f0f-700">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="28f0f-701">Konfiguracja pliku (na przykład z *hostsettings.jsw* pliku).</span><span class="sxs-lookup"><span data-stu-id="28f0f-701">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="28f0f-702">Konfiguracja zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="28f0f-702">Environment variable configuration.</span></span>
* <span data-ttu-id="28f0f-703">Konfiguracja argumentu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-703">Command-line argument configuration.</span></span>
* <span data-ttu-id="28f0f-704">Każdy inny wymagany dostawca konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-704">Any other required configuration providers.</span></span>

<span data-ttu-id="28f0f-705">Konfiguracja pliku hosta jest włączana przez określenie ścieżki podstawowej aplikacji, `SetBasePath` po której następuje wywołanie jednego z [dostawców konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="28f0f-705">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="28f0f-706">Przykładowa aplikacja używa pliku JSON, *hostsettings.json* i wywołuje <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> do użycia ustawienia konfiguracji hosta pliku.</span><span class="sxs-lookup"><span data-stu-id="28f0f-706">The sample app uses a JSON file, *hostsettings.json* , and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="28f0f-707">Aby dodać [konfigurację zmiennej środowiskowej](xref:fundamentals/configuration/index#environment-variables-configuration-provider) hosta, wywołaj polecenie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> na konstruktorze hosta.</span><span class="sxs-lookup"><span data-stu-id="28f0f-707">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="28f0f-708">`AddEnvironmentVariables` akceptuje opcjonalny prefiks zdefiniowany przez użytkownika.</span><span class="sxs-lookup"><span data-stu-id="28f0f-708">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="28f0f-709">Przykładowa aplikacja używa prefiksu `PREFIX_` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-709">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="28f0f-710">Prefiks jest usuwany, gdy są odczytywane zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="28f0f-710">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="28f0f-711">Po skonfigurowaniu hosta przykładowej aplikacji wartość zmiennej środowiskowej dla `PREFIX_ENVIRONMENT` klucza będzie wartością konfiguracji hosta `environment` .</span><span class="sxs-lookup"><span data-stu-id="28f0f-711">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="28f0f-712">Podczas tworzenia w przypadku korzystania z [programu Visual Studio](https://visualstudio.microsoft.com) lub uruchamiania aplikacji w programie `dotnet run` zmienne środowiskowe mogą być ustawiane we *właściwościach/launchSettings.jsw* pliku.</span><span class="sxs-lookup"><span data-stu-id="28f0f-712">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="28f0f-713">W [Visual Studio Code](https://code.visualstudio.com/)zmienne środowiskowe mogą być ustawiane w pliku *. programu vscode/launch.js* podczas opracowywania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-713">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="28f0f-714">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="28f0f-714">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="28f0f-715">[Konfiguracja wiersza polecenia](xref:fundamentals/configuration/index#command-line-configuration-provider) jest dodawana przez wywołanie <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-715">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="28f0f-716">Konfiguracja wiersza polecenia jest dodawana jako Ostatnia, aby zezwolić na argumenty wiersza polecenia w celu przesłonięcia konfiguracji udostępnionej przez wcześniejszych dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-716">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="28f0f-717">*hostsettings.js* :</span><span class="sxs-lookup"><span data-stu-id="28f0f-717">*hostsettings.json* :</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="28f0f-718">Dodatkową konfigurację można uzyskać za pomocą programu [ApplicationName](#application-key-name) i kluczy [contentRoot](#content-root) .</span><span class="sxs-lookup"><span data-stu-id="28f0f-718">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="28f0f-719">Przykładowa `HostBuilder` Konfiguracja przy użyciu <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="28f0f-719">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="28f0f-720">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="28f0f-720">ConfigureAppConfiguration</span></span>

<span data-ttu-id="28f0f-721">Konfiguracja aplikacji jest tworzona przez wywołanie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-721">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="28f0f-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> za pomocą programu <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> można utworzyć <xref:Microsoft.Extensions.Configuration.IConfiguration> dla aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="28f0f-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="28f0f-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="28f0f-724">Aplikacja używa dowolnej opcji ustawia wartość ostatnią dla danego klucza.</span><span class="sxs-lookup"><span data-stu-id="28f0f-724">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="28f0f-725">Konfiguracja utworzona przez program <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> jest dostępna w [HostBuilderContext.Configwersja](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) na potrzeby kolejnych operacji i w <xref:Microsoft.Extensions.Hosting.IHost.Services*> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-725">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="28f0f-726">Konfiguracja aplikacji automatycznie otrzymuje konfigurację hosta dostarczoną przez [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="28f0f-726">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="28f0f-727">Przykładowa konfiguracja aplikacji przy użyciu <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> :</span><span class="sxs-lookup"><span data-stu-id="28f0f-727">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="28f0f-728">*:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="28f0f-728">*:::no-loc(appsettings.json):::* :</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/:::no-loc(appsettings.json):::)]

<span data-ttu-id="28f0f-729">*appsettings.Development.js* :</span><span class="sxs-lookup"><span data-stu-id="28f0f-729">*appsettings.Development.json* :</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="28f0f-730">*appsettings.Production.js* :</span><span class="sxs-lookup"><span data-stu-id="28f0f-730">*appsettings.Production.json* :</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="28f0f-731">Aby przenieść pliki ustawień do katalogu wyjściowego, określ pliki ustawień jako [elementy projektu MSBuild](/visualstudio/msbuild/common-msbuild-project-items) w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="28f0f-731">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="28f0f-732">Aplikacja Przykładowa przenosi pliki ustawień aplikacji JSON i *hostsettings.js* przy użyciu następującego `<Content>` elementu:</span><span class="sxs-lookup"><span data-stu-id="28f0f-732">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="28f0f-733">Metody rozszerzenia konfiguracji, takie jak <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> i <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> wymagające dodatkowych pakietów NuGet, takie jak [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) i [Microsoft.Extensions.Configwersja. EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="28f0f-733">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="28f0f-734">Jeśli aplikacja nie używa [pakietu Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), należy dodać te pakiety do projektu oprócz podstawowego pakietu [Microsoft.Extensions.Configwersja](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) .</span><span class="sxs-lookup"><span data-stu-id="28f0f-734">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="28f0f-735">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="28f0f-735">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="28f0f-736">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="28f0f-736">ConfigureServices</span></span>

<span data-ttu-id="28f0f-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> dodaje usługi do kontenera [iniekcji zależności](xref:fundamentals/dependency-injection) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="28f0f-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="28f0f-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="28f0f-739">Usługa hostowana jest klasą z logiką zadań w tle, która implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> interfejs.</span><span class="sxs-lookup"><span data-stu-id="28f0f-739">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="28f0f-740">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="28f0f-740">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="28f0f-741">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) używa `AddHostedService` metody rozszerzającej, aby dodać usługę dla zdarzeń okresu istnienia, `LifetimeEventsHostedService` oraz zadanie w tle czasu, `TimedHostedService` do aplikacji:</span><span class="sxs-lookup"><span data-stu-id="28f0f-741">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="28f0f-742">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="28f0f-742">ConfigureLogging</span></span>

<span data-ttu-id="28f0f-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> dodaje delegata do konfigurowania podanego elementu <xref:Microsoft.Extensions.Logging.ILoggingBuilder> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="28f0f-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="28f0f-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="28f0f-745">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="28f0f-745">UseConsoleLifetime</span></span>

<span data-ttu-id="28f0f-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>nasłuchuje <kbd>kombinacji klawiszy CTRL</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM oraz wywołań, <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> Aby rozpocząć proces zamykania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="28f0f-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> odblokowuje rozszerzenia, takie jak [RunAsync](#runasync) i [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="28f0f-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="28f0f-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` jest wstępnie zarejestrowany jako domyślna implementacja okresu istnienia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="28f0f-749">Używany jest ostatni zarejestrowany okres istnienia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-749">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="28f0f-750">Konfiguracja kontenera</span><span class="sxs-lookup"><span data-stu-id="28f0f-750">Container configuration</span></span>

<span data-ttu-id="28f0f-751">Aby zapewnić obsługę podłączania w innych kontenerach, host może akceptować <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-751">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="28f0f-752">Dostarczenie fabryki nie jest częścią rejestracji typu DI Container, ale zamiast tego jest wewnętrznym hostem używanym do tworzenia konkretnych kontenerów DI.</span><span class="sxs-lookup"><span data-stu-id="28f0f-752">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="28f0f-753">[UseServiceProviderFactory (IServiceProviderFactory &lt; TContainerBuilder &gt; )](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) zastępuje domyślną fabrykę używaną do utworzenia dostawcy usług aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-753">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="28f0f-754">Niestandardowa konfiguracja kontenera jest zarządzana przez <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> metodę.</span><span class="sxs-lookup"><span data-stu-id="28f0f-754">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="28f0f-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> Program zapewnia silnie wpisaną funkcję konfigurowania kontenera na podstawie podstawowego interfejsu API hosta.</span><span class="sxs-lookup"><span data-stu-id="28f0f-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="28f0f-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> może być wywoływana wiele razy z wynikami.</span><span class="sxs-lookup"><span data-stu-id="28f0f-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="28f0f-757">Utwórz kontener usługi dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="28f0f-757">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="28f0f-758">Podaj fabrykę kontenera usługi:</span><span class="sxs-lookup"><span data-stu-id="28f0f-758">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="28f0f-759">Użyj fabryki i skonfiguruj niestandardowy kontener usługi dla aplikacji:</span><span class="sxs-lookup"><span data-stu-id="28f0f-759">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="28f0f-760">Rozszerzalność</span><span class="sxs-lookup"><span data-stu-id="28f0f-760">Extensibility</span></span>

<span data-ttu-id="28f0f-761">Rozszerzalność hosta jest wykonywana z metodami rozszerzenia w systemie <xref:Microsoft.Extensions.Hosting.IHostBuilder> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-761">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="28f0f-762">Poniższy przykład pokazuje, jak Metoda rozszerzania rozszerza <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementację z przykładem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) przedstawionym w <xref:fundamentals/host/hosted-services> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-762">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="28f0f-763">Aplikacja ustanowi `UseHostedService` metodę rozszerzenia w celu zarejestrowania przesyłanej usługi hostowanej `T` :</span><span class="sxs-lookup"><span data-stu-id="28f0f-763">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="28f0f-764">Zarządzanie hostem</span><span class="sxs-lookup"><span data-stu-id="28f0f-764">Manage the host</span></span>

<span data-ttu-id="28f0f-765"><xref:Microsoft.Extensions.Hosting.IHost>Implementacja jest odpowiedzialna za uruchamianie i zatrzymywanie <xref:Microsoft.Extensions.Hosting.IHostedService> implementacji, które są zarejestrowane w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="28f0f-765">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="28f0f-766">Uruchom</span><span class="sxs-lookup"><span data-stu-id="28f0f-766">Run</span></span>

<span data-ttu-id="28f0f-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> uruchamia aplikację i blokuje wątek wywołujący do momentu wyłączenia hosta:</span><span class="sxs-lookup"><span data-stu-id="28f0f-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="28f0f-768">RunAsync</span><span class="sxs-lookup"><span data-stu-id="28f0f-768">RunAsync</span></span>

<span data-ttu-id="28f0f-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> uruchamia aplikację i zwraca <xref:System.Threading.Tasks.Task> , która kończy się po wyzwoleniu tokenu anulowania lub zamknięcia:</span><span class="sxs-lookup"><span data-stu-id="28f0f-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="28f0f-770">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="28f0f-770">RunConsoleAsync</span></span>

<span data-ttu-id="28f0f-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>włącza obsługę konsoli, kompiluje i uruchamia hosta, a następnie czeka na zamknięcie programu <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="28f0f-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="28f0f-772">Uruchom i StopAsync</span><span class="sxs-lookup"><span data-stu-id="28f0f-772">Start and StopAsync</span></span>

<span data-ttu-id="28f0f-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> uruchamia hosta synchronicznie.</span><span class="sxs-lookup"><span data-stu-id="28f0f-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="28f0f-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> próbuje zatrzymać hosta w ramach podanego limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="28f0f-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="28f0f-775">StartAsync i StopAsync</span><span class="sxs-lookup"><span data-stu-id="28f0f-775">StartAsync and StopAsync</span></span>

<span data-ttu-id="28f0f-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> uruchamia aplikację.</span><span class="sxs-lookup"><span data-stu-id="28f0f-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="28f0f-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> kończy działanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="28f0f-778">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="28f0f-778">WaitForShutdown</span></span>

<span data-ttu-id="28f0f-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>jest wyzwalany za pośrednictwem, na przykład <xref:Microsoft.Extensions.Hosting.IHostLifetime> `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (nasłuchuje dla <kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT lub SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="28f0f-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="28f0f-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> wywołania <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="28f0f-781">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="28f0f-781">WaitForShutdownAsync</span></span>

<span data-ttu-id="28f0f-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> Zwraca wartość <xref:System.Threading.Tasks.Task> , która kończy się, gdy zamknięcie jest wyzwalane za pośrednictwem danego tokenu i wywołań <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> .</span><span class="sxs-lookup"><span data-stu-id="28f0f-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="28f0f-783">Zewnętrzna kontrola</span><span class="sxs-lookup"><span data-stu-id="28f0f-783">External control</span></span>

<span data-ttu-id="28f0f-784">Kontrolę zewnętrzną hosta można osiągnąć przy użyciu metod, które mogą być wywoływane zewnętrznie:</span><span class="sxs-lookup"><span data-stu-id="28f0f-784">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="28f0f-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> jest wywoływana na początku <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> , który czeka na zakończenie przed kontynuowaniem.</span><span class="sxs-lookup"><span data-stu-id="28f0f-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="28f0f-786">Może to służyć do opóźnienia uruchamiania do momentu zasygnalizowania zdarzenia zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="28f0f-786">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="28f0f-787">IHostingEnvironment, interfejs</span><span class="sxs-lookup"><span data-stu-id="28f0f-787">IHostingEnvironment interface</span></span>

<span data-ttu-id="28f0f-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> zawiera informacje o środowisku hostingu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="28f0f-789">Użyj [iniekcji konstruktorów](xref:fundamentals/dependency-injection) , aby uzyskać <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> w celu użycia jej właściwości i metod rozszerzających:</span><span class="sxs-lookup"><span data-stu-id="28f0f-789">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="28f0f-790">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="28f0f-790">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="28f0f-791">IApplicationLifetime, interfejs</span><span class="sxs-lookup"><span data-stu-id="28f0f-791">IApplicationLifetime interface</span></span>

<span data-ttu-id="28f0f-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> zezwala na działania po uruchomieniu i zamknięcia, w tym bezpieczne żądania zamknięcia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="28f0f-793">Trzy właściwości interfejsu są tokenami anulowania używanymi do rejestrowania <xref:System.Action> metod, które definiują zdarzenia uruchamiania i zamykania.</span><span class="sxs-lookup"><span data-stu-id="28f0f-793">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="28f0f-794">Token anulowania</span><span class="sxs-lookup"><span data-stu-id="28f0f-794">Cancellation Token</span></span> | <span data-ttu-id="28f0f-795">Wyzwalane, gdy&#8230;</span><span class="sxs-lookup"><span data-stu-id="28f0f-795">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="28f0f-796">Host został w pełni uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="28f0f-796">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="28f0f-797">Host kończy bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="28f0f-797">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="28f0f-798">Wszystkie żądania powinny być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="28f0f-798">All requests should be processed.</span></span> <span data-ttu-id="28f0f-799">Bloki zamknięcia do momentu zakończenia tego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-799">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="28f0f-800">Host wykonuje bezpieczne zamknięcie.</span><span class="sxs-lookup"><span data-stu-id="28f0f-800">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="28f0f-801">Żądania mogą nadal być przetwarzane.</span><span class="sxs-lookup"><span data-stu-id="28f0f-801">Requests may still be processing.</span></span> <span data-ttu-id="28f0f-802">Bloki zamknięcia do momentu zakończenia tego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-802">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="28f0f-803">Konstruktor — wstrzyknięcie <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> usługi do dowolnej klasy.</span><span class="sxs-lookup"><span data-stu-id="28f0f-803">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="28f0f-804">[Przykładowa aplikacja](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) używa iniekcji konstruktora do `LifetimeEventsHostedService` klasy ( <xref:Microsoft.Extensions.Hosting.IHostedService> implementacji), aby zarejestrować zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="28f0f-804">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="28f0f-805">*LifetimeEventsHostedService.cs* :</span><span class="sxs-lookup"><span data-stu-id="28f0f-805">*LifetimeEventsHostedService.cs* :</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="28f0f-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> żąda zakończenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="28f0f-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="28f0f-807">Następująca Klasa służy <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> do bezpiecznego zamykania aplikacji w przypadku `Shutdown` wywołania metody klasy:</span><span class="sxs-lookup"><span data-stu-id="28f0f-807">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="28f0f-808">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="28f0f-808">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
