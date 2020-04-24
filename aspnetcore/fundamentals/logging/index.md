---
title: Rejestrowanie w programie .NET Core i ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać struktury rejestrowania dostarczonej przez pakiet NuGet Microsoft. Extensions. Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/23/2020
uid: fundamentals/logging/index
ms.openlocfilehash: 7be8cef3377132ed43efde209db67401d7bdb6dc
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110918"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="a1395-103">Rejestrowanie w programie .NET Core i ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a1395-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a1395-104">Autorzy [Dykstra](https://github.com/tdykstra) i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a1395-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a1395-105">Platforma .NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi dostawcami rejestrowania wbudowanych i innych firm.</span><span class="sxs-lookup"><span data-stu-id="a1395-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="a1395-106">W tym artykule pokazano, jak używać interfejsu API rejestrowania z wbudowanymi dostawcami.</span><span class="sxs-lookup"><span data-stu-id="a1395-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="a1395-107">Większość przykładów kodu pokazanych w tym artykule pochodzą z ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="a1395-108">Części tych fragmentów kodu dotyczące rejestrowania mają zastosowanie do dowolnej aplikacji .NET Core, która korzysta z [hosta ogólnego](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="a1395-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="a1395-109">Przykład korzystania z hosta ogólnego w aplikacji konsolowej innej niż sieci Web można znaleźć w pliku *program.cs* [zadania w tle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span><span class="sxs-lookup"><span data-stu-id="a1395-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="a1395-110">Rejestrowanie kodu dla aplikacji bez hosta ogólnego różni się w sposób, w jaki [są dodawane dostawcy](#add-providers) i [są tworzone rejestratory](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="a1395-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="a1395-111">Przykłady kodu niehosta są wyświetlane w tych częściach artykułu.</span><span class="sxs-lookup"><span data-stu-id="a1395-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="a1395-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a1395-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="a1395-113">Dodaj dostawców</span><span class="sxs-lookup"><span data-stu-id="a1395-113">Add providers</span></span>

<span data-ttu-id="a1395-114">Dostawca rejestrowania wyświetla lub przechowuje dzienniki.</span><span class="sxs-lookup"><span data-stu-id="a1395-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="a1395-115">Na przykład dostawca konsoli wyświetla dzienniki w konsoli programu, a Dostawca usługi Azure Application Insights przechowuje je na platformie Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="a1395-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="a1395-116">Dzienniki mogą być wysyłane do wielu miejsc docelowych przez dodanie wielu dostawców.</span><span class="sxs-lookup"><span data-stu-id="a1395-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="a1395-117">Aby dodać dostawcę w aplikacji korzystającej z hosta ogólnego, wywołaj metodę `Add{provider name}` rozszerzenia dostawcy w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a1395-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="a1395-118">W aplikacji konsolowej innej niż host Wywołaj metodę `Add{provider name}` rozszerzenia dostawcy podczas tworzenia: `LoggerFactory`</span><span class="sxs-lookup"><span data-stu-id="a1395-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="a1395-119">`LoggerFactory`i `AddConsole` wymagają `using` instrukcji dla `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="a1395-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="a1395-120">Domyślne wywołanie <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>szablonów projektu ASP.NET Core, które dodaje następujących dostawców rejestrowania:</span><span class="sxs-lookup"><span data-stu-id="a1395-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="a1395-121">Konsola</span><span class="sxs-lookup"><span data-stu-id="a1395-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="a1395-122">Rozpocząć</span><span class="sxs-lookup"><span data-stu-id="a1395-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="a1395-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="a1395-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="a1395-124">[EventLog](#windows-eventlog-provider) (tylko w przypadku uruchamiania w systemie Windows)</span><span class="sxs-lookup"><span data-stu-id="a1395-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="a1395-125">Dostawców domyślnych można zastąpić własnymi opcjami.</span><span class="sxs-lookup"><span data-stu-id="a1395-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="a1395-126">Wywołaj <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>i Dodaj żądanych dostawców.</span><span class="sxs-lookup"><span data-stu-id="a1395-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="a1395-127">Więcej informacji na temat [wbudowanych dostawców rejestrowania](#built-in-logging-providers) i [dostawców rejestrowania innych](#third-party-logging-providers) firm znajduje się w dalszej części artykułu.</span><span class="sxs-lookup"><span data-stu-id="a1395-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="a1395-128">Tworzenie dzienników</span><span class="sxs-lookup"><span data-stu-id="a1395-128">Create logs</span></span>

<span data-ttu-id="a1395-129">Aby utworzyć dzienniki, użyj <xref:Microsoft.Extensions.Logging.ILogger%601> obiektu.</span><span class="sxs-lookup"><span data-stu-id="a1395-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="a1395-130">W aplikacji sieci Web lub hostowanej usłudze Pobierz `ILogger` od iniekcji zależności (di).</span><span class="sxs-lookup"><span data-stu-id="a1395-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="a1395-131">W aplikacjach konsolowych innych niż host Użyj programu `LoggerFactory` , aby utworzyć `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="a1395-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="a1395-132">Poniższy ASP.NET Core przykład tworzy Rejestrator przy użyciu `TodoApiSample.Pages.AboutModel` jako kategorii.</span><span class="sxs-lookup"><span data-stu-id="a1395-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="a1395-133">*Kategoria* dziennika jest ciągiem, który jest skojarzony z każdym dziennikiem.</span><span class="sxs-lookup"><span data-stu-id="a1395-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="a1395-134">`ILogger<T>` Wystąpienie zapewniane przez program di tworzy dzienniki, które mają w pełni kwalifikowaną nazwę `T` typu jako kategorię.</span><span class="sxs-lookup"><span data-stu-id="a1395-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="a1395-135">Poniższy przykład aplikacji nieprzyjmującej konsoli tworzy Rejestrator z `LoggingConsoleApp.Program` kategorią.</span><span class="sxs-lookup"><span data-stu-id="a1395-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="a1395-136">W poniższych przykładach ASP.NET Core i aplikacji konsoli Rejestrator służy do tworzenia dzienników z użyciem `Information` jako poziom.</span><span class="sxs-lookup"><span data-stu-id="a1395-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="a1395-137">*Poziom* dziennika wskazuje ważność rejestrowanego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="a1395-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="a1395-138">[Poziomy](#log-level) i [Kategorie](#log-category) zostały wyjaśnione bardziej szczegółowo w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="a1395-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="a1395-139">Tworzenie dzienników w klasie programu</span><span class="sxs-lookup"><span data-stu-id="a1395-139">Create logs in the Program class</span></span>

<span data-ttu-id="a1395-140">Aby napisać dzienniki w `Program` klasie ASP.NET Core aplikacji, Pobierz `ILogger` wystąpienie z programu di po skompilowaniu hosta:</span><span class="sxs-lookup"><span data-stu-id="a1395-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="a1395-141">Rejestrowanie podczas konstruowania hosta nie jest bezpośrednio obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="a1395-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="a1395-142">Można jednak użyć osobnego rejestratora.</span><span class="sxs-lookup"><span data-stu-id="a1395-142">However, a separate logger can be used.</span></span> <span data-ttu-id="a1395-143">W poniższym przykładzie jest używany Rejestrator [Serilog](https://serilog.net/) do logowania `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a1395-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="a1395-144">`AddSerilog`używa konfiguracji statycznej określonej w `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="a1395-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="a1395-145">Tworzenie dzienników w klasie startowej</span><span class="sxs-lookup"><span data-stu-id="a1395-145">Create logs in the Startup class</span></span>

<span data-ttu-id="a1395-146">Aby napisać dzienniki w `Startup.Configure` metodzie aplikacji ASP.NET Core, Dołącz `ILogger` parametr do sygnatury metody:</span><span class="sxs-lookup"><span data-stu-id="a1395-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="a1395-147">Zapisywanie dzienników przed ukończeniem instalacji programu DI Container w `Startup.ConfigureServices` metodzie nie jest obsługiwane:</span><span class="sxs-lookup"><span data-stu-id="a1395-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="a1395-148">Iniekcja rejestratora `Startup` do konstruktora nie jest obsługiwana.</span><span class="sxs-lookup"><span data-stu-id="a1395-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="a1395-149">Iniekcja rejestratora `Startup.ConfigureServices` do sygnatury metody nie jest obsługiwana</span><span class="sxs-lookup"><span data-stu-id="a1395-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="a1395-150">Przyczyną tego ograniczenia jest to, że rejestrowanie jest zależne od typu i konfiguracji, która z tego powodu zależy od DI.</span><span class="sxs-lookup"><span data-stu-id="a1395-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="a1395-151">Kontener DI nie jest ustawiany do momentu `ConfigureServices` zakończenia.</span><span class="sxs-lookup"><span data-stu-id="a1395-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="a1395-152">Wstrzykiwanie konstruktora rejestratora do `Startup` działa we wcześniejszych wersjach ASP.NET Core, ponieważ dla hosta sieci Web jest tworzony oddzielny kontener di.</span><span class="sxs-lookup"><span data-stu-id="a1395-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="a1395-153">Aby uzyskać informacje o tym, dlaczego dla hosta generycznego jest tworzony tylko jeden kontener, zobacz [zawiadomienie o rozdzieleniu zmian](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="a1395-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="a1395-154">Jeśli trzeba skonfigurować usługę, która zależy od programu `ILogger<T>`, można to zrobić za pomocą iniekcji konstruktora lub dostarczając metodę fabryki.</span><span class="sxs-lookup"><span data-stu-id="a1395-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="a1395-155">Podejście metody fabryki jest zalecane tylko wtedy, gdy nie ma żadnych innych opcji.</span><span class="sxs-lookup"><span data-stu-id="a1395-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="a1395-156">Załóżmy na przykład, że musisz wypełnić Właściwość usługą z:</span><span class="sxs-lookup"><span data-stu-id="a1395-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="a1395-157">Poprzedni wyróżniony kod jest `Func` uruchomiony, gdy jest uruchamiany po raz pierwszy kontener di musi utworzyć wystąpienie. `MyService`</span><span class="sxs-lookup"><span data-stu-id="a1395-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="a1395-158">W ten sposób można uzyskać dostęp do dowolnych zarejestrowanych usług.</span><span class="sxs-lookup"><span data-stu-id="a1395-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor"></a><span data-ttu-id="a1395-159">Tworzenie dzienników w Blazor</span><span class="sxs-lookup"><span data-stu-id="a1395-159">Create logs in Blazor</span></span>

#### <a name="blazor-webassembly"></a><span data-ttu-id="a1395-160">Zestaw WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="a1395-160">Blazor WebAssembly</span></span>

<span data-ttu-id="a1395-161">Konfigurowanie rejestrowania w aplikacjach Blazor webassembly z `WebAssemblyHostBuilder.Logging` właściwością w `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="a1395-161">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="a1395-162">`Logging` Właściwość jest typu <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, więc wszystkie dostępne metody rozszerzenia <xref:Microsoft.Extensions.Logging.ILoggingBuilder> są również dostępne w systemie `Logging`.</span><span class="sxs-lookup"><span data-stu-id="a1395-162">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

#### <a name="log-in-razor-components"></a><span data-ttu-id="a1395-163">Logowanie składników Razor</span><span class="sxs-lookup"><span data-stu-id="a1395-163">Log in Razor components</span></span>

<span data-ttu-id="a1395-164">Rejestratory respektują konfigurację uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-164">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="a1395-165">`using` Dyrektywa <xref:Microsoft.Extensions.Logging> for jest wymagana do obsługi uzupełniania technologii IntelliSense dla interfejsów API, takich <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> jak <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>i.</span><span class="sxs-lookup"><span data-stu-id="a1395-165">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="a1395-166">Poniższy przykład demonstruje rejestrowanie przy użyciu <xref:Microsoft.Extensions.Logging.ILogger> składników Razor:</span><span class="sxs-lookup"><span data-stu-id="a1395-166">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="a1395-167">Poniższy przykład demonstruje rejestrowanie przy użyciu <xref:Microsoft.Extensions.Logging.ILoggerFactory> składników Razor:</span><span class="sxs-lookup"><span data-stu-id="a1395-167">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="a1395-168">Brak metod rejestratora asynchronicznego</span><span class="sxs-lookup"><span data-stu-id="a1395-168">No asynchronous logger methods</span></span>

<span data-ttu-id="a1395-169">Rejestracja powinna być tak szybka, że nie jest to koszt wydajności kodu asynchronicznego.</span><span class="sxs-lookup"><span data-stu-id="a1395-169">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="a1395-170">Jeśli magazyn danych rejestrowania jest wolny, nie zapisuj go bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="a1395-170">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="a1395-171">Najpierw Rozważ zapisanie komunikatów dziennika do szybkiego sklepu, a następnie przeniesienie ich do wolnego magazynu później.</span><span class="sxs-lookup"><span data-stu-id="a1395-171">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="a1395-172">Na przykład jeśli rejestrujesz się do SQL Server, nie chcesz tego robić bezpośrednio w `Log` metodzie, ponieważ `Log` metody są synchroniczne.</span><span class="sxs-lookup"><span data-stu-id="a1395-172">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="a1395-173">Zamiast tego można synchronicznie dodawać komunikaty dziennika do kolejki w pamięci, a proces roboczy w tle ściągał komunikaty z kolejki, aby wykonać asynchroniczne działanie wypychania danych do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a1395-173">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="a1395-174">Aby uzyskać więcej informacji, zobacz [ten](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problem w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="a1395-174">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="a1395-175">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="a1395-175">Configuration</span></span>

<span data-ttu-id="a1395-176">Konfiguracja dostawcy rejestrowania jest świadczona przez co najmniej jednego dostawcę konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="a1395-176">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="a1395-177">Formaty plików (INI, JSON i XML).</span><span class="sxs-lookup"><span data-stu-id="a1395-177">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="a1395-178">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="a1395-178">Command-line arguments.</span></span>
* <span data-ttu-id="a1395-179">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="a1395-179">Environment variables.</span></span>
* <span data-ttu-id="a1395-180">Obiekty platformy .NET w pamięci.</span><span class="sxs-lookup"><span data-stu-id="a1395-180">In-memory .NET objects.</span></span>
* <span data-ttu-id="a1395-181">Magazyn niezaszyfrowanego [klucza tajnego](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="a1395-181">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="a1395-182">Zaszyfrowany magazyn użytkowników, taki jak [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="a1395-182">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="a1395-183">Dostawcy niestandardowi (instalowani lub utworzony).</span><span class="sxs-lookup"><span data-stu-id="a1395-183">Custom providers (installed or created).</span></span>

<span data-ttu-id="a1395-184">Na przykład konfiguracja rejestrowania jest zwykle dostarczana przez `Logging` sekcję plików ustawień aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-184">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="a1395-185">Poniższy przykład pokazuje zawartość typowej wartości *appSettings. Plik Development. JSON* :</span><span class="sxs-lookup"><span data-stu-id="a1395-185">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="a1395-186">`Logging` Właściwość może mieć `LogLevel` właściwości dostawcy dzienników i.</span><span class="sxs-lookup"><span data-stu-id="a1395-186">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="a1395-187">`LogLevel` Właściwość w obszarze `Logging` określa minimalny [poziom](#log-level) rejestrowania wybranych kategorii.</span><span class="sxs-lookup"><span data-stu-id="a1395-187">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="a1395-188">`System` W przykładzie, `Microsoft` i kategorie są rejestrowane na `Information` poziomie, a wszystkie inne logowania na `Debug` poziomie.</span><span class="sxs-lookup"><span data-stu-id="a1395-188">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="a1395-189">Inne właściwości w `Logging` obszarze Określ dostawców rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a1395-189">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="a1395-190">Przykład dotyczy dostawcy konsoli.</span><span class="sxs-lookup"><span data-stu-id="a1395-190">The example is for the Console provider.</span></span> <span data-ttu-id="a1395-191">Jeśli dostawca obsługuje [zakresy rejestrowania](#log-scopes), wskazuje `IncludeScopes` , czy są one włączone.</span><span class="sxs-lookup"><span data-stu-id="a1395-191">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="a1395-192">Właściwość dostawcy (taka jak `Console` w przykładzie) może także określić `LogLevel` właściwość.</span><span class="sxs-lookup"><span data-stu-id="a1395-192">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="a1395-193">`LogLevel`w obszarze dostawca Określa poziomy do rejestrowania dla tego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="a1395-193">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="a1395-194">Jeśli w programie `Logging.{providername}.LogLevel`są określone poziomy, zastępują one wszystko `Logging.LogLevel`ustawione w.</span><span class="sxs-lookup"><span data-stu-id="a1395-194">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="a1395-195">Interfejs API rejestrowania nie zawiera scenariusza zmiany poziomów rejestrowania, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="a1395-195">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="a1395-196">Niektórzy dostawcy konfiguracji mogą jednak ponownie ładować konfigurację, która zacznie natychmiastowo wpływać na konfigurację rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a1395-196">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="a1395-197">Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider), który jest dodawany przez `CreateDefaultBuilder` program do odczytu plików ustawień, ponownie ładuje konfigurację rejestrowania domyślnie.</span><span class="sxs-lookup"><span data-stu-id="a1395-197">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="a1395-198">Jeśli konfiguracja zostanie zmieniona w kodzie w czasie, gdy aplikacja jest uruchomiona, aplikacja może wywołać [IConfigurationRoot. reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) , aby zaktualizować konfigurację rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-198">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="a1395-199">Informacje o implementowaniu dostawców konfiguracji znajdują <xref:fundamentals/configuration/index>się w temacie.</span><span class="sxs-lookup"><span data-stu-id="a1395-199">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="a1395-200">Przykładowe dane wyjściowe rejestrowania</span><span class="sxs-lookup"><span data-stu-id="a1395-200">Sample logging output</span></span>

<span data-ttu-id="a1395-201">W przypadku przykładowego kodu podanego w poprzedniej sekcji Dzienniki są wyświetlane w konsoli programu, gdy aplikacja jest uruchamiana z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="a1395-201">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="a1395-202">Oto przykład danych wyjściowych konsoli:</span><span class="sxs-lookup"><span data-stu-id="a1395-202">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

<span data-ttu-id="a1395-203">Poprzednie dzienniki zostały wygenerowane przez utworzenie żądania HTTP GET do przykładowej aplikacji w lokalizacji `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="a1395-203">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="a1395-204">Oto przykład tych samych dzienników, które są wyświetlane w oknie debugowania podczas uruchamiania przykładowej aplikacji w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a1395-204">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

<span data-ttu-id="a1395-205">Dzienniki utworzone przez `ILogger` wywołania pokazane w poprzedniej sekcji zaczynają się od "TodoApiSample".</span><span class="sxs-lookup"><span data-stu-id="a1395-205">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="a1395-206">Dzienniki zaczynające się od kategorii "Microsoft" pochodzą z kodu ASP.NET Core Framework.</span><span class="sxs-lookup"><span data-stu-id="a1395-206">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="a1395-207">ASP.NET Core i kod aplikacji używają tego samego rejestrowania interfejsu API i dostawców.</span><span class="sxs-lookup"><span data-stu-id="a1395-207">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="a1395-208">W pozostałej części tego artykułu opisano niektóre szczegóły i opcje rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a1395-208">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="a1395-209">Pakiety NuGet</span><span class="sxs-lookup"><span data-stu-id="a1395-209">NuGet packages</span></span>

<span data-ttu-id="a1395-210">Interfejsy `ILogger` i `ILoggerFactory` są w [Microsoft. Extensions. Logging. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)i domyślne implementacje dla nich są w [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="a1395-210">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="a1395-211">Kategoria dziennika</span><span class="sxs-lookup"><span data-stu-id="a1395-211">Log category</span></span>

<span data-ttu-id="a1395-212">Po utworzeniu `ILogger` obiektu jest dla niego określona *Kategoria* .</span><span class="sxs-lookup"><span data-stu-id="a1395-212">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="a1395-213">Ta kategoria jest dołączona do każdego komunikatu dziennika utworzonego przez to wystąpienie `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="a1395-213">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="a1395-214">Kategoria może być dowolnym ciągiem, ale Konwencja ma używać nazwy klasy, takiej jak "TodoApi. controllers. TodoController".</span><span class="sxs-lookup"><span data-stu-id="a1395-214">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="a1395-215">Użyj `ILogger<T>` , aby uzyskać `ILogger` wystąpienie używające w pełni kwalifikowanej nazwy typu `T` jako kategorii:</span><span class="sxs-lookup"><span data-stu-id="a1395-215">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="a1395-216">Aby jawnie określić kategorię, wywołaj `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="a1395-216">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="a1395-217">`ILogger<T>`jest odpowiednikiem wywołania `CreateLogger` z w pełni kwalifikowaną nazwą typu `T`.</span><span class="sxs-lookup"><span data-stu-id="a1395-217">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="a1395-218">Poziom dziennika</span><span class="sxs-lookup"><span data-stu-id="a1395-218">Log level</span></span>

<span data-ttu-id="a1395-219">Każdy dziennik Określa <xref:Microsoft.Extensions.Logging.LogLevel> wartość.</span><span class="sxs-lookup"><span data-stu-id="a1395-219">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="a1395-220">Poziom dziennika wskazuje ważność lub ważność.</span><span class="sxs-lookup"><span data-stu-id="a1395-220">The log level indicates the severity or importance.</span></span> <span data-ttu-id="a1395-221">Przykładowo można napisać `Information` dziennik, gdy metoda jest zwykle zakończona, a `Warning` dziennik, gdy metoda zwraca 404, *nie znaleziono* kodu stanu.</span><span class="sxs-lookup"><span data-stu-id="a1395-221">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="a1395-222">Poniższy kod tworzy `Information` i `Warning` rejestruje:</span><span class="sxs-lookup"><span data-stu-id="a1395-222">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="a1395-223">W powyższym kodzie pierwszy parametr jest [identyfikatorem zdarzenia dziennika](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="a1395-223">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="a1395-224">Drugi parametr jest szablonem wiadomości z symbolami zastępczymi dla wartości argumentów dostarczonych przez pozostałe parametry metody.</span><span class="sxs-lookup"><span data-stu-id="a1395-224">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="a1395-225">Parametry metody zostały wyjaśnione w [sekcji szablon komunikatu](#log-message-template) w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="a1395-225">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="a1395-226">Metody rejestrowania, które obejmują poziom w nazwie metody (na przykład `LogInformation` i `LogWarning`) są [metodami rozszerzającymi dla ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="a1395-226">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="a1395-227">Te metody wywołują `Log` metodę, która pobiera `LogLevel` parametr.</span><span class="sxs-lookup"><span data-stu-id="a1395-227">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="a1395-228">`Log` Metodę można wywołać bezpośrednio zamiast jednej z tych metod rozszerzających, ale składnia jest stosunkowo skomplikowana.</span><span class="sxs-lookup"><span data-stu-id="a1395-228">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="a1395-229">Aby uzyskać więcej informacji, <xref:Microsoft.Extensions.Logging.ILogger> Zobacz i [kod źródłowy rozszerzeń rejestratora](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="a1395-229">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="a1395-230">ASP.NET Core definiuje następujące poziomy dziennika uporządkowane w tym miejscu od najniższej do najwyższej wagi.</span><span class="sxs-lookup"><span data-stu-id="a1395-230">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="a1395-231">Ślad = 0</span><span class="sxs-lookup"><span data-stu-id="a1395-231">Trace = 0</span></span>

  <span data-ttu-id="a1395-232">Aby uzyskać informacje, które są zazwyczaj cenne tylko dla debugowania.</span><span class="sxs-lookup"><span data-stu-id="a1395-232">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="a1395-233">Komunikaty te mogą zawierać poufne dane aplikacji, dlatego nie powinny być włączone w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="a1395-233">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="a1395-234">*Domyślnie wyłączona.*</span><span class="sxs-lookup"><span data-stu-id="a1395-234">*Disabled by default.*</span></span>

* <span data-ttu-id="a1395-235">Debuguj = 1</span><span class="sxs-lookup"><span data-stu-id="a1395-235">Debug = 1</span></span>

  <span data-ttu-id="a1395-236">Informacje, które mogą być przydatne podczas tworzenia i debugowania.</span><span class="sxs-lookup"><span data-stu-id="a1395-236">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="a1395-237">Przykład: `Entering method Configure with flag set to true.` Włącz `Debug` dzienniki poziomów w środowisku produkcyjnym tylko w przypadku rozwiązywania problemów, ze względu na dużą ilość dzienników.</span><span class="sxs-lookup"><span data-stu-id="a1395-237">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="a1395-238">Informacje = 2</span><span class="sxs-lookup"><span data-stu-id="a1395-238">Information = 2</span></span>

  <span data-ttu-id="a1395-239">Do śledzenia ogólnego przepływu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-239">For tracking the general flow of the app.</span></span> <span data-ttu-id="a1395-240">Te dzienniki zwykle mają pewną wartość długoterminową.</span><span class="sxs-lookup"><span data-stu-id="a1395-240">These logs typically have some long-term value.</span></span> <span data-ttu-id="a1395-241">Przykład: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="a1395-241">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="a1395-242">Ostrzeżenie = 3</span><span class="sxs-lookup"><span data-stu-id="a1395-242">Warning = 3</span></span>

  <span data-ttu-id="a1395-243">Dla nietypowych lub nieoczekiwanych zdarzeń w przepływie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-243">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="a1395-244">Mogą to być błędy lub inne warunki, które nie powodują zatrzymania aplikacji, ale konieczne może być zbadanie.</span><span class="sxs-lookup"><span data-stu-id="a1395-244">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="a1395-245">Obsłużone wyjątki są typowym miejscem do korzystania `Warning` z poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="a1395-245">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="a1395-246">Przykład: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="a1395-246">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="a1395-247">Błąd = 4</span><span class="sxs-lookup"><span data-stu-id="a1395-247">Error = 4</span></span>

  <span data-ttu-id="a1395-248">W przypadku błędów i wyjątków, których nie można obsłużyć.</span><span class="sxs-lookup"><span data-stu-id="a1395-248">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="a1395-249">Te komunikaty wskazują niepowodzenie w bieżącym działaniu lub operacji (np. bieżące żądanie HTTP), a nie awaria całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-249">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="a1395-250">Przykładowy komunikat dziennika:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="a1395-250">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="a1395-251">Krytyczne = 5</span><span class="sxs-lookup"><span data-stu-id="a1395-251">Critical = 5</span></span>

  <span data-ttu-id="a1395-252">Dla niepowodzeń, które wymagają natychmiastowej uwagi.</span><span class="sxs-lookup"><span data-stu-id="a1395-252">For failures that require immediate attention.</span></span> <span data-ttu-id="a1395-253">Przykłady: scenariusze utraty danych, brak miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="a1395-253">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="a1395-254">Poziom dziennika służy do kontrolowania, ile danych wyjściowych dziennika jest zapisywana w określonym nośniku lub oknie wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="a1395-254">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="a1395-255">Przykład:</span><span class="sxs-lookup"><span data-stu-id="a1395-255">For example:</span></span>

* <span data-ttu-id="a1395-256">W środowisku produkcyjnym:</span><span class="sxs-lookup"><span data-stu-id="a1395-256">In production:</span></span>
  * <span data-ttu-id="a1395-257">Rejestrowanie na poziomie `Trace` za `Information` pomocą poziomów zapewnia wysoką liczbę szczegółowych komunikatów dziennika.</span><span class="sxs-lookup"><span data-stu-id="a1395-257">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="a1395-258">Aby kontrolować koszty i nie przekraczać limitów magazynowania danych `Trace` , `Information` należy rejestrować komunikaty na poziomie w magazynach o wysokim poziomie ilości danych.</span><span class="sxs-lookup"><span data-stu-id="a1395-258">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="a1395-259">Rejestrowanie na `Warning` poziomie `Critical` poziomów zwykle generuje mniejszą liczbę mniejszych komunikatów dzienników.</span><span class="sxs-lookup"><span data-stu-id="a1395-259">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="a1395-260">W związku z tym koszty i limity magazynu zazwyczaj nie są problemem, co skutkuje większą elastycznością wyboru magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="a1395-260">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="a1395-261">Podczas tworzenia:</span><span class="sxs-lookup"><span data-stu-id="a1395-261">During development:</span></span>
  * <span data-ttu-id="a1395-262">Rejestruj `Warning` `Critical` komunikaty w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="a1395-262">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="a1395-263">Dodawanie `Trace` `Information` komunikatów podczas rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="a1395-263">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="a1395-264">W sekcji [filtrowanie dzienników](#log-filtering) w dalszej części tego artykułu wyjaśniono, jak kontrolować poziomy dzienników obsługiwane przez dostawcę.</span><span class="sxs-lookup"><span data-stu-id="a1395-264">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="a1395-265">ASP.NET Core zapisuje dzienniki dla zdarzeń struktury.</span><span class="sxs-lookup"><span data-stu-id="a1395-265">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="a1395-266">Przykłady dzienników znajdujące się wcześniej w tym artykule nie `Information` wykluczają dzienników `Debug` poniżej `Trace` , dlatego nie zostały utworzone dzienniki.</span><span class="sxs-lookup"><span data-stu-id="a1395-266">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="a1395-267">Oto przykład dzienników konsoli utworzonych przez uruchomienie przykładowej aplikacji skonfigurowanej do wyświetlania `Debug` dzienników:</span><span class="sxs-lookup"><span data-stu-id="a1395-267">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="a1395-268">Identyfikator zdarzenia dziennika</span><span class="sxs-lookup"><span data-stu-id="a1395-268">Log event ID</span></span>

<span data-ttu-id="a1395-269">Każdy dziennik może określać *Identyfikator zdarzenia*.</span><span class="sxs-lookup"><span data-stu-id="a1395-269">Each log can specify an *event ID*.</span></span> <span data-ttu-id="a1395-270">Aplikacja Przykładowa wykonuje to przy użyciu lokalnie zdefiniowanej `LoggingEvents` klasy:</span><span class="sxs-lookup"><span data-stu-id="a1395-270">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="a1395-271">Identyfikator zdarzenia kojarzy zestaw zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="a1395-271">An event ID associates a set of events.</span></span> <span data-ttu-id="a1395-272">Na przykład wszystkie dzienniki związane z wyświetlaniem listy elementów na stronie mogą być 1001.</span><span class="sxs-lookup"><span data-stu-id="a1395-272">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="a1395-273">Dostawca rejestrowania może przechowywać identyfikator zdarzenia w polu identyfikatora, w komunikacie rejestrowania lub wcale.</span><span class="sxs-lookup"><span data-stu-id="a1395-273">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="a1395-274">Dostawca debugowania nie pokazuje identyfikatorów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="a1395-274">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="a1395-275">Dostawca konsoli pokazuje identyfikatory zdarzeń w nawiasach po kategorii:</span><span class="sxs-lookup"><span data-stu-id="a1395-275">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="a1395-276">Szablon komunikatu dziennika</span><span class="sxs-lookup"><span data-stu-id="a1395-276">Log message template</span></span>

<span data-ttu-id="a1395-277">Każdy dziennik Określa szablon wiadomości.</span><span class="sxs-lookup"><span data-stu-id="a1395-277">Each log specifies a message template.</span></span> <span data-ttu-id="a1395-278">Szablon wiadomości może zawierać symbole zastępcze, dla których podano argumenty.</span><span class="sxs-lookup"><span data-stu-id="a1395-278">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="a1395-279">Użyj nazw dla symboli zastępczych, a nie liczby.</span><span class="sxs-lookup"><span data-stu-id="a1395-279">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="a1395-280">Kolejność symboli zastępczych, nie ich nazw, określa, które parametry są używane do dostarczania ich wartości.</span><span class="sxs-lookup"><span data-stu-id="a1395-280">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="a1395-281">W poniższym kodzie Zwróć uwagę, że nazwy parametrów są poza kolejnością w szablonie wiadomości:</span><span class="sxs-lookup"><span data-stu-id="a1395-281">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="a1395-282">Ten kod tworzy komunikat dziennika z wartościami parametrów w kolejności:</span><span class="sxs-lookup"><span data-stu-id="a1395-282">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="a1395-283">Struktura rejestrowania działa w ten sposób, aby dostawcy rejestrowania mogli zaimplementować [Rejestrowanie semantyczne, znane także jako rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="a1395-283">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="a1395-284">Same argumenty są przesyłane do systemu rejestrowania, a nie tylko dla sformatowanego szablonu wiadomości.</span><span class="sxs-lookup"><span data-stu-id="a1395-284">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="a1395-285">Te informacje umożliwiają dostawcom rejestrowania przechowywanie wartości parametrów jako pól.</span><span class="sxs-lookup"><span data-stu-id="a1395-285">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="a1395-286">Na przykład załóżmy, że wywołania metod rejestratora wyglądają następująco:</span><span class="sxs-lookup"><span data-stu-id="a1395-286">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="a1395-287">W przypadku wysyłania dzienników do usługi Azure Table Storage każda jednostka tabeli platformy Azure może mieć `ID` właściwości i `RequestTime` , które upraszczają zapytania dotyczące danych dziennika.</span><span class="sxs-lookup"><span data-stu-id="a1395-287">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="a1395-288">Zapytanie może znaleźć wszystkie dzienniki w określonym `RequestTime` zakresie bez analizowania limitu czasu wiadomości tekstowej.</span><span class="sxs-lookup"><span data-stu-id="a1395-288">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="a1395-289">Wyjątki rejestrowania</span><span class="sxs-lookup"><span data-stu-id="a1395-289">Logging exceptions</span></span>

<span data-ttu-id="a1395-290">Metody rejestratora mają przeciążenia umożliwiające przekazanie wyjątku, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a1395-290">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="a1395-291">Różni dostawcy obsługują informacje o wyjątkach na różne sposoby.</span><span class="sxs-lookup"><span data-stu-id="a1395-291">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="a1395-292">Oto przykład danych wyjściowych dostawcy debugowania z kodu pokazanego powyżej.</span><span class="sxs-lookup"><span data-stu-id="a1395-292">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="a1395-293">Filtrowanie dzienników</span><span class="sxs-lookup"><span data-stu-id="a1395-293">Log filtering</span></span>

<span data-ttu-id="a1395-294">Można określić minimalny poziom rejestrowania dla określonego dostawcy i kategorii lub dla wszystkich dostawców lub wszystkich kategorii.</span><span class="sxs-lookup"><span data-stu-id="a1395-294">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="a1395-295">Wszystkie dzienniki poniżej minimalnego poziomu nie są przesyłane do tego dostawcy, więc nie są wyświetlane ani przechowywane.</span><span class="sxs-lookup"><span data-stu-id="a1395-295">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="a1395-296">Aby pominąć wszystkie dzienniki, określ `LogLevel.None` jako minimalny poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="a1395-296">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="a1395-297">Wartość całkowita `LogLevel.None` wynosi 6, która jest większa niż `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="a1395-297">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="a1395-298">Utwórz reguły filtru w konfiguracji</span><span class="sxs-lookup"><span data-stu-id="a1395-298">Create filter rules in configuration</span></span>

<span data-ttu-id="a1395-299">Kod szablonu projektu wywołuje `CreateDefaultBuilder` w celu skonfigurowania rejestrowania dla dostawców konsoli, debugowania i EventSource (ASP.NET Core 2,2 lub nowszych).</span><span class="sxs-lookup"><span data-stu-id="a1395-299">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="a1395-300">`CreateDefaultBuilder` Metoda konfiguruje rejestrowanie, aby wyszukać konfigurację w `Logging` sekcji, jak wyjaśniono [wcześniej w tym artykule](#configuration).</span><span class="sxs-lookup"><span data-stu-id="a1395-300">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="a1395-301">Dane konfiguracyjne określają minimalne poziomy dziennika według dostawcy i kategorii, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a1395-301">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="a1395-302">Ten kod JSON tworzy sześć reguł filtrowania: jeden dla dostawcy debugowania, cztery dla dostawcy konsoli i jeden dla wszystkich dostawców.</span><span class="sxs-lookup"><span data-stu-id="a1395-302">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="a1395-303">Dla każdego dostawcy wybierana jest pojedyncza reguła, `ILogger` gdy tworzony jest obiekt.</span><span class="sxs-lookup"><span data-stu-id="a1395-303">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="a1395-304">Filtrowanie reguł w kodzie</span><span class="sxs-lookup"><span data-stu-id="a1395-304">Filter rules in code</span></span>

<span data-ttu-id="a1395-305">Poniższy przykład pokazuje, jak zarejestrować reguły filtru w kodzie:</span><span class="sxs-lookup"><span data-stu-id="a1395-305">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="a1395-306">Drugi `AddFilter` określa dostawcę debugowania za pomocą nazwy typu.</span><span class="sxs-lookup"><span data-stu-id="a1395-306">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="a1395-307">Pierwszy `AddFilter` ma zastosowanie do wszystkich dostawców, ponieważ nie określa typu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="a1395-307">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="a1395-308">Jak są stosowane reguły filtrowania</span><span class="sxs-lookup"><span data-stu-id="a1395-308">How filtering rules are applied</span></span>

<span data-ttu-id="a1395-309">Dane konfiguracji i `AddFilter` kod przedstawiony w powyższych przykładach tworzą reguły pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="a1395-309">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="a1395-310">Pierwsze sześć pochodzi z przykładu konfiguracji, a ostatnie dwa pochodzą z przykładu kodu.</span><span class="sxs-lookup"><span data-stu-id="a1395-310">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="a1395-311">Liczba</span><span class="sxs-lookup"><span data-stu-id="a1395-311">Number</span></span> | <span data-ttu-id="a1395-312">Dostawca</span><span class="sxs-lookup"><span data-stu-id="a1395-312">Provider</span></span>      | <span data-ttu-id="a1395-313">Kategorie zaczynające się od...</span><span class="sxs-lookup"><span data-stu-id="a1395-313">Categories that begin with ...</span></span>          | <span data-ttu-id="a1395-314">Minimalny poziom rejestrowania</span><span class="sxs-lookup"><span data-stu-id="a1395-314">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="a1395-315">1</span><span class="sxs-lookup"><span data-stu-id="a1395-315">1</span></span>      | <span data-ttu-id="a1395-316">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="a1395-316">Debug</span></span>         | <span data-ttu-id="a1395-317">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="a1395-317">All categories</span></span>                          | <span data-ttu-id="a1395-318">Informacje</span><span class="sxs-lookup"><span data-stu-id="a1395-318">Information</span></span>       |
| <span data-ttu-id="a1395-319">2</span><span class="sxs-lookup"><span data-stu-id="a1395-319">2</span></span>      | <span data-ttu-id="a1395-320">Konsola</span><span class="sxs-lookup"><span data-stu-id="a1395-320">Console</span></span>       | <span data-ttu-id="a1395-321">Microsoft. AspNetCore. MVC. Razor. Internal</span><span class="sxs-lookup"><span data-stu-id="a1395-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="a1395-322">Ostrzeżenie</span><span class="sxs-lookup"><span data-stu-id="a1395-322">Warning</span></span>           |
| <span data-ttu-id="a1395-323">3</span><span class="sxs-lookup"><span data-stu-id="a1395-323">3</span></span>      | <span data-ttu-id="a1395-324">Konsola</span><span class="sxs-lookup"><span data-stu-id="a1395-324">Console</span></span>       | <span data-ttu-id="a1395-325">Microsoft. AspNetCore. MVC. Razor. Razor</span><span class="sxs-lookup"><span data-stu-id="a1395-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="a1395-326">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="a1395-326">Debug</span></span>             |
| <span data-ttu-id="a1395-327">4</span><span class="sxs-lookup"><span data-stu-id="a1395-327">4</span></span>      | <span data-ttu-id="a1395-328">Konsola</span><span class="sxs-lookup"><span data-stu-id="a1395-328">Console</span></span>       | <span data-ttu-id="a1395-329">Microsoft. AspNetCore. MVC. Razor</span><span class="sxs-lookup"><span data-stu-id="a1395-329">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="a1395-330">Błąd</span><span class="sxs-lookup"><span data-stu-id="a1395-330">Error</span></span>             |
| <span data-ttu-id="a1395-331">5</span><span class="sxs-lookup"><span data-stu-id="a1395-331">5</span></span>      | <span data-ttu-id="a1395-332">Konsola</span><span class="sxs-lookup"><span data-stu-id="a1395-332">Console</span></span>       | <span data-ttu-id="a1395-333">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="a1395-333">All categories</span></span>                          | <span data-ttu-id="a1395-334">Informacje</span><span class="sxs-lookup"><span data-stu-id="a1395-334">Information</span></span>       |
| <span data-ttu-id="a1395-335">6</span><span class="sxs-lookup"><span data-stu-id="a1395-335">6</span></span>      | <span data-ttu-id="a1395-336">Wszyscy dostawcy</span><span class="sxs-lookup"><span data-stu-id="a1395-336">All providers</span></span> | <span data-ttu-id="a1395-337">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="a1395-337">All categories</span></span>                          | <span data-ttu-id="a1395-338">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="a1395-338">Debug</span></span>             |
| <span data-ttu-id="a1395-339">7</span><span class="sxs-lookup"><span data-stu-id="a1395-339">7</span></span>      | <span data-ttu-id="a1395-340">Wszyscy dostawcy</span><span class="sxs-lookup"><span data-stu-id="a1395-340">All providers</span></span> | <span data-ttu-id="a1395-341">System</span><span class="sxs-lookup"><span data-stu-id="a1395-341">System</span></span>                                  | <span data-ttu-id="a1395-342">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="a1395-342">Debug</span></span>             |
| <span data-ttu-id="a1395-343">8</span><span class="sxs-lookup"><span data-stu-id="a1395-343">8</span></span>      | <span data-ttu-id="a1395-344">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="a1395-344">Debug</span></span>         | <span data-ttu-id="a1395-345">Microsoft</span><span class="sxs-lookup"><span data-stu-id="a1395-345">Microsoft</span></span>                               | <span data-ttu-id="a1395-346">Ślad</span><span class="sxs-lookup"><span data-stu-id="a1395-346">Trace</span></span>             |

<span data-ttu-id="a1395-347">Po utworzeniu `ILogger` obiektu `ILoggerFactory` obiekt wybiera jedną regułę dla każdego dostawcy, która ma zostać zastosowana do tego rejestratora.</span><span class="sxs-lookup"><span data-stu-id="a1395-347">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="a1395-348">Wszystkie komunikaty zapisywane przez `ILogger` wystąpienie są filtrowane na podstawie wybranych reguł.</span><span class="sxs-lookup"><span data-stu-id="a1395-348">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="a1395-349">Najbardziej konkretną regułą można wybrać dla każdego dostawcy i pary kategorii z dostępnych reguł.</span><span class="sxs-lookup"><span data-stu-id="a1395-349">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="a1395-350">Następujący algorytm jest używany dla każdego dostawcy, `ILogger` gdy jest tworzony dla danej kategorii:</span><span class="sxs-lookup"><span data-stu-id="a1395-350">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="a1395-351">Wybierz wszystkie reguły, które pasują do dostawcy lub jego aliasu.</span><span class="sxs-lookup"><span data-stu-id="a1395-351">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="a1395-352">Jeśli nie zostanie znalezione dopasowanie, zaznacz wszystkie reguły z pustym dostawcą.</span><span class="sxs-lookup"><span data-stu-id="a1395-352">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="a1395-353">W wyniku poprzedniego kroku wybierz pozycję reguły z najdłuższym prefiksem kategorii.</span><span class="sxs-lookup"><span data-stu-id="a1395-353">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="a1395-354">Jeśli nie zostanie znalezione dopasowanie, zaznacz wszystkie reguły, które nie określają kategorii.</span><span class="sxs-lookup"><span data-stu-id="a1395-354">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="a1395-355">Jeśli wybrano wiele reguł, zrób to **ostatnie** .</span><span class="sxs-lookup"><span data-stu-id="a1395-355">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="a1395-356">Jeśli nie wybrano żadnych reguł, `MinimumLevel`Użyj.</span><span class="sxs-lookup"><span data-stu-id="a1395-356">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="a1395-357">Na powyższej liście reguł Załóżmy, że tworzysz `ILogger` obiekt dla kategorii "Microsoft. AspNetCore. MVC. Razor. RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="a1395-357">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="a1395-358">Dla dostawcy debugowania obowiązują reguły 1, 6 i 8.</span><span class="sxs-lookup"><span data-stu-id="a1395-358">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="a1395-359">Reguła 8 jest najbardziej specyficzna, więc jest to jedna wybrana.</span><span class="sxs-lookup"><span data-stu-id="a1395-359">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="a1395-360">W przypadku dostawcy konsoli obowiązują reguły 3, 4, 5 i 6.</span><span class="sxs-lookup"><span data-stu-id="a1395-360">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="a1395-361">Reguła 3 jest najbardziej specyficzna.</span><span class="sxs-lookup"><span data-stu-id="a1395-361">Rule 3 is most specific.</span></span>

<span data-ttu-id="a1395-362">Wystąpienie wyników `ILogger` wysyła dzienniki `Trace` poziomu i powyżej do dostawcy debugowania.</span><span class="sxs-lookup"><span data-stu-id="a1395-362">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="a1395-363">Dzienniki `Debug` poziomów i powyżej są wysyłane do dostawcy konsoli.</span><span class="sxs-lookup"><span data-stu-id="a1395-363">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="a1395-364">Aliasy dostawców</span><span class="sxs-lookup"><span data-stu-id="a1395-364">Provider aliases</span></span>

<span data-ttu-id="a1395-365">Każdy dostawca definiuje *alias* , który może być używany w konfiguracji zamiast w pełni kwalifikowanej nazwy typu.</span><span class="sxs-lookup"><span data-stu-id="a1395-365">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="a1395-366">W przypadku dostawców wbudowanych Użyj następujących aliasów:</span><span class="sxs-lookup"><span data-stu-id="a1395-366">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="a1395-367">Konsola</span><span class="sxs-lookup"><span data-stu-id="a1395-367">Console</span></span>
* <span data-ttu-id="a1395-368">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="a1395-368">Debug</span></span>
* <span data-ttu-id="a1395-369">EventSource</span><span class="sxs-lookup"><span data-stu-id="a1395-369">EventSource</span></span>
* <span data-ttu-id="a1395-370">Elemencie</span><span class="sxs-lookup"><span data-stu-id="a1395-370">EventLog</span></span>
* <span data-ttu-id="a1395-371">TraceSource</span><span class="sxs-lookup"><span data-stu-id="a1395-371">TraceSource</span></span>
* <span data-ttu-id="a1395-372">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="a1395-372">AzureAppServicesFile</span></span>
* <span data-ttu-id="a1395-373">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="a1395-373">AzureAppServicesBlob</span></span>
* <span data-ttu-id="a1395-374">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="a1395-374">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="a1395-375">Domyślny poziom minimalny</span><span class="sxs-lookup"><span data-stu-id="a1395-375">Default minimum level</span></span>

<span data-ttu-id="a1395-376">Istnieje ustawienie minimalnego poziomu, które działa tylko wtedy, gdy nie mają zastosowania żadne reguły z konfiguracji lub kodu dla danego dostawcy i kategorii.</span><span class="sxs-lookup"><span data-stu-id="a1395-376">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="a1395-377">Poniższy przykład pokazuje, jak ustawić poziom minimalny:</span><span class="sxs-lookup"><span data-stu-id="a1395-377">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="a1395-378">Jeśli poziom minimalny nie został jawnie ustawiony, wartość domyślna to `Information`, co oznacza, że `Trace` dzienniki i `Debug` są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="a1395-378">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="a1395-379">Funkcje filtrowania</span><span class="sxs-lookup"><span data-stu-id="a1395-379">Filter functions</span></span>

<span data-ttu-id="a1395-380">Funkcja filtru jest wywoływana dla wszystkich dostawców i kategorii, które nie mają przypisanych do nich reguł przez konfigurację lub kod.</span><span class="sxs-lookup"><span data-stu-id="a1395-380">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="a1395-381">Kod w funkcji ma dostęp do typu dostawcy, kategorii i poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="a1395-381">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="a1395-382">Przykład:</span><span class="sxs-lookup"><span data-stu-id="a1395-382">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="a1395-383">Kategorie i poziomy systemu</span><span class="sxs-lookup"><span data-stu-id="a1395-383">System categories and levels</span></span>

<span data-ttu-id="a1395-384">Poniżej przedstawiono niektóre kategorie używane przez ASP.NET Core i Entity Framework Core, z informacjami o dziennikach, od których należy się spodziewać:</span><span class="sxs-lookup"><span data-stu-id="a1395-384">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="a1395-385">Kategoria</span><span class="sxs-lookup"><span data-stu-id="a1395-385">Category</span></span>                            | <span data-ttu-id="a1395-386">Uwagi</span><span class="sxs-lookup"><span data-stu-id="a1395-386">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="a1395-387">Microsoft. AspNetCore</span><span class="sxs-lookup"><span data-stu-id="a1395-387">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="a1395-388">Ogólna Diagnostyka ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1395-388">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="a1395-389">Microsoft. AspNetCore. dataprotection</span><span class="sxs-lookup"><span data-stu-id="a1395-389">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="a1395-390">Które klucze zostały wzięte pod uwagę, znaleziono i użyte.</span><span class="sxs-lookup"><span data-stu-id="a1395-390">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="a1395-391">Microsoft. AspNetCore. HostFiltering</span><span class="sxs-lookup"><span data-stu-id="a1395-391">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="a1395-392">Dozwolone hosty.</span><span class="sxs-lookup"><span data-stu-id="a1395-392">Hosts allowed.</span></span> |
| <span data-ttu-id="a1395-393">Microsoft. AspNetCore. hosting</span><span class="sxs-lookup"><span data-stu-id="a1395-393">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="a1395-394">Jak długo trwa wykonywanie żądań HTTP i czas ich uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="a1395-394">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="a1395-395">Które hostowanie zestawów uruchamiania zostało załadowane.</span><span class="sxs-lookup"><span data-stu-id="a1395-395">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="a1395-396">Microsoft. AspNetCore. MVC</span><span class="sxs-lookup"><span data-stu-id="a1395-396">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="a1395-397">Diagnostyka MVC i Razor.</span><span class="sxs-lookup"><span data-stu-id="a1395-397">MVC and Razor diagnostics.</span></span> <span data-ttu-id="a1395-398">Powiązanie modelu, wykonywanie filtru, kompilacja widoku, wybór akcji.</span><span class="sxs-lookup"><span data-stu-id="a1395-398">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="a1395-399">Microsoft. AspNetCore. Routing</span><span class="sxs-lookup"><span data-stu-id="a1395-399">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="a1395-400">Informacje o trasie.</span><span class="sxs-lookup"><span data-stu-id="a1395-400">Route matching information.</span></span> |
| <span data-ttu-id="a1395-401">Microsoft. AspNetCore. Server</span><span class="sxs-lookup"><span data-stu-id="a1395-401">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="a1395-402">Reagowanie na uruchamianie, zatrzymywanie i utrzymywanie aktywności.</span><span class="sxs-lookup"><span data-stu-id="a1395-402">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="a1395-403">Informacje o certyfikacie HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a1395-403">HTTPS certificate information.</span></span> |
| <span data-ttu-id="a1395-404">Microsoft. AspNetCore. StaticFiles</span><span class="sxs-lookup"><span data-stu-id="a1395-404">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="a1395-405">Obsługiwane pliki.</span><span class="sxs-lookup"><span data-stu-id="a1395-405">Files served.</span></span> |
| <span data-ttu-id="a1395-406">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="a1395-406">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="a1395-407">Ogólna Diagnostyka Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="a1395-407">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="a1395-408">Aktywność i Konfiguracja bazy danych, wykrywanie zmian, migracje.</span><span class="sxs-lookup"><span data-stu-id="a1395-408">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="a1395-409">Zakresy dziennika</span><span class="sxs-lookup"><span data-stu-id="a1395-409">Log scopes</span></span>

 <span data-ttu-id="a1395-410">*Zakres* może grupować zestaw operacji logicznych.</span><span class="sxs-lookup"><span data-stu-id="a1395-410">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="a1395-411">Takie grupowanie może służyć do dołączania tych samych danych do każdego dziennika, który został utworzony jako część zestawu.</span><span class="sxs-lookup"><span data-stu-id="a1395-411">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="a1395-412">Na przykład każdy dziennik utworzony w ramach przetwarzania transakcji może zawierać identyfikator transakcji.</span><span class="sxs-lookup"><span data-stu-id="a1395-412">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="a1395-413">Zakres jest `IDisposable` typem zwracanym przez <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodę i obowiązuje do momentu jego usunięcia.</span><span class="sxs-lookup"><span data-stu-id="a1395-413">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="a1395-414">Użyj zakresu przez Zawijanie wywołań rejestratora w `using` bloku:</span><span class="sxs-lookup"><span data-stu-id="a1395-414">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="a1395-415">Poniższy kod włącza zakresy dla dostawcy konsoli:</span><span class="sxs-lookup"><span data-stu-id="a1395-415">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="a1395-416">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a1395-416">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="a1395-417">Konfigurowanie opcji `IncludeScopes` rejestratora konsoli jest wymagane do włączenia rejestrowania na podstawie zakresu.</span><span class="sxs-lookup"><span data-stu-id="a1395-417">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="a1395-418">Informacje o konfiguracji znajdują się w sekcji [Konfiguracja](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="a1395-418">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="a1395-419">Każdy komunikat dziennika zawiera informacje o zakresie:</span><span class="sxs-lookup"><span data-stu-id="a1395-419">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="a1395-420">Wbudowani dostawcy rejestrowania</span><span class="sxs-lookup"><span data-stu-id="a1395-420">Built-in logging providers</span></span>

<span data-ttu-id="a1395-421">ASP.NET Core dostarcza następujących dostawców:</span><span class="sxs-lookup"><span data-stu-id="a1395-421">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="a1395-422">Konsola</span><span class="sxs-lookup"><span data-stu-id="a1395-422">Console</span></span>](#console-provider)
* [<span data-ttu-id="a1395-423">Rozpocząć</span><span class="sxs-lookup"><span data-stu-id="a1395-423">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="a1395-424">EventSource</span><span class="sxs-lookup"><span data-stu-id="a1395-424">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="a1395-425">Elemencie</span><span class="sxs-lookup"><span data-stu-id="a1395-425">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="a1395-426">TraceSource</span><span class="sxs-lookup"><span data-stu-id="a1395-426">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="a1395-427">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="a1395-427">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="a1395-428">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="a1395-428">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="a1395-429">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="a1395-429">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="a1395-430">Aby uzyskać informacje na temat strumienia stdout i rejestrowania debugowania za pomocą modułu <xref:test/troubleshoot-azure-iis> ASP.NET Core <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>, zobacz i.</span><span class="sxs-lookup"><span data-stu-id="a1395-430">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="a1395-431">Dostawca konsoli</span><span class="sxs-lookup"><span data-stu-id="a1395-431">Console provider</span></span>

<span data-ttu-id="a1395-432">Pakiet [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) Provider wysyła dane wyjściowe dziennika do konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="a1395-432">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="a1395-433">Aby wyświetlić dane wyjściowe rejestrowania konsoli, Otwórz wiersz polecenia w folderze projektu i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="a1395-433">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="a1395-434">Dostawca debugowania</span><span class="sxs-lookup"><span data-stu-id="a1395-434">Debug provider</span></span>

<span data-ttu-id="a1395-435">Pakiet [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) Provider zapisuje dane wyjściowe dziennika przy użyciu klasy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine` wywołania metod).</span><span class="sxs-lookup"><span data-stu-id="a1395-435">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="a1395-436">W systemie Linux ten dostawca zapisuje dzienniki do */var/log/Message*.</span><span class="sxs-lookup"><span data-stu-id="a1395-436">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="a1395-437">Dostawca źródła zdarzeń</span><span class="sxs-lookup"><span data-stu-id="a1395-437">Event Source provider</span></span>

<span data-ttu-id="a1395-438">Pakiet dostawcy [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na międzyplatformę źródła zdarzeń, używając nazwy `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="a1395-438">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="a1395-439">W systemie Windows Dostawca używa [funkcji ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="a1395-439">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="a1395-440">Dostawca źródła zdarzeń zostanie dodany automatycznie, gdy `CreateDefaultBuilder` jest wywoływana w celu skompilowania hosta.</span><span class="sxs-lookup"><span data-stu-id="a1395-440">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="a1395-441">narzędzia śledzenia dotnet</span><span class="sxs-lookup"><span data-stu-id="a1395-441">dotnet trace tooling</span></span>

<span data-ttu-id="a1395-442">Narzędzie do [śledzenia dotnet](/dotnet/core/diagnostics/dotnet-trace) to międzyplatformowe narzędzie globalne interfejsu wiersza polecenia, które umożliwia zbieranie śladów programu .NET Core uruchomionego procesu.</span><span class="sxs-lookup"><span data-stu-id="a1395-442">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="a1395-443">Narzędzie zbiera <xref:Microsoft.Extensions.Logging.EventSource> dane dostawcy za pomocą <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span><span class="sxs-lookup"><span data-stu-id="a1395-443">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="a1395-444">Zainstaluj narzędzia śledzenia dotnet przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="a1395-444">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="a1395-445">Użyj narzędzi śledzenia dotnet, aby zebrać ślad z aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a1395-445">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="a1395-446">Jeśli aplikacja nie kompiluje hosta za pomocą `CreateDefaultBuilder`programu, Dodaj [dostawcę źródła zdarzeń](#event-source-provider) do konfiguracji rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-446">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="a1395-447">Uruchom aplikację za pomocą `dotnet run` polecenia.</span><span class="sxs-lookup"><span data-stu-id="a1395-447">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="a1395-448">Określ identyfikator procesu (PID) aplikacji .NET Core:</span><span class="sxs-lookup"><span data-stu-id="a1395-448">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="a1395-449">W systemie Windows należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="a1395-449">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="a1395-450">Menedżer zadań (Ctrl + Alt + Del)</span><span class="sxs-lookup"><span data-stu-id="a1395-450">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="a1395-451">tasklist — polecenie</span><span class="sxs-lookup"><span data-stu-id="a1395-451">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="a1395-452">Get-Process — polecenie programu PowerShell</span><span class="sxs-lookup"><span data-stu-id="a1395-452">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="a1395-453">W systemie Linux Użyj [polecenia pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="a1395-453">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="a1395-454">Znajdź identyfikator PID procesu, który ma taką samą nazwę jak zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-454">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="a1395-455">Wykonaj `dotnet trace` polecenie.</span><span class="sxs-lookup"><span data-stu-id="a1395-455">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="a1395-456">Ogólna składnia poleceń:</span><span class="sxs-lookup"><span data-stu-id="a1395-456">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="a1395-457">W przypadku korzystania z powłoki poleceń programu PowerShell należy ująć `--providers` wartość w apostrofy (`'`):</span><span class="sxs-lookup"><span data-stu-id="a1395-457">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="a1395-458">Na platformach innych niż Windows Dodaj `-f speedscope` opcję, aby zmienić format wyjściowego pliku śledzenia na. `speedscope`</span><span class="sxs-lookup"><span data-stu-id="a1395-458">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="a1395-459">Słowo kluczowe</span><span class="sxs-lookup"><span data-stu-id="a1395-459">Keyword</span></span> | <span data-ttu-id="a1395-460">Opis</span><span class="sxs-lookup"><span data-stu-id="a1395-460">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="a1395-461">1</span><span class="sxs-lookup"><span data-stu-id="a1395-461">1</span></span>       | <span data-ttu-id="a1395-462">Rejestruj meta zdarzenia dotyczące `LoggingEventSource`.</span><span class="sxs-lookup"><span data-stu-id="a1395-462">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="a1395-463">Nie rejestruje zdarzeń z `ILogger`).</span><span class="sxs-lookup"><span data-stu-id="a1395-463">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="a1395-464">2</span><span class="sxs-lookup"><span data-stu-id="a1395-464">2</span></span>       | <span data-ttu-id="a1395-465">Włącza zdarzenie, `Message` gdy `ILogger.Log()` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="a1395-465">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="a1395-466">Zawiera informacje w sposób programistyczny (nie sformatowany).</span><span class="sxs-lookup"><span data-stu-id="a1395-466">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="a1395-467">4</span><span class="sxs-lookup"><span data-stu-id="a1395-467">4</span></span>       | <span data-ttu-id="a1395-468">Włącza zdarzenie, `FormatMessage` gdy `ILogger.Log()` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="a1395-468">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="a1395-469">Udostępnia sformatowaną wersję ciągu informacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-469">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="a1395-470">8</span><span class="sxs-lookup"><span data-stu-id="a1395-470">8</span></span>       | <span data-ttu-id="a1395-471">Włącza zdarzenie, `MessageJson` gdy `ILogger.Log()` jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="a1395-471">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="a1395-472">Zawiera reprezentację argumentów w formacie JSON.</span><span class="sxs-lookup"><span data-stu-id="a1395-472">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="a1395-473">Poziom zdarzenia</span><span class="sxs-lookup"><span data-stu-id="a1395-473">Event Level</span></span> | <span data-ttu-id="a1395-474">Opis</span><span class="sxs-lookup"><span data-stu-id="a1395-474">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="a1395-475">0</span><span class="sxs-lookup"><span data-stu-id="a1395-475">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="a1395-476">1</span><span class="sxs-lookup"><span data-stu-id="a1395-476">1</span></span>           | `Critical`      |
   | <span data-ttu-id="a1395-477">2</span><span class="sxs-lookup"><span data-stu-id="a1395-477">2</span></span>           | `Error`         |
   | <span data-ttu-id="a1395-478">3</span><span class="sxs-lookup"><span data-stu-id="a1395-478">3</span></span>           | `Warning`       |
   | <span data-ttu-id="a1395-479">4</span><span class="sxs-lookup"><span data-stu-id="a1395-479">4</span></span>           | `Informational` |
   | <span data-ttu-id="a1395-480">5</span><span class="sxs-lookup"><span data-stu-id="a1395-480">5</span></span>           | `Verbose`       |

   <span data-ttu-id="a1395-481">`FilterSpecs`wpisy dla `{Logger Category}` i `{Event Level}` przedstawiają dodatkowe warunki filtrowania dzienników.</span><span class="sxs-lookup"><span data-stu-id="a1395-481">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="a1395-482">Oddzielaj `FilterSpecs` wpisy średnikami (`;`).</span><span class="sxs-lookup"><span data-stu-id="a1395-482">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="a1395-483">Przykład użycia powłoki poleceń systemu Windows (**Brak** pojedynczego cudzysłowu wokół `--providers` wartości):</span><span class="sxs-lookup"><span data-stu-id="a1395-483">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="a1395-484">Poprzednie polecenie aktywuje:</span><span class="sxs-lookup"><span data-stu-id="a1395-484">The preceding command activates:</span></span>

   * <span data-ttu-id="a1395-485">Rejestrator źródła zdarzeń do tworzenia sformatowanych ciągów (`4`) dla błędów (`2`).</span><span class="sxs-lookup"><span data-stu-id="a1395-485">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="a1395-486">`Microsoft.AspNetCore.Hosting`Rejestrowanie na poziomie `Informational` rejestrowania (`4`).</span><span class="sxs-lookup"><span data-stu-id="a1395-486">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="a1395-487">Zatrzymaj narzędzia śledzenia dotnet, naciskając klawisz ENTER lub CTRL + C.</span><span class="sxs-lookup"><span data-stu-id="a1395-487">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="a1395-488">Ślad jest zapisywany z nazwą *Trace. webtrace* w folderze, w którym jest `dotnet trace` wykonywane polecenie.</span><span class="sxs-lookup"><span data-stu-id="a1395-488">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="a1395-489">Otwórz ślad z [Narzędzia PerfView](#perfview).</span><span class="sxs-lookup"><span data-stu-id="a1395-489">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="a1395-490">Otwórz plik *Trace. webtrace* i zbadaj zdarzenia śledzenia.</span><span class="sxs-lookup"><span data-stu-id="a1395-490">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="a1395-491">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="a1395-491">For more information, see:</span></span>

* <span data-ttu-id="a1395-492">[Śledzenie dla narzędzia do analizy wydajności (program dotnet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (dokumentacja platformy .NET Core)</span><span class="sxs-lookup"><span data-stu-id="a1395-492">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="a1395-493">[Śledzenie dla narzędzia analizy wydajności (dotnet-Trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dokumentacja repozytorium dotnet/Diagnostics)</span><span class="sxs-lookup"><span data-stu-id="a1395-493">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="a1395-494">[LoggingEventSource — Klasa](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (przeglądarka interfejsu API platformy .NET)</span><span class="sxs-lookup"><span data-stu-id="a1395-494">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="a1395-495">[Źródło odwołania LoggingEventSource (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Aby uzyskać Źródło odwołania dla innej wersji, Zmień gałąź na `release/{Version}`, gdzie `{Version}` jest pożądaną wersją ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1395-495">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="a1395-496">[Narzędzia PerfView](#perfview) &ndash; przydatny do wyświetlania śladów źródła zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="a1395-496">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="a1395-497">Narzędzia PerfView</span><span class="sxs-lookup"><span data-stu-id="a1395-497">Perfview</span></span>

<span data-ttu-id="a1395-498">Użyj [Narzędzia Narzędzia PerfView](https://github.com/Microsoft/perfview) do zbierania i wyświetlania dzienników.</span><span class="sxs-lookup"><span data-stu-id="a1395-498">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="a1395-499">Istnieją inne narzędzia do wyświetlania dzienników ETW, ale narzędzia PerfView zapewnia najlepsze środowisko pracy z zdarzeniami ETW emitowanymi przez ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1395-499">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="a1395-500">Aby skonfigurować narzędzia PerfView do zbierania zdarzeń rejestrowanych przez tego dostawcę, Dodaj ciąg `*Microsoft-Extensions-Logging` do listy **dodatkowych dostawców** .</span><span class="sxs-lookup"><span data-stu-id="a1395-500">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="a1395-501">(Nie przegap gwiazdki na początku ciągu znaków).</span><span class="sxs-lookup"><span data-stu-id="a1395-501">(Don't miss the asterisk at the start of the string.)</span></span>

![Narzędzia PerfView dodatkowych dostawców](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="a1395-503">Dostawca dziennika zdarzeń systemu Windows</span><span class="sxs-lookup"><span data-stu-id="a1395-503">Windows EventLog provider</span></span>

<span data-ttu-id="a1395-504">Pakiet dostawcy [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) wysyła dane wyjściowe dziennika do dziennika zdarzeń systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="a1395-504">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="a1395-505">[Przeciążenia addeventlog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umożliwiają przekazywanie <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="a1395-505">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="a1395-506">Jeśli `null` lub nie zostanie określony, są używane następujące ustawienia domyślne:</span><span class="sxs-lookup"><span data-stu-id="a1395-506">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="a1395-507">`LogName`&ndash; "Aplikacja"</span><span class="sxs-lookup"><span data-stu-id="a1395-507">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="a1395-508">`SourceName`&ndash; "Środowisko uruchomieniowe platformy .NET"</span><span class="sxs-lookup"><span data-stu-id="a1395-508">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="a1395-509">`MachineName`&ndash; komputer lokalny</span><span class="sxs-lookup"><span data-stu-id="a1395-509">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="a1395-510">Zdarzenia są rejestrowane dla [poziomu ostrzeżeń i wyższych](#log-level).</span><span class="sxs-lookup"><span data-stu-id="a1395-510">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="a1395-511">Aby rejestrować zdarzenia mniejsze niż `Warning`, jawnie ustaw poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="a1395-511">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="a1395-512">Na przykład Dodaj następujący kod do pliku *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="a1395-512">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="a1395-513">Dostawca TraceSource</span><span class="sxs-lookup"><span data-stu-id="a1395-513">TraceSource provider</span></span>

<span data-ttu-id="a1395-514">Pakiet dostawcy [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) używa <xref:System.Diagnostics.TraceSource> bibliotek i dostawców.</span><span class="sxs-lookup"><span data-stu-id="a1395-514">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="a1395-515">[Przeciążenia AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umożliwiają przekazywanie danych w przełączniku źródłowym i odbiorniku śledzenia.</span><span class="sxs-lookup"><span data-stu-id="a1395-515">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="a1395-516">Aby można było korzystać z tego dostawcy, aplikacja musi działać na .NET Framework (a nie na platformie .NET Core).</span><span class="sxs-lookup"><span data-stu-id="a1395-516">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="a1395-517">Dostawca może kierować komunikaty do różnych [odbiorników](/dotnet/framework/debug-trace-profile/trace-listeners), takich jak <xref:System.Diagnostics.TextWriterTraceListener> używane w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-517">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="a1395-518">Dostawca Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a1395-518">Azure App Service provider</span></span>

<span data-ttu-id="a1395-519">Pakiet [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Provider zapisuje dzienniki w plikach tekstowych w systemie plików aplikacji Azure App Service i w usłudze [BLOB Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) na koncie usługi Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="a1395-519">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="a1395-520">Pakiet dostawcy nie jest uwzględniony w udostępnionej infrastrukturze.</span><span class="sxs-lookup"><span data-stu-id="a1395-520">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="a1395-521">Aby użyć dostawcy, Dodaj pakiet dostawcy do projektu.</span><span class="sxs-lookup"><span data-stu-id="a1395-521">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="a1395-522">Aby skonfigurować ustawienia dostawcy, użyj <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> i <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, jak pokazano w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a1395-522">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="a1395-523">Po wdrożeniu w aplikacji App Service, aplikacja będzie przestrzegać ustawień w sekcji [dzienniki App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stronie **App Service** Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="a1395-523">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="a1395-524">Po zaktualizowaniu następujących ustawień zmiany zaczynają obowiązywać natychmiast, bez konieczności ponownego uruchomienia lub ponownej wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-524">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="a1395-525">**Rejestrowanie aplikacji (system plików)**</span><span class="sxs-lookup"><span data-stu-id="a1395-525">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="a1395-526">**Rejestrowanie aplikacji (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="a1395-526">**Application Logging (Blob)**</span></span>

<span data-ttu-id="a1395-527">Domyślną lokalizacją plików dziennika jest folder *D:\\Home\\LogFiles\\* , a domyślna nazwa pliku to *Diagnostics-YYYYMMDD. txt*.</span><span class="sxs-lookup"><span data-stu-id="a1395-527">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="a1395-528">Domyślny limit rozmiaru pliku wynosi 10 MB, a domyślna maksymalna liczba zachowywanych plików to 2.</span><span class="sxs-lookup"><span data-stu-id="a1395-528">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="a1395-529">Domyślną nazwą obiektu BLOB jest *{App-Name} {timestamp}/yyyy/mm/dd/hh/{GUID}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="a1395-529">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="a1395-530">Dostawca działa tylko wtedy, gdy projekt jest uruchomiony w środowisku platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="a1395-530">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="a1395-531">Nie ma ono wpływu, gdy projekt jest uruchamiany lokalnie&mdash;, nie zapisuje w plikach lokalnych ani w lokalnym magazynie programistycznym dla obiektów BLOB.</span><span class="sxs-lookup"><span data-stu-id="a1395-531">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="a1395-532">Przesyłanie strumieniowe dzienników Azure</span><span class="sxs-lookup"><span data-stu-id="a1395-532">Azure log streaming</span></span>

<span data-ttu-id="a1395-533">Usługa przesyłania strumieniowego w usłudze Azure log umożliwia wyświetlanie dziennika aktywności w czasie rzeczywistym z:</span><span class="sxs-lookup"><span data-stu-id="a1395-533">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="a1395-534">Serwer aplikacji</span><span class="sxs-lookup"><span data-stu-id="a1395-534">The app server</span></span>
* <span data-ttu-id="a1395-535">Serwer sieci Web</span><span class="sxs-lookup"><span data-stu-id="a1395-535">The web server</span></span>
* <span data-ttu-id="a1395-536">Śledzenie nieudanych żądań</span><span class="sxs-lookup"><span data-stu-id="a1395-536">Failed request tracing</span></span>

<span data-ttu-id="a1395-537">Aby skonfigurować przesyłanie strumieniowe dzienników Azure:</span><span class="sxs-lookup"><span data-stu-id="a1395-537">To configure Azure log streaming:</span></span>

* <span data-ttu-id="a1395-538">Przejdź do strony **dzienników App Service** ze strony portalu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-538">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="a1395-539">Ustaw **Rejestrowanie aplikacji (system plików)** na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="a1395-539">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="a1395-540">Wybierz **poziom**dziennika.</span><span class="sxs-lookup"><span data-stu-id="a1395-540">Choose the log **Level**.</span></span> <span data-ttu-id="a1395-541">To ustawienie dotyczy tylko przesyłania strumieniowego dzienników platformy Azure, a nie innych dostawców rejestrowania w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-541">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="a1395-542">Przejdź do strony **strumień dziennika** , aby wyświetlić komunikaty aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-542">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="a1395-543">Są one rejestrowane przez aplikację za pomocą `ILogger` interfejsu.</span><span class="sxs-lookup"><span data-stu-id="a1395-543">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="a1395-544">Rejestrowanie śledzenia Application Insights platformy Azure</span><span class="sxs-lookup"><span data-stu-id="a1395-544">Azure Application Insights trace logging</span></span>

<span data-ttu-id="a1395-545">Pakiet [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) Provider zapisuje dzienniki na platformie Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="a1395-545">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="a1395-546">Application Insights to usługa, która monitoruje aplikację internetową i udostępnia narzędzia do wykonywania zapytań i analizowania danych telemetrycznych.</span><span class="sxs-lookup"><span data-stu-id="a1395-546">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="a1395-547">Jeśli używasz tego dostawcy, możesz wysyłać zapytania i analizować dzienniki przy użyciu narzędzi Application Insights.</span><span class="sxs-lookup"><span data-stu-id="a1395-547">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="a1395-548">Dostawca rejestrowania jest dołączony jako zależność [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), który jest pakietem, który zapewnia wszystkie dostępne dane telemetryczne dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1395-548">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="a1395-549">Jeśli używasz tego pakietu, nie musisz instalować pakietu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="a1395-549">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="a1395-550">Nie używaj&mdash;pakietu [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) , który jest przeznaczony dla ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="a1395-550">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="a1395-551">Więcej informacji zawierają następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="a1395-551">For more information, see the following resources:</span></span>

* [<span data-ttu-id="a1395-552">Omówienie usługi Application Insights</span><span class="sxs-lookup"><span data-stu-id="a1395-552">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="a1395-553">[Application Insights dla ASP.NET Core aplikacji](/azure/azure-monitor/app/asp-net-core) — Zacznij tutaj, jeśli chcesz zaimplementować cały zakres Application Insights telemetrii wraz z rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="a1395-553">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="a1395-554">[ApplicationInsightsLoggerProvider for .NET Core ILogger](/azure/azure-monitor/app/ilogger) — Rozpocznij tutaj, jeśli chcesz zaimplementować dostawcę rejestrowania bez pozostałej części telemetrii Application Insights.</span><span class="sxs-lookup"><span data-stu-id="a1395-554">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="a1395-555">[Karty rejestrowania Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="a1395-555">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="a1395-556">[Zainstaluj, skonfiguruj i zainicjuj samouczek Application INSIGHTS SDK](/learn/modules/instrument-web-app-code-with-application-insights) — interaktywny w witrynie Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="a1395-556">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="a1395-557">Dostawcy rejestrowania innych firm</span><span class="sxs-lookup"><span data-stu-id="a1395-557">Third-party logging providers</span></span>

<span data-ttu-id="a1395-558">Platformy rejestrowania innych firm, które współpracują z ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a1395-558">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="a1395-559">[ELMAH.IO](https://elmah.io/) ([repozytorium GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="a1395-559">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="a1395-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repozytorium GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="a1395-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="a1395-561">[JSNLog](https://jsnlog.com/) ([repozytorium GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="a1395-561">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="a1395-562">[KissLog.NET](https://kisslog.net/) ([repozytorium GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="a1395-562">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="a1395-563">[Log4Net](https://logging.apache.org/log4net/) ([repozytorium GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="a1395-563">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="a1395-564">[Loggr](https://loggr.net/) ([repozytorium GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="a1395-564">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="a1395-565">[NLOG](https://nlog-project.org/) ([repozytorium GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="a1395-565">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="a1395-566">[Sentry](https://sentry.io/welcome/) ([repozytorium GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="a1395-566">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="a1395-567">[Serilog](https://serilog.net/) ([repozytorium GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="a1395-567">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="a1395-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repozytorium GitHub](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="a1395-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="a1395-569">Niektóre struktury innych firm mogą wykonywać [Rejestrowanie semantyczne, znane także jako rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="a1395-569">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="a1395-570">Korzystanie z struktury innej firmy jest podobne do korzystania z jednego z wbudowanych dostawców:</span><span class="sxs-lookup"><span data-stu-id="a1395-570">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="a1395-571">Dodaj pakiet NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="a1395-571">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="a1395-572">Wywoływanie `ILoggerFactory` metody rozszerzenia dostarczonej przez strukturę rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a1395-572">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="a1395-573">Aby uzyskać więcej informacji, zobacz dokumentację każdego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="a1395-573">For more information, see each provider's documentation.</span></span> <span data-ttu-id="a1395-574">Dostawcy rejestrowania innych firm nie są obsługiwani przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a1395-574">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a1395-575">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a1395-575">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a1395-576">Autorzy [Dykstra](https://github.com/tdykstra) i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a1395-576">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a1395-577">Platforma .NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi dostawcami rejestrowania wbudowanych i innych firm.</span><span class="sxs-lookup"><span data-stu-id="a1395-577">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="a1395-578">W tym artykule pokazano, jak używać interfejsu API rejestrowania z wbudowanymi dostawcami.</span><span class="sxs-lookup"><span data-stu-id="a1395-578">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="a1395-579">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a1395-579">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="a1395-580">Dodaj dostawców</span><span class="sxs-lookup"><span data-stu-id="a1395-580">Add providers</span></span>

<span data-ttu-id="a1395-581">Dostawca rejestrowania wyświetla lub przechowuje dzienniki.</span><span class="sxs-lookup"><span data-stu-id="a1395-581">A logging provider displays or stores logs.</span></span> <span data-ttu-id="a1395-582">Na przykład dostawca konsoli wyświetla dzienniki w konsoli programu, a Dostawca usługi Azure Application Insights przechowuje je na platformie Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="a1395-582">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="a1395-583">Dzienniki mogą być wysyłane do wielu miejsc docelowych przez dodanie wielu dostawców.</span><span class="sxs-lookup"><span data-stu-id="a1395-583">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="a1395-584">Aby dodać dostawcę, wywołaj metodę `Add{provider name}` rozszerzenia dostawcy w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a1395-584">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="a1395-585">Poprzedzający kod wymaga odwołania do `Microsoft.Extensions.Logging` i `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="a1395-585">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="a1395-586">Domyślne wywołania <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>szablonu projektu, które dodaje następujących dostawców rejestrowania:</span><span class="sxs-lookup"><span data-stu-id="a1395-586">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="a1395-587">Konsola</span><span class="sxs-lookup"><span data-stu-id="a1395-587">Console</span></span>
* <span data-ttu-id="a1395-588">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="a1395-588">Debug</span></span>
* <span data-ttu-id="a1395-589">EventSource (rozpoczęcie w ASP.NET Core 2,2)</span><span class="sxs-lookup"><span data-stu-id="a1395-589">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="a1395-590">Jeśli używasz `CreateDefaultBuilder`programu, możesz zastąpić domyślnych dostawców własnymi opcjami.</span><span class="sxs-lookup"><span data-stu-id="a1395-590">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="a1395-591">Wywołaj <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>i Dodaj żądanych dostawców.</span><span class="sxs-lookup"><span data-stu-id="a1395-591">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="a1395-592">Więcej informacji na temat [wbudowanych dostawców rejestrowania](#built-in-logging-providers) i [dostawców rejestrowania innych](#third-party-logging-providers) firm znajduje się w dalszej części artykułu.</span><span class="sxs-lookup"><span data-stu-id="a1395-592">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="a1395-593">Tworzenie dzienników</span><span class="sxs-lookup"><span data-stu-id="a1395-593">Create logs</span></span>

<span data-ttu-id="a1395-594">Aby utworzyć dzienniki, użyj <xref:Microsoft.Extensions.Logging.ILogger%601> obiektu.</span><span class="sxs-lookup"><span data-stu-id="a1395-594">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="a1395-595">W aplikacji sieci Web lub hostowanej usłudze Pobierz `ILogger` od iniekcji zależności (di).</span><span class="sxs-lookup"><span data-stu-id="a1395-595">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="a1395-596">W aplikacjach konsolowych innych niż host Użyj programu `LoggerFactory` , aby utworzyć `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="a1395-596">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="a1395-597">Poniższy ASP.NET Core przykład tworzy Rejestrator przy użyciu `TodoApiSample.Pages.AboutModel` jako kategorii.</span><span class="sxs-lookup"><span data-stu-id="a1395-597">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="a1395-598">*Kategoria* dziennika jest ciągiem, który jest skojarzony z każdym dziennikiem.</span><span class="sxs-lookup"><span data-stu-id="a1395-598">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="a1395-599">`ILogger<T>` Wystąpienie zapewniane przez program di tworzy dzienniki, które mają w pełni kwalifikowaną nazwę `T` typu jako kategorię.</span><span class="sxs-lookup"><span data-stu-id="a1395-599">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="a1395-600">W poniższych przykładach ASP.NET Core i aplikacji konsoli Rejestrator służy do tworzenia dzienników z użyciem `Information` jako poziom.</span><span class="sxs-lookup"><span data-stu-id="a1395-600">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="a1395-601">*Poziom* dziennika wskazuje ważność rejestrowanego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="a1395-601">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="a1395-602">[Poziomy](#log-level) i [Kategorie](#log-category) zostały wyjaśnione bardziej szczegółowo w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="a1395-602">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="a1395-603">Tworzenie dzienników w programie startowym</span><span class="sxs-lookup"><span data-stu-id="a1395-603">Create logs in Startup</span></span>

<span data-ttu-id="a1395-604">Aby napisać dzienniki w `Startup` klasie, Dołącz `ILogger` parametr do sygnatury konstruktora:</span><span class="sxs-lookup"><span data-stu-id="a1395-604">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="a1395-605">Tworzenie dzienników w klasie programu</span><span class="sxs-lookup"><span data-stu-id="a1395-605">Create logs in the Program class</span></span>

<span data-ttu-id="a1395-606">Aby napisać dzienniki w `Program` klasie, Pobierz wystąpienie z `ILogger` elementu di:</span><span class="sxs-lookup"><span data-stu-id="a1395-606">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="a1395-607">Rejestrowanie podczas konstruowania hosta nie jest bezpośrednio obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="a1395-607">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="a1395-608">Można jednak użyć osobnego rejestratora.</span><span class="sxs-lookup"><span data-stu-id="a1395-608">However, a separate logger can be used.</span></span> <span data-ttu-id="a1395-609">W poniższym przykładzie jest używany Rejestrator [Serilog](https://serilog.net/) do logowania `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a1395-609">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="a1395-610">`AddSerilog`używa konfiguracji statycznej określonej w `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="a1395-610">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="a1395-611">Brak metod rejestratora asynchronicznego</span><span class="sxs-lookup"><span data-stu-id="a1395-611">No asynchronous logger methods</span></span>

<span data-ttu-id="a1395-612">Rejestracja powinna być tak szybka, że nie jest to koszt wydajności kodu asynchronicznego.</span><span class="sxs-lookup"><span data-stu-id="a1395-612">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="a1395-613">Jeśli magazyn danych rejestrowania jest wolny, nie zapisuj go bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="a1395-613">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="a1395-614">Najpierw Rozważ zapisanie komunikatów dziennika do szybkiego sklepu, a następnie przeniesienie ich do wolnego magazynu później.</span><span class="sxs-lookup"><span data-stu-id="a1395-614">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="a1395-615">Na przykład jeśli rejestrujesz się do SQL Server, nie chcesz tego robić bezpośrednio w `Log` metodzie, ponieważ `Log` metody są synchroniczne.</span><span class="sxs-lookup"><span data-stu-id="a1395-615">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="a1395-616">Zamiast tego można synchronicznie dodawać komunikaty dziennika do kolejki w pamięci, a proces roboczy w tle ściągał komunikaty z kolejki, aby wykonać asynchroniczne działanie wypychania danych do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a1395-616">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="a1395-617">Aby uzyskać więcej informacji, zobacz [ten](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problem w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="a1395-617">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="a1395-618">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="a1395-618">Configuration</span></span>

<span data-ttu-id="a1395-619">Konfiguracja dostawcy rejestrowania jest świadczona przez co najmniej jednego dostawcę konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="a1395-619">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="a1395-620">Formaty plików (INI, JSON i XML).</span><span class="sxs-lookup"><span data-stu-id="a1395-620">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="a1395-621">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="a1395-621">Command-line arguments.</span></span>
* <span data-ttu-id="a1395-622">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="a1395-622">Environment variables.</span></span>
* <span data-ttu-id="a1395-623">Obiekty platformy .NET w pamięci.</span><span class="sxs-lookup"><span data-stu-id="a1395-623">In-memory .NET objects.</span></span>
* <span data-ttu-id="a1395-624">Magazyn niezaszyfrowanego [klucza tajnego](xref:security/app-secrets) .</span><span class="sxs-lookup"><span data-stu-id="a1395-624">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="a1395-625">Zaszyfrowany magazyn użytkowników, taki jak [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="a1395-625">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="a1395-626">Dostawcy niestandardowi (instalowani lub utworzony).</span><span class="sxs-lookup"><span data-stu-id="a1395-626">Custom providers (installed or created).</span></span>

<span data-ttu-id="a1395-627">Na przykład konfiguracja rejestrowania jest zwykle dostarczana przez `Logging` sekcję plików ustawień aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-627">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="a1395-628">Poniższy przykład pokazuje zawartość typowej wartości *appSettings. Plik Development. JSON* :</span><span class="sxs-lookup"><span data-stu-id="a1395-628">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="a1395-629">`Logging` Właściwość może mieć `LogLevel` właściwości dostawcy dzienników i.</span><span class="sxs-lookup"><span data-stu-id="a1395-629">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="a1395-630">`LogLevel` Właściwość w obszarze `Logging` określa minimalny [poziom](#log-level) rejestrowania wybranych kategorii.</span><span class="sxs-lookup"><span data-stu-id="a1395-630">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="a1395-631">`System` W przykładzie, `Microsoft` i kategorie są rejestrowane na `Information` poziomie, a wszystkie inne logowania na `Debug` poziomie.</span><span class="sxs-lookup"><span data-stu-id="a1395-631">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="a1395-632">Inne właściwości w `Logging` obszarze Określ dostawców rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a1395-632">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="a1395-633">Przykład dotyczy dostawcy konsoli.</span><span class="sxs-lookup"><span data-stu-id="a1395-633">The example is for the Console provider.</span></span> <span data-ttu-id="a1395-634">Jeśli dostawca obsługuje [zakresy rejestrowania](#log-scopes), wskazuje `IncludeScopes` , czy są one włączone.</span><span class="sxs-lookup"><span data-stu-id="a1395-634">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="a1395-635">Właściwość dostawcy (taka jak `Console` w przykładzie) może także określić `LogLevel` właściwość.</span><span class="sxs-lookup"><span data-stu-id="a1395-635">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="a1395-636">`LogLevel`w obszarze dostawca Określa poziomy do rejestrowania dla tego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="a1395-636">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="a1395-637">Jeśli w programie `Logging.{providername}.LogLevel`są określone poziomy, zastępują one wszystko `Logging.LogLevel`ustawione w.</span><span class="sxs-lookup"><span data-stu-id="a1395-637">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="a1395-638">Interfejs API rejestrowania nie zawiera scenariusza zmiany poziomów rejestrowania, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="a1395-638">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="a1395-639">Niektórzy dostawcy konfiguracji mogą jednak ponownie ładować konfigurację, która zacznie natychmiastowo wpływać na konfigurację rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a1395-639">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="a1395-640">Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider), który jest dodawany przez `CreateDefaultBuilder` program do odczytu plików ustawień, ponownie ładuje konfigurację rejestrowania domyślnie.</span><span class="sxs-lookup"><span data-stu-id="a1395-640">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="a1395-641">Jeśli konfiguracja zostanie zmieniona w kodzie w czasie, gdy aplikacja jest uruchomiona, aplikacja może wywołać [IConfigurationRoot. reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) , aby zaktualizować konfigurację rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-641">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="a1395-642">Informacje o implementowaniu dostawców konfiguracji znajdują <xref:fundamentals/configuration/index>się w temacie.</span><span class="sxs-lookup"><span data-stu-id="a1395-642">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="a1395-643">Przykładowe dane wyjściowe rejestrowania</span><span class="sxs-lookup"><span data-stu-id="a1395-643">Sample logging output</span></span>

<span data-ttu-id="a1395-644">W przypadku przykładowego kodu podanego w poprzedniej sekcji Dzienniki są wyświetlane w konsoli programu, gdy aplikacja jest uruchamiana z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="a1395-644">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="a1395-645">Oto przykład danych wyjściowych konsoli:</span><span class="sxs-lookup"><span data-stu-id="a1395-645">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

<span data-ttu-id="a1395-646">Poprzednie dzienniki zostały wygenerowane przez utworzenie żądania HTTP GET do przykładowej aplikacji w lokalizacji `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="a1395-646">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="a1395-647">Oto przykład tych samych dzienników, które są wyświetlane w oknie debugowania podczas uruchamiania przykładowej aplikacji w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a1395-647">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="a1395-648">Dzienniki utworzone przez `ILogger` wywołania pokazane w poprzedniej sekcji zaczynają się od "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="a1395-648">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="a1395-649">Dzienniki zaczynające się od kategorii "Microsoft" pochodzą z kodu ASP.NET Core Framework.</span><span class="sxs-lookup"><span data-stu-id="a1395-649">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="a1395-650">ASP.NET Core i kod aplikacji używają tego samego rejestrowania interfejsu API i dostawców.</span><span class="sxs-lookup"><span data-stu-id="a1395-650">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="a1395-651">W pozostałej części tego artykułu opisano niektóre szczegóły i opcje rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a1395-651">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="a1395-652">Pakiety NuGet</span><span class="sxs-lookup"><span data-stu-id="a1395-652">NuGet packages</span></span>

<span data-ttu-id="a1395-653">Interfejsy `ILogger` i `ILoggerFactory` są w [Microsoft. Extensions. Logging. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)i domyślne implementacje dla nich są w [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="a1395-653">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="a1395-654">Kategoria dziennika</span><span class="sxs-lookup"><span data-stu-id="a1395-654">Log category</span></span>

<span data-ttu-id="a1395-655">Po utworzeniu `ILogger` obiektu jest dla niego określona *Kategoria* .</span><span class="sxs-lookup"><span data-stu-id="a1395-655">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="a1395-656">Ta kategoria jest dołączona do każdego komunikatu dziennika utworzonego przez to wystąpienie `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="a1395-656">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="a1395-657">Kategoria może być dowolnym ciągiem, ale Konwencja ma używać nazwy klasy, takiej jak "TodoApi. controllers. TodoController".</span><span class="sxs-lookup"><span data-stu-id="a1395-657">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="a1395-658">Użyj `ILogger<T>` , aby uzyskać `ILogger` wystąpienie używające w pełni kwalifikowanej nazwy typu `T` jako kategorii:</span><span class="sxs-lookup"><span data-stu-id="a1395-658">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="a1395-659">Aby jawnie określić kategorię, wywołaj `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="a1395-659">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="a1395-660">`ILogger<T>`jest odpowiednikiem wywołania `CreateLogger` z w pełni kwalifikowaną nazwą typu `T`.</span><span class="sxs-lookup"><span data-stu-id="a1395-660">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="a1395-661">Poziom dziennika</span><span class="sxs-lookup"><span data-stu-id="a1395-661">Log level</span></span>

<span data-ttu-id="a1395-662">Każdy dziennik Określa <xref:Microsoft.Extensions.Logging.LogLevel> wartość.</span><span class="sxs-lookup"><span data-stu-id="a1395-662">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="a1395-663">Poziom dziennika wskazuje ważność lub ważność.</span><span class="sxs-lookup"><span data-stu-id="a1395-663">The log level indicates the severity or importance.</span></span> <span data-ttu-id="a1395-664">Przykładowo można napisać `Information` dziennik, gdy metoda jest zwykle zakończona, a `Warning` dziennik, gdy metoda zwraca 404, *nie znaleziono* kodu stanu.</span><span class="sxs-lookup"><span data-stu-id="a1395-664">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="a1395-665">Poniższy kod tworzy `Information` i `Warning` rejestruje:</span><span class="sxs-lookup"><span data-stu-id="a1395-665">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="a1395-666">W powyższym kodzie pierwszy parametr jest [identyfikatorem zdarzenia dziennika](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="a1395-666">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="a1395-667">Drugi parametr jest szablonem wiadomości z symbolami zastępczymi dla wartości argumentów dostarczonych przez pozostałe parametry metody.</span><span class="sxs-lookup"><span data-stu-id="a1395-667">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="a1395-668">Parametry metody zostały wyjaśnione w [sekcji szablon komunikatu](#log-message-template) w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="a1395-668">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="a1395-669">Metody rejestrowania, które obejmują poziom w nazwie metody (na przykład `LogInformation` i `LogWarning`) są [metodami rozszerzającymi dla ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="a1395-669">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="a1395-670">Te metody wywołują `Log` metodę, która pobiera `LogLevel` parametr.</span><span class="sxs-lookup"><span data-stu-id="a1395-670">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="a1395-671">`Log` Metodę można wywołać bezpośrednio zamiast jednej z tych metod rozszerzających, ale składnia jest stosunkowo skomplikowana.</span><span class="sxs-lookup"><span data-stu-id="a1395-671">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="a1395-672">Aby uzyskać więcej informacji, <xref:Microsoft.Extensions.Logging.ILogger> Zobacz i [kod źródłowy rozszerzeń rejestratora](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="a1395-672">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="a1395-673">ASP.NET Core definiuje następujące poziomy dziennika uporządkowane w tym miejscu od najniższej do najwyższej wagi.</span><span class="sxs-lookup"><span data-stu-id="a1395-673">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="a1395-674">Ślad = 0</span><span class="sxs-lookup"><span data-stu-id="a1395-674">Trace = 0</span></span>

  <span data-ttu-id="a1395-675">Aby uzyskać informacje, które są zazwyczaj cenne tylko dla debugowania.</span><span class="sxs-lookup"><span data-stu-id="a1395-675">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="a1395-676">Komunikaty te mogą zawierać poufne dane aplikacji, dlatego nie powinny być włączone w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="a1395-676">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="a1395-677">*Domyślnie wyłączona.*</span><span class="sxs-lookup"><span data-stu-id="a1395-677">*Disabled by default.*</span></span>

* <span data-ttu-id="a1395-678">Debuguj = 1</span><span class="sxs-lookup"><span data-stu-id="a1395-678">Debug = 1</span></span>

  <span data-ttu-id="a1395-679">Informacje, które mogą być przydatne podczas tworzenia i debugowania.</span><span class="sxs-lookup"><span data-stu-id="a1395-679">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="a1395-680">Przykład: `Entering method Configure with flag set to true.` Włącz `Debug` dzienniki poziomów w środowisku produkcyjnym tylko w przypadku rozwiązywania problemów, ze względu na dużą ilość dzienników.</span><span class="sxs-lookup"><span data-stu-id="a1395-680">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="a1395-681">Informacje = 2</span><span class="sxs-lookup"><span data-stu-id="a1395-681">Information = 2</span></span>

  <span data-ttu-id="a1395-682">Do śledzenia ogólnego przepływu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-682">For tracking the general flow of the app.</span></span> <span data-ttu-id="a1395-683">Te dzienniki zwykle mają pewną wartość długoterminową.</span><span class="sxs-lookup"><span data-stu-id="a1395-683">These logs typically have some long-term value.</span></span> <span data-ttu-id="a1395-684">Przykład: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="a1395-684">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="a1395-685">Ostrzeżenie = 3</span><span class="sxs-lookup"><span data-stu-id="a1395-685">Warning = 3</span></span>

  <span data-ttu-id="a1395-686">Dla nietypowych lub nieoczekiwanych zdarzeń w przepływie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-686">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="a1395-687">Mogą to być błędy lub inne warunki, które nie powodują zatrzymania aplikacji, ale konieczne może być zbadanie.</span><span class="sxs-lookup"><span data-stu-id="a1395-687">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="a1395-688">Obsłużone wyjątki są typowym miejscem do korzystania `Warning` z poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="a1395-688">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="a1395-689">Przykład: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="a1395-689">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="a1395-690">Błąd = 4</span><span class="sxs-lookup"><span data-stu-id="a1395-690">Error = 4</span></span>

  <span data-ttu-id="a1395-691">W przypadku błędów i wyjątków, których nie można obsłużyć.</span><span class="sxs-lookup"><span data-stu-id="a1395-691">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="a1395-692">Te komunikaty wskazują niepowodzenie w bieżącym działaniu lub operacji (np. bieżące żądanie HTTP), a nie awaria całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-692">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="a1395-693">Przykładowy komunikat dziennika:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="a1395-693">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="a1395-694">Krytyczne = 5</span><span class="sxs-lookup"><span data-stu-id="a1395-694">Critical = 5</span></span>

  <span data-ttu-id="a1395-695">Dla niepowodzeń, które wymagają natychmiastowej uwagi.</span><span class="sxs-lookup"><span data-stu-id="a1395-695">For failures that require immediate attention.</span></span> <span data-ttu-id="a1395-696">Przykłady: scenariusze utraty danych, brak miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="a1395-696">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="a1395-697">Poziom dziennika służy do kontrolowania, ile danych wyjściowych dziennika jest zapisywana w określonym nośniku lub oknie wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="a1395-697">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="a1395-698">Przykład:</span><span class="sxs-lookup"><span data-stu-id="a1395-698">For example:</span></span>

* <span data-ttu-id="a1395-699">W środowisku produkcyjnym:</span><span class="sxs-lookup"><span data-stu-id="a1395-699">In production:</span></span>
  * <span data-ttu-id="a1395-700">Rejestrowanie na poziomie `Trace` za `Information` pomocą poziomów zapewnia wysoką liczbę szczegółowych komunikatów dziennika.</span><span class="sxs-lookup"><span data-stu-id="a1395-700">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="a1395-701">Aby kontrolować koszty i nie przekraczać limitów magazynowania danych `Trace` , `Information` należy rejestrować komunikaty na poziomie w magazynach o wysokim poziomie ilości danych.</span><span class="sxs-lookup"><span data-stu-id="a1395-701">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="a1395-702">Rejestrowanie na `Warning` poziomie `Critical` poziomów zwykle generuje mniejszą liczbę mniejszych komunikatów dzienników.</span><span class="sxs-lookup"><span data-stu-id="a1395-702">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="a1395-703">W związku z tym koszty i limity magazynu zazwyczaj nie są problemem, co skutkuje większą elastycznością wyboru magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="a1395-703">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="a1395-704">Podczas tworzenia:</span><span class="sxs-lookup"><span data-stu-id="a1395-704">During development:</span></span>
  * <span data-ttu-id="a1395-705">Rejestruj `Warning` `Critical` komunikaty w konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="a1395-705">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="a1395-706">Dodawanie `Trace` `Information` komunikatów podczas rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="a1395-706">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="a1395-707">W sekcji [filtrowanie dzienników](#log-filtering) w dalszej części tego artykułu wyjaśniono, jak kontrolować poziomy dzienników obsługiwane przez dostawcę.</span><span class="sxs-lookup"><span data-stu-id="a1395-707">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="a1395-708">ASP.NET Core zapisuje dzienniki dla zdarzeń struktury.</span><span class="sxs-lookup"><span data-stu-id="a1395-708">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="a1395-709">Przykłady dzienników znajdujące się wcześniej w tym artykule nie `Information` wykluczają dzienników `Debug` poniżej `Trace` , dlatego nie zostały utworzone dzienniki.</span><span class="sxs-lookup"><span data-stu-id="a1395-709">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="a1395-710">Oto przykład dzienników konsoli utworzonych przez uruchomienie przykładowej aplikacji skonfigurowanej do wyświetlania `Debug` dzienników:</span><span class="sxs-lookup"><span data-stu-id="a1395-710">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="a1395-711">Identyfikator zdarzenia dziennika</span><span class="sxs-lookup"><span data-stu-id="a1395-711">Log event ID</span></span>

<span data-ttu-id="a1395-712">Każdy dziennik może określać *Identyfikator zdarzenia*.</span><span class="sxs-lookup"><span data-stu-id="a1395-712">Each log can specify an *event ID*.</span></span> <span data-ttu-id="a1395-713">Aplikacja Przykładowa wykonuje to przy użyciu lokalnie zdefiniowanej `LoggingEvents` klasy:</span><span class="sxs-lookup"><span data-stu-id="a1395-713">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="a1395-714">Identyfikator zdarzenia kojarzy zestaw zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="a1395-714">An event ID associates a set of events.</span></span> <span data-ttu-id="a1395-715">Na przykład wszystkie dzienniki związane z wyświetlaniem listy elementów na stronie mogą być 1001.</span><span class="sxs-lookup"><span data-stu-id="a1395-715">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="a1395-716">Dostawca rejestrowania może przechowywać identyfikator zdarzenia w polu identyfikatora, w komunikacie rejestrowania lub wcale.</span><span class="sxs-lookup"><span data-stu-id="a1395-716">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="a1395-717">Dostawca debugowania nie pokazuje identyfikatorów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="a1395-717">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="a1395-718">Dostawca konsoli pokazuje identyfikatory zdarzeń w nawiasach po kategorii:</span><span class="sxs-lookup"><span data-stu-id="a1395-718">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="a1395-719">Szablon komunikatu dziennika</span><span class="sxs-lookup"><span data-stu-id="a1395-719">Log message template</span></span>

<span data-ttu-id="a1395-720">Każdy dziennik Określa szablon wiadomości.</span><span class="sxs-lookup"><span data-stu-id="a1395-720">Each log specifies a message template.</span></span> <span data-ttu-id="a1395-721">Szablon wiadomości może zawierać symbole zastępcze, dla których podano argumenty.</span><span class="sxs-lookup"><span data-stu-id="a1395-721">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="a1395-722">Użyj nazw dla symboli zastępczych, a nie liczby.</span><span class="sxs-lookup"><span data-stu-id="a1395-722">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="a1395-723">Kolejność symboli zastępczych, nie ich nazw, określa, które parametry są używane do dostarczania ich wartości.</span><span class="sxs-lookup"><span data-stu-id="a1395-723">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="a1395-724">W poniższym kodzie Zwróć uwagę, że nazwy parametrów są poza kolejnością w szablonie wiadomości:</span><span class="sxs-lookup"><span data-stu-id="a1395-724">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="a1395-725">Ten kod tworzy komunikat dziennika z wartościami parametrów w kolejności:</span><span class="sxs-lookup"><span data-stu-id="a1395-725">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="a1395-726">Struktura rejestrowania działa w ten sposób, aby dostawcy rejestrowania mogli zaimplementować [Rejestrowanie semantyczne, znane także jako rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="a1395-726">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="a1395-727">Same argumenty są przesyłane do systemu rejestrowania, a nie tylko dla sformatowanego szablonu wiadomości.</span><span class="sxs-lookup"><span data-stu-id="a1395-727">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="a1395-728">Te informacje umożliwiają dostawcom rejestrowania przechowywanie wartości parametrów jako pól.</span><span class="sxs-lookup"><span data-stu-id="a1395-728">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="a1395-729">Na przykład załóżmy, że wywołania metod rejestratora wyglądają następująco:</span><span class="sxs-lookup"><span data-stu-id="a1395-729">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="a1395-730">W przypadku wysyłania dzienników do usługi Azure Table Storage każda jednostka tabeli platformy Azure może mieć `ID` właściwości i `RequestTime` , które upraszczają zapytania dotyczące danych dziennika.</span><span class="sxs-lookup"><span data-stu-id="a1395-730">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="a1395-731">Zapytanie może znaleźć wszystkie dzienniki w określonym `RequestTime` zakresie bez analizowania limitu czasu wiadomości tekstowej.</span><span class="sxs-lookup"><span data-stu-id="a1395-731">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="a1395-732">Wyjątki rejestrowania</span><span class="sxs-lookup"><span data-stu-id="a1395-732">Logging exceptions</span></span>

<span data-ttu-id="a1395-733">Metody rejestratora mają przeciążenia umożliwiające przekazanie wyjątku, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a1395-733">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="a1395-734">Różni dostawcy obsługują informacje o wyjątkach na różne sposoby.</span><span class="sxs-lookup"><span data-stu-id="a1395-734">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="a1395-735">Oto przykład danych wyjściowych dostawcy debugowania z kodu pokazanego powyżej.</span><span class="sxs-lookup"><span data-stu-id="a1395-735">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="a1395-736">Filtrowanie dzienników</span><span class="sxs-lookup"><span data-stu-id="a1395-736">Log filtering</span></span>

<span data-ttu-id="a1395-737">Można określić minimalny poziom rejestrowania dla określonego dostawcy i kategorii lub dla wszystkich dostawców lub wszystkich kategorii.</span><span class="sxs-lookup"><span data-stu-id="a1395-737">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="a1395-738">Wszystkie dzienniki poniżej minimalnego poziomu nie są przesyłane do tego dostawcy, więc nie są wyświetlane ani przechowywane.</span><span class="sxs-lookup"><span data-stu-id="a1395-738">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="a1395-739">Aby pominąć wszystkie dzienniki, określ `LogLevel.None` jako minimalny poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="a1395-739">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="a1395-740">Wartość całkowita `LogLevel.None` wynosi 6, która jest większa niż `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="a1395-740">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="a1395-741">Utwórz reguły filtru w konfiguracji</span><span class="sxs-lookup"><span data-stu-id="a1395-741">Create filter rules in configuration</span></span>

<span data-ttu-id="a1395-742">Kod szablonu projektu wywołuje `CreateDefaultBuilder` w celu skonfigurowania rejestrowania dla dostawców konsoli, debugowania i EventSource (ASP.NET Core 2,2 lub nowszych).</span><span class="sxs-lookup"><span data-stu-id="a1395-742">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="a1395-743">`CreateDefaultBuilder` Metoda konfiguruje rejestrowanie, aby wyszukać konfigurację w `Logging` sekcji, jak wyjaśniono [wcześniej w tym artykule](#configuration).</span><span class="sxs-lookup"><span data-stu-id="a1395-743">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="a1395-744">Dane konfiguracyjne określają minimalne poziomy dziennika według dostawcy i kategorii, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="a1395-744">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="a1395-745">Ten kod JSON tworzy sześć reguł filtrowania: jeden dla dostawcy debugowania, cztery dla dostawcy konsoli i jeden dla wszystkich dostawców.</span><span class="sxs-lookup"><span data-stu-id="a1395-745">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="a1395-746">Dla każdego dostawcy wybierana jest pojedyncza reguła, `ILogger` gdy tworzony jest obiekt.</span><span class="sxs-lookup"><span data-stu-id="a1395-746">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="a1395-747">Filtrowanie reguł w kodzie</span><span class="sxs-lookup"><span data-stu-id="a1395-747">Filter rules in code</span></span>

<span data-ttu-id="a1395-748">Poniższy przykład pokazuje, jak zarejestrować reguły filtru w kodzie:</span><span class="sxs-lookup"><span data-stu-id="a1395-748">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="a1395-749">Drugi `AddFilter` określa dostawcę debugowania za pomocą nazwy typu.</span><span class="sxs-lookup"><span data-stu-id="a1395-749">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="a1395-750">Pierwszy `AddFilter` ma zastosowanie do wszystkich dostawców, ponieważ nie określa typu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="a1395-750">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="a1395-751">Jak są stosowane reguły filtrowania</span><span class="sxs-lookup"><span data-stu-id="a1395-751">How filtering rules are applied</span></span>

<span data-ttu-id="a1395-752">Dane konfiguracji i `AddFilter` kod przedstawiony w powyższych przykładach tworzą reguły pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="a1395-752">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="a1395-753">Pierwsze sześć pochodzi z przykładu konfiguracji, a ostatnie dwa pochodzą z przykładu kodu.</span><span class="sxs-lookup"><span data-stu-id="a1395-753">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="a1395-754">Liczba</span><span class="sxs-lookup"><span data-stu-id="a1395-754">Number</span></span> | <span data-ttu-id="a1395-755">Dostawca</span><span class="sxs-lookup"><span data-stu-id="a1395-755">Provider</span></span>      | <span data-ttu-id="a1395-756">Kategorie zaczynające się od...</span><span class="sxs-lookup"><span data-stu-id="a1395-756">Categories that begin with ...</span></span>          | <span data-ttu-id="a1395-757">Minimalny poziom rejestrowania</span><span class="sxs-lookup"><span data-stu-id="a1395-757">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="a1395-758">1</span><span class="sxs-lookup"><span data-stu-id="a1395-758">1</span></span>      | <span data-ttu-id="a1395-759">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="a1395-759">Debug</span></span>         | <span data-ttu-id="a1395-760">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="a1395-760">All categories</span></span>                          | <span data-ttu-id="a1395-761">Informacje</span><span class="sxs-lookup"><span data-stu-id="a1395-761">Information</span></span>       |
| <span data-ttu-id="a1395-762">2</span><span class="sxs-lookup"><span data-stu-id="a1395-762">2</span></span>      | <span data-ttu-id="a1395-763">Konsola</span><span class="sxs-lookup"><span data-stu-id="a1395-763">Console</span></span>       | <span data-ttu-id="a1395-764">Microsoft. AspNetCore. MVC. Razor. Internal</span><span class="sxs-lookup"><span data-stu-id="a1395-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="a1395-765">Ostrzeżenie</span><span class="sxs-lookup"><span data-stu-id="a1395-765">Warning</span></span>           |
| <span data-ttu-id="a1395-766">3</span><span class="sxs-lookup"><span data-stu-id="a1395-766">3</span></span>      | <span data-ttu-id="a1395-767">Konsola</span><span class="sxs-lookup"><span data-stu-id="a1395-767">Console</span></span>       | <span data-ttu-id="a1395-768">Microsoft. AspNetCore. MVC. Razor. Razor</span><span class="sxs-lookup"><span data-stu-id="a1395-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="a1395-769">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="a1395-769">Debug</span></span>             |
| <span data-ttu-id="a1395-770">4</span><span class="sxs-lookup"><span data-stu-id="a1395-770">4</span></span>      | <span data-ttu-id="a1395-771">Konsola</span><span class="sxs-lookup"><span data-stu-id="a1395-771">Console</span></span>       | <span data-ttu-id="a1395-772">Microsoft. AspNetCore. MVC. Razor</span><span class="sxs-lookup"><span data-stu-id="a1395-772">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="a1395-773">Błąd</span><span class="sxs-lookup"><span data-stu-id="a1395-773">Error</span></span>             |
| <span data-ttu-id="a1395-774">5</span><span class="sxs-lookup"><span data-stu-id="a1395-774">5</span></span>      | <span data-ttu-id="a1395-775">Konsola</span><span class="sxs-lookup"><span data-stu-id="a1395-775">Console</span></span>       | <span data-ttu-id="a1395-776">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="a1395-776">All categories</span></span>                          | <span data-ttu-id="a1395-777">Informacje</span><span class="sxs-lookup"><span data-stu-id="a1395-777">Information</span></span>       |
| <span data-ttu-id="a1395-778">6</span><span class="sxs-lookup"><span data-stu-id="a1395-778">6</span></span>      | <span data-ttu-id="a1395-779">Wszyscy dostawcy</span><span class="sxs-lookup"><span data-stu-id="a1395-779">All providers</span></span> | <span data-ttu-id="a1395-780">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="a1395-780">All categories</span></span>                          | <span data-ttu-id="a1395-781">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="a1395-781">Debug</span></span>             |
| <span data-ttu-id="a1395-782">7</span><span class="sxs-lookup"><span data-stu-id="a1395-782">7</span></span>      | <span data-ttu-id="a1395-783">Wszyscy dostawcy</span><span class="sxs-lookup"><span data-stu-id="a1395-783">All providers</span></span> | <span data-ttu-id="a1395-784">System</span><span class="sxs-lookup"><span data-stu-id="a1395-784">System</span></span>                                  | <span data-ttu-id="a1395-785">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="a1395-785">Debug</span></span>             |
| <span data-ttu-id="a1395-786">8</span><span class="sxs-lookup"><span data-stu-id="a1395-786">8</span></span>      | <span data-ttu-id="a1395-787">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="a1395-787">Debug</span></span>         | <span data-ttu-id="a1395-788">Microsoft</span><span class="sxs-lookup"><span data-stu-id="a1395-788">Microsoft</span></span>                               | <span data-ttu-id="a1395-789">Ślad</span><span class="sxs-lookup"><span data-stu-id="a1395-789">Trace</span></span>             |

<span data-ttu-id="a1395-790">Po utworzeniu `ILogger` obiektu `ILoggerFactory` obiekt wybiera jedną regułę dla każdego dostawcy, która ma zostać zastosowana do tego rejestratora.</span><span class="sxs-lookup"><span data-stu-id="a1395-790">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="a1395-791">Wszystkie komunikaty zapisywane przez `ILogger` wystąpienie są filtrowane na podstawie wybranych reguł.</span><span class="sxs-lookup"><span data-stu-id="a1395-791">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="a1395-792">Najbardziej konkretną regułą można wybrać dla każdego dostawcy i pary kategorii z dostępnych reguł.</span><span class="sxs-lookup"><span data-stu-id="a1395-792">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="a1395-793">Następujący algorytm jest używany dla każdego dostawcy, `ILogger` gdy jest tworzony dla danej kategorii:</span><span class="sxs-lookup"><span data-stu-id="a1395-793">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="a1395-794">Wybierz wszystkie reguły, które pasują do dostawcy lub jego aliasu.</span><span class="sxs-lookup"><span data-stu-id="a1395-794">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="a1395-795">Jeśli nie zostanie znalezione dopasowanie, zaznacz wszystkie reguły z pustym dostawcą.</span><span class="sxs-lookup"><span data-stu-id="a1395-795">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="a1395-796">W wyniku poprzedniego kroku wybierz pozycję reguły z najdłuższym prefiksem kategorii.</span><span class="sxs-lookup"><span data-stu-id="a1395-796">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="a1395-797">Jeśli nie zostanie znalezione dopasowanie, zaznacz wszystkie reguły, które nie określają kategorii.</span><span class="sxs-lookup"><span data-stu-id="a1395-797">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="a1395-798">Jeśli wybrano wiele reguł, zrób to **ostatnie** .</span><span class="sxs-lookup"><span data-stu-id="a1395-798">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="a1395-799">Jeśli nie wybrano żadnych reguł, `MinimumLevel`Użyj.</span><span class="sxs-lookup"><span data-stu-id="a1395-799">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="a1395-800">Na powyższej liście reguł Załóżmy, że tworzysz `ILogger` obiekt dla kategorii "Microsoft. AspNetCore. MVC. Razor. RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="a1395-800">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="a1395-801">Dla dostawcy debugowania obowiązują reguły 1, 6 i 8.</span><span class="sxs-lookup"><span data-stu-id="a1395-801">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="a1395-802">Reguła 8 jest najbardziej specyficzna, więc jest to jedna wybrana.</span><span class="sxs-lookup"><span data-stu-id="a1395-802">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="a1395-803">W przypadku dostawcy konsoli obowiązują reguły 3, 4, 5 i 6.</span><span class="sxs-lookup"><span data-stu-id="a1395-803">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="a1395-804">Reguła 3 jest najbardziej specyficzna.</span><span class="sxs-lookup"><span data-stu-id="a1395-804">Rule 3 is most specific.</span></span>

<span data-ttu-id="a1395-805">Wystąpienie wyników `ILogger` wysyła dzienniki `Trace` poziomu i powyżej do dostawcy debugowania.</span><span class="sxs-lookup"><span data-stu-id="a1395-805">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="a1395-806">Dzienniki `Debug` poziomów i powyżej są wysyłane do dostawcy konsoli.</span><span class="sxs-lookup"><span data-stu-id="a1395-806">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="a1395-807">Aliasy dostawców</span><span class="sxs-lookup"><span data-stu-id="a1395-807">Provider aliases</span></span>

<span data-ttu-id="a1395-808">Każdy dostawca definiuje *alias* , który może być używany w konfiguracji zamiast w pełni kwalifikowanej nazwy typu.</span><span class="sxs-lookup"><span data-stu-id="a1395-808">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="a1395-809">W przypadku dostawców wbudowanych Użyj następujących aliasów:</span><span class="sxs-lookup"><span data-stu-id="a1395-809">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="a1395-810">Konsola</span><span class="sxs-lookup"><span data-stu-id="a1395-810">Console</span></span>
* <span data-ttu-id="a1395-811">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="a1395-811">Debug</span></span>
* <span data-ttu-id="a1395-812">EventSource</span><span class="sxs-lookup"><span data-stu-id="a1395-812">EventSource</span></span>
* <span data-ttu-id="a1395-813">Elemencie</span><span class="sxs-lookup"><span data-stu-id="a1395-813">EventLog</span></span>
* <span data-ttu-id="a1395-814">TraceSource</span><span class="sxs-lookup"><span data-stu-id="a1395-814">TraceSource</span></span>
* <span data-ttu-id="a1395-815">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="a1395-815">AzureAppServicesFile</span></span>
* <span data-ttu-id="a1395-816">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="a1395-816">AzureAppServicesBlob</span></span>
* <span data-ttu-id="a1395-817">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="a1395-817">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="a1395-818">Domyślny poziom minimalny</span><span class="sxs-lookup"><span data-stu-id="a1395-818">Default minimum level</span></span>

<span data-ttu-id="a1395-819">Istnieje ustawienie minimalnego poziomu, które działa tylko wtedy, gdy nie mają zastosowania żadne reguły z konfiguracji lub kodu dla danego dostawcy i kategorii.</span><span class="sxs-lookup"><span data-stu-id="a1395-819">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="a1395-820">Poniższy przykład pokazuje, jak ustawić poziom minimalny:</span><span class="sxs-lookup"><span data-stu-id="a1395-820">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="a1395-821">Jeśli poziom minimalny nie został jawnie ustawiony, wartość domyślna to `Information`, co oznacza, że `Trace` dzienniki i `Debug` są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="a1395-821">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="a1395-822">Funkcje filtrowania</span><span class="sxs-lookup"><span data-stu-id="a1395-822">Filter functions</span></span>

<span data-ttu-id="a1395-823">Funkcja filtru jest wywoływana dla wszystkich dostawców i kategorii, które nie mają przypisanych do nich reguł przez konfigurację lub kod.</span><span class="sxs-lookup"><span data-stu-id="a1395-823">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="a1395-824">Kod w funkcji ma dostęp do typu dostawcy, kategorii i poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="a1395-824">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="a1395-825">Przykład:</span><span class="sxs-lookup"><span data-stu-id="a1395-825">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="a1395-826">Kategorie i poziomy systemu</span><span class="sxs-lookup"><span data-stu-id="a1395-826">System categories and levels</span></span>

<span data-ttu-id="a1395-827">Poniżej przedstawiono niektóre kategorie używane przez ASP.NET Core i Entity Framework Core, z informacjami o dziennikach, od których należy się spodziewać:</span><span class="sxs-lookup"><span data-stu-id="a1395-827">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="a1395-828">Kategoria</span><span class="sxs-lookup"><span data-stu-id="a1395-828">Category</span></span>                            | <span data-ttu-id="a1395-829">Uwagi</span><span class="sxs-lookup"><span data-stu-id="a1395-829">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="a1395-830">Microsoft. AspNetCore</span><span class="sxs-lookup"><span data-stu-id="a1395-830">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="a1395-831">Ogólna Diagnostyka ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1395-831">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="a1395-832">Microsoft. AspNetCore. dataprotection</span><span class="sxs-lookup"><span data-stu-id="a1395-832">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="a1395-833">Które klucze zostały wzięte pod uwagę, znaleziono i użyte.</span><span class="sxs-lookup"><span data-stu-id="a1395-833">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="a1395-834">Microsoft. AspNetCore. HostFiltering</span><span class="sxs-lookup"><span data-stu-id="a1395-834">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="a1395-835">Dozwolone hosty.</span><span class="sxs-lookup"><span data-stu-id="a1395-835">Hosts allowed.</span></span> |
| <span data-ttu-id="a1395-836">Microsoft. AspNetCore. hosting</span><span class="sxs-lookup"><span data-stu-id="a1395-836">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="a1395-837">Jak długo trwa wykonywanie żądań HTTP i czas ich uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="a1395-837">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="a1395-838">Które hostowanie zestawów uruchamiania zostało załadowane.</span><span class="sxs-lookup"><span data-stu-id="a1395-838">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="a1395-839">Microsoft. AspNetCore. MVC</span><span class="sxs-lookup"><span data-stu-id="a1395-839">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="a1395-840">Diagnostyka MVC i Razor.</span><span class="sxs-lookup"><span data-stu-id="a1395-840">MVC and Razor diagnostics.</span></span> <span data-ttu-id="a1395-841">Powiązanie modelu, wykonywanie filtru, kompilacja widoku, wybór akcji.</span><span class="sxs-lookup"><span data-stu-id="a1395-841">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="a1395-842">Microsoft. AspNetCore. Routing</span><span class="sxs-lookup"><span data-stu-id="a1395-842">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="a1395-843">Informacje o trasie.</span><span class="sxs-lookup"><span data-stu-id="a1395-843">Route matching information.</span></span> |
| <span data-ttu-id="a1395-844">Microsoft. AspNetCore. Server</span><span class="sxs-lookup"><span data-stu-id="a1395-844">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="a1395-845">Reagowanie na uruchamianie, zatrzymywanie i utrzymywanie aktywności.</span><span class="sxs-lookup"><span data-stu-id="a1395-845">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="a1395-846">Informacje o certyfikacie HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a1395-846">HTTPS certificate information.</span></span> |
| <span data-ttu-id="a1395-847">Microsoft. AspNetCore. StaticFiles</span><span class="sxs-lookup"><span data-stu-id="a1395-847">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="a1395-848">Obsługiwane pliki.</span><span class="sxs-lookup"><span data-stu-id="a1395-848">Files served.</span></span> |
| <span data-ttu-id="a1395-849">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="a1395-849">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="a1395-850">Ogólna Diagnostyka Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="a1395-850">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="a1395-851">Aktywność i Konfiguracja bazy danych, wykrywanie zmian, migracje.</span><span class="sxs-lookup"><span data-stu-id="a1395-851">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="a1395-852">Zakresy dziennika</span><span class="sxs-lookup"><span data-stu-id="a1395-852">Log scopes</span></span>

 <span data-ttu-id="a1395-853">*Zakres* może grupować zestaw operacji logicznych.</span><span class="sxs-lookup"><span data-stu-id="a1395-853">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="a1395-854">Takie grupowanie może służyć do dołączania tych samych danych do każdego dziennika, który został utworzony jako część zestawu.</span><span class="sxs-lookup"><span data-stu-id="a1395-854">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="a1395-855">Na przykład każdy dziennik utworzony w ramach przetwarzania transakcji może zawierać identyfikator transakcji.</span><span class="sxs-lookup"><span data-stu-id="a1395-855">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="a1395-856">Zakres jest `IDisposable` typem zwracanym przez <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodę i obowiązuje do momentu jego usunięcia.</span><span class="sxs-lookup"><span data-stu-id="a1395-856">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="a1395-857">Użyj zakresu przez Zawijanie wywołań rejestratora w `using` bloku:</span><span class="sxs-lookup"><span data-stu-id="a1395-857">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="a1395-858">Poniższy kod włącza zakresy dla dostawcy konsoli:</span><span class="sxs-lookup"><span data-stu-id="a1395-858">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="a1395-859">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a1395-859">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="a1395-860">Konfigurowanie opcji `IncludeScopes` rejestratora konsoli jest wymagane do włączenia rejestrowania na podstawie zakresu.</span><span class="sxs-lookup"><span data-stu-id="a1395-860">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="a1395-861">Informacje o konfiguracji znajdują się w sekcji [Konfiguracja](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="a1395-861">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="a1395-862">Każdy komunikat dziennika zawiera informacje o zakresie:</span><span class="sxs-lookup"><span data-stu-id="a1395-862">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="a1395-863">Wbudowani dostawcy rejestrowania</span><span class="sxs-lookup"><span data-stu-id="a1395-863">Built-in logging providers</span></span>

<span data-ttu-id="a1395-864">ASP.NET Core dostarcza następujących dostawców:</span><span class="sxs-lookup"><span data-stu-id="a1395-864">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="a1395-865">Konsola</span><span class="sxs-lookup"><span data-stu-id="a1395-865">Console</span></span>](#console-provider)
* [<span data-ttu-id="a1395-866">Rozpocząć</span><span class="sxs-lookup"><span data-stu-id="a1395-866">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="a1395-867">EventSource</span><span class="sxs-lookup"><span data-stu-id="a1395-867">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="a1395-868">Elemencie</span><span class="sxs-lookup"><span data-stu-id="a1395-868">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="a1395-869">TraceSource</span><span class="sxs-lookup"><span data-stu-id="a1395-869">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="a1395-870">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="a1395-870">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="a1395-871">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="a1395-871">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="a1395-872">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="a1395-872">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="a1395-873">Aby uzyskać informacje na temat strumienia stdout i rejestrowania debugowania za pomocą modułu <xref:test/troubleshoot-azure-iis> ASP.NET Core <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>, zobacz i.</span><span class="sxs-lookup"><span data-stu-id="a1395-873">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="a1395-874">Dostawca konsoli</span><span class="sxs-lookup"><span data-stu-id="a1395-874">Console provider</span></span>

<span data-ttu-id="a1395-875">Pakiet [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) Provider wysyła dane wyjściowe dziennika do konsoli programu.</span><span class="sxs-lookup"><span data-stu-id="a1395-875">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="a1395-876">Aby wyświetlić dane wyjściowe rejestrowania konsoli, Otwórz wiersz polecenia w folderze projektu i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="a1395-876">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="a1395-877">Dostawca debugowania</span><span class="sxs-lookup"><span data-stu-id="a1395-877">Debug provider</span></span>

<span data-ttu-id="a1395-878">Pakiet [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) Provider zapisuje dane wyjściowe dziennika przy użyciu klasy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine` wywołania metod).</span><span class="sxs-lookup"><span data-stu-id="a1395-878">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="a1395-879">W systemie Linux ten dostawca zapisuje dzienniki do */var/log/Message*.</span><span class="sxs-lookup"><span data-stu-id="a1395-879">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="a1395-880">Dostawca źródła zdarzeń</span><span class="sxs-lookup"><span data-stu-id="a1395-880">Event Source provider</span></span>

<span data-ttu-id="a1395-881">Pakiet dostawcy [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na międzyplatformę źródła zdarzeń, używając nazwy `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="a1395-881">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="a1395-882">W systemie Windows Dostawca używa [funkcji ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="a1395-882">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="a1395-883">Dostawca źródła zdarzeń zostanie dodany automatycznie, gdy `CreateDefaultBuilder` jest wywoływana w celu skompilowania hosta.</span><span class="sxs-lookup"><span data-stu-id="a1395-883">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="a1395-884">Użyj [Narzędzia Narzędzia PerfView](https://github.com/Microsoft/perfview) do zbierania i wyświetlania dzienników.</span><span class="sxs-lookup"><span data-stu-id="a1395-884">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="a1395-885">Istnieją inne narzędzia do wyświetlania dzienników ETW, ale narzędzia PerfView zapewnia najlepsze środowisko pracy z zdarzeniami ETW emitowanymi przez ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1395-885">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="a1395-886">Aby skonfigurować narzędzia PerfView do zbierania zdarzeń rejestrowanych przez tego dostawcę, Dodaj ciąg `*Microsoft-Extensions-Logging` do listy **dodatkowych dostawców** .</span><span class="sxs-lookup"><span data-stu-id="a1395-886">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="a1395-887">(Nie przegap gwiazdki na początku ciągu znaków).</span><span class="sxs-lookup"><span data-stu-id="a1395-887">(Don't miss the asterisk at the start of the string.)</span></span>

![Narzędzia PerfView dodatkowych dostawców](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="a1395-889">Dostawca dziennika zdarzeń systemu Windows</span><span class="sxs-lookup"><span data-stu-id="a1395-889">Windows EventLog provider</span></span>

<span data-ttu-id="a1395-890">Pakiet dostawcy [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) wysyła dane wyjściowe dziennika do dziennika zdarzeń systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="a1395-890">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="a1395-891">[Przeciążenia addeventlog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umożliwiają przekazywanie <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="a1395-891">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="a1395-892">Jeśli `null` lub nie zostanie określony, są używane następujące ustawienia domyślne:</span><span class="sxs-lookup"><span data-stu-id="a1395-892">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="a1395-893">`LogName`&ndash; "Aplikacja"</span><span class="sxs-lookup"><span data-stu-id="a1395-893">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="a1395-894">`SourceName`&ndash; "Środowisko uruchomieniowe platformy .NET"</span><span class="sxs-lookup"><span data-stu-id="a1395-894">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="a1395-895">`MachineName`&ndash; komputer lokalny</span><span class="sxs-lookup"><span data-stu-id="a1395-895">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="a1395-896">Zdarzenia są rejestrowane dla [poziomu ostrzeżeń i wyższych](#log-level).</span><span class="sxs-lookup"><span data-stu-id="a1395-896">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="a1395-897">Aby rejestrować zdarzenia mniejsze niż `Warning`, jawnie ustaw poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="a1395-897">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="a1395-898">Na przykład Dodaj następujący kod do pliku *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="a1395-898">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="a1395-899">Dostawca TraceSource</span><span class="sxs-lookup"><span data-stu-id="a1395-899">TraceSource provider</span></span>

<span data-ttu-id="a1395-900">Pakiet dostawcy [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) używa <xref:System.Diagnostics.TraceSource> bibliotek i dostawców.</span><span class="sxs-lookup"><span data-stu-id="a1395-900">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="a1395-901">[Przeciążenia AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umożliwiają przekazywanie danych w przełączniku źródłowym i odbiorniku śledzenia.</span><span class="sxs-lookup"><span data-stu-id="a1395-901">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="a1395-902">Aby można było korzystać z tego dostawcy, aplikacja musi działać na .NET Framework (a nie na platformie .NET Core).</span><span class="sxs-lookup"><span data-stu-id="a1395-902">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="a1395-903">Dostawca może kierować komunikaty do różnych [odbiorników](/dotnet/framework/debug-trace-profile/trace-listeners), takich jak <xref:System.Diagnostics.TextWriterTraceListener> używane w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-903">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="a1395-904">Dostawca Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a1395-904">Azure App Service provider</span></span>

<span data-ttu-id="a1395-905">Pakiet [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Provider zapisuje dzienniki w plikach tekstowych w systemie plików aplikacji Azure App Service i w usłudze [BLOB Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) na koncie usługi Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="a1395-905">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="a1395-906">Pakiet dostawcy nie jest uwzględniony w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a1395-906">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="a1395-907">Podczas określania elementu docelowego .NET Framework lub `Microsoft.AspNetCore.App` odwoływania się do niego, Dodaj pakiet dostawcy do projektu.</span><span class="sxs-lookup"><span data-stu-id="a1395-907">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="a1395-908"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> Przeciążenie umożliwia przekazywanie <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="a1395-908">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="a1395-909">Obiekt Settings może przesłonić ustawienia domyślne, takie jak szablon danych wyjściowych rejestrowania, nazwa obiektu BLOB i limit rozmiaru pliku.</span><span class="sxs-lookup"><span data-stu-id="a1395-909">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="a1395-910">(*Szablon danych wyjściowych* to szablon wiadomości, który jest stosowany do wszystkich dzienników oprócz tego, co jest dostępne `ILogger` w wywołaniu metody).</span><span class="sxs-lookup"><span data-stu-id="a1395-910">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="a1395-911">Po wdrożeniu w aplikacji App Service, aplikacja będzie przestrzegać ustawień w sekcji [dzienniki App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stronie **App Service** Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="a1395-911">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="a1395-912">Po zaktualizowaniu następujących ustawień zmiany zaczynają obowiązywać natychmiast, bez konieczności ponownego uruchomienia lub ponownej wdrożenia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-912">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="a1395-913">**Rejestrowanie aplikacji (system plików)**</span><span class="sxs-lookup"><span data-stu-id="a1395-913">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="a1395-914">**Rejestrowanie aplikacji (BLOB)**</span><span class="sxs-lookup"><span data-stu-id="a1395-914">**Application Logging (Blob)**</span></span>

<span data-ttu-id="a1395-915">Domyślną lokalizacją plików dziennika jest folder *D:\\Home\\LogFiles\\* , a domyślna nazwa pliku to *Diagnostics-YYYYMMDD. txt*.</span><span class="sxs-lookup"><span data-stu-id="a1395-915">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="a1395-916">Domyślny limit rozmiaru pliku wynosi 10 MB, a domyślna maksymalna liczba zachowywanych plików to 2.</span><span class="sxs-lookup"><span data-stu-id="a1395-916">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="a1395-917">Domyślną nazwą obiektu BLOB jest *{App-Name} {timestamp}/yyyy/mm/dd/hh/{GUID}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="a1395-917">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="a1395-918">Dostawca działa tylko wtedy, gdy projekt jest uruchomiony w środowisku platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="a1395-918">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="a1395-919">Nie ma ono wpływu, gdy projekt jest uruchamiany lokalnie&mdash;, nie zapisuje w plikach lokalnych ani w lokalnym magazynie programistycznym dla obiektów BLOB.</span><span class="sxs-lookup"><span data-stu-id="a1395-919">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="a1395-920">Przesyłanie strumieniowe dzienników Azure</span><span class="sxs-lookup"><span data-stu-id="a1395-920">Azure log streaming</span></span>

<span data-ttu-id="a1395-921">Usługa przesyłania strumieniowego w usłudze Azure log umożliwia wyświetlanie dziennika aktywności w czasie rzeczywistym z:</span><span class="sxs-lookup"><span data-stu-id="a1395-921">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="a1395-922">Serwer aplikacji</span><span class="sxs-lookup"><span data-stu-id="a1395-922">The app server</span></span>
* <span data-ttu-id="a1395-923">Serwer sieci Web</span><span class="sxs-lookup"><span data-stu-id="a1395-923">The web server</span></span>
* <span data-ttu-id="a1395-924">Śledzenie nieudanych żądań</span><span class="sxs-lookup"><span data-stu-id="a1395-924">Failed request tracing</span></span>

<span data-ttu-id="a1395-925">Aby skonfigurować przesyłanie strumieniowe dzienników Azure:</span><span class="sxs-lookup"><span data-stu-id="a1395-925">To configure Azure log streaming:</span></span>

* <span data-ttu-id="a1395-926">Przejdź do strony **dzienników App Service** ze strony portalu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-926">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="a1395-927">Ustaw **Rejestrowanie aplikacji (system plików)** na **włączone**.</span><span class="sxs-lookup"><span data-stu-id="a1395-927">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="a1395-928">Wybierz **poziom**dziennika.</span><span class="sxs-lookup"><span data-stu-id="a1395-928">Choose the log **Level**.</span></span> <span data-ttu-id="a1395-929">To ustawienie dotyczy tylko przesyłania strumieniowego dzienników platformy Azure, a nie innych dostawców rejestrowania w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-929">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="a1395-930">Przejdź do strony **strumień dziennika** , aby wyświetlić komunikaty aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a1395-930">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="a1395-931">Są one rejestrowane przez aplikację za pomocą `ILogger` interfejsu.</span><span class="sxs-lookup"><span data-stu-id="a1395-931">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="a1395-932">Rejestrowanie śledzenia Application Insights platformy Azure</span><span class="sxs-lookup"><span data-stu-id="a1395-932">Azure Application Insights trace logging</span></span>

<span data-ttu-id="a1395-933">Pakiet [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) Provider zapisuje dzienniki na platformie Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="a1395-933">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="a1395-934">Application Insights to usługa, która monitoruje aplikację internetową i udostępnia narzędzia do wykonywania zapytań i analizowania danych telemetrycznych.</span><span class="sxs-lookup"><span data-stu-id="a1395-934">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="a1395-935">Jeśli używasz tego dostawcy, możesz wysyłać zapytania i analizować dzienniki przy użyciu narzędzi Application Insights.</span><span class="sxs-lookup"><span data-stu-id="a1395-935">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="a1395-936">Dostawca rejestrowania jest dołączony jako zależność [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), który jest pakietem, który zapewnia wszystkie dostępne dane telemetryczne dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a1395-936">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="a1395-937">Jeśli używasz tego pakietu, nie musisz instalować pakietu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="a1395-937">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="a1395-938">Nie używaj&mdash;pakietu [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) , który jest przeznaczony dla ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="a1395-938">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="a1395-939">Więcej informacji zawierają następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="a1395-939">For more information, see the following resources:</span></span>

* [<span data-ttu-id="a1395-940">Omówienie usługi Application Insights</span><span class="sxs-lookup"><span data-stu-id="a1395-940">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="a1395-941">[Application Insights dla ASP.NET Core aplikacji](/azure/azure-monitor/app/asp-net-core) — Zacznij tutaj, jeśli chcesz zaimplementować cały zakres Application Insights telemetrii wraz z rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="a1395-941">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="a1395-942">[ApplicationInsightsLoggerProvider for .NET Core ILogger](/azure/azure-monitor/app/ilogger) — Rozpocznij tutaj, jeśli chcesz zaimplementować dostawcę rejestrowania bez pozostałej części telemetrii Application Insights.</span><span class="sxs-lookup"><span data-stu-id="a1395-942">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="a1395-943">[Karty rejestrowania Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="a1395-943">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="a1395-944">[Zainstaluj, skonfiguruj i zainicjuj samouczek Application INSIGHTS SDK](/learn/modules/instrument-web-app-code-with-application-insights) — interaktywny w witrynie Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="a1395-944">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="a1395-945">Dostawcy rejestrowania innych firm</span><span class="sxs-lookup"><span data-stu-id="a1395-945">Third-party logging providers</span></span>

<span data-ttu-id="a1395-946">Platformy rejestrowania innych firm, które współpracują z ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a1395-946">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="a1395-947">[ELMAH.IO](https://elmah.io/) ([repozytorium GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="a1395-947">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="a1395-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repozytorium GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="a1395-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="a1395-949">[JSNLog](https://jsnlog.com/) ([repozytorium GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="a1395-949">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="a1395-950">[KissLog.NET](https://kisslog.net/) ([repozytorium GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="a1395-950">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="a1395-951">[Log4Net](https://logging.apache.org/log4net/) ([repozytorium GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="a1395-951">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="a1395-952">[Loggr](https://loggr.net/) ([repozytorium GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="a1395-952">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="a1395-953">[NLOG](https://nlog-project.org/) ([repozytorium GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="a1395-953">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="a1395-954">[Sentry](https://sentry.io/welcome/) ([repozytorium GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="a1395-954">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="a1395-955">[Serilog](https://serilog.net/) ([repozytorium GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="a1395-955">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="a1395-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repozytorium GitHub](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="a1395-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="a1395-957">Niektóre struktury innych firm mogą wykonywać [Rejestrowanie semantyczne, znane także jako rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="a1395-957">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="a1395-958">Korzystanie z struktury innej firmy jest podobne do korzystania z jednego z wbudowanych dostawców:</span><span class="sxs-lookup"><span data-stu-id="a1395-958">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="a1395-959">Dodaj pakiet NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="a1395-959">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="a1395-960">Wywoływanie `ILoggerFactory` metody rozszerzenia dostarczonej przez strukturę rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a1395-960">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="a1395-961">Aby uzyskać więcej informacji, zobacz dokumentację każdego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="a1395-961">For more information, see each provider's documentation.</span></span> <span data-ttu-id="a1395-962">Dostawcy rejestrowania innych firm nie są obsługiwani przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a1395-962">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a1395-963">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="a1395-963">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
