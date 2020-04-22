---
title: Logowanie do .NET Core i ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak korzystać z struktury rejestrowania dostarczonej przez pakiet Microsoft.Extensions.Logging NuGet.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/logging/index
ms.openlocfilehash: b897d0d775da62a11f01a64f39b47b6c5abebc8b
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791561"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="9c72d-103">Logowanie do .NET Core i ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9c72d-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9c72d-104">Przez [Tom Dykstra](https://github.com/tdykstra) i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="9c72d-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="9c72d-105">Program .NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi wbudowanymi i zewnętrznymi dostawcami rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="9c72d-106">W tym artykule pokazano, jak używać interfejsu API rejestrowania z wbudowanymi dostawcami.</span><span class="sxs-lookup"><span data-stu-id="9c72d-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="9c72d-107">Większość przykładów kodu pokazano w tym artykule są z ASP.NET podstawowych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="9c72d-108">Części tych fragmentów kodu specyficzne dla rejestrowania mają zastosowanie do dowolnej aplikacji .NET Core, która używa [hosta ogólnego.](xref:fundamentals/host/generic-host)</span><span class="sxs-lookup"><span data-stu-id="9c72d-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="9c72d-109">Na przykład sposobu używania hosta ogólnego w aplikacji konsoli niesienia sieci Web zobacz *Program.cs* plik [przykładowej aplikacji Zadania w tle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span><span class="sxs-lookup"><span data-stu-id="9c72d-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="9c72d-110">Rejestrowanie kodu dla aplikacji bez ogólnego hosta różni się sposobem [dodawania dostawców](#add-providers) i [tworzenia rejestratorów.](#create-logs)</span><span class="sxs-lookup"><span data-stu-id="9c72d-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="9c72d-111">Przykłady kodu niebędącego hostem są wyświetlane w tych sekcjach artykułu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="9c72d-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9c72d-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="9c72d-113">Dodawanie dostawców</span><span class="sxs-lookup"><span data-stu-id="9c72d-113">Add providers</span></span>

<span data-ttu-id="9c72d-114">Dostawca rejestrowania wyświetla lub przechowuje dzienniki.</span><span class="sxs-lookup"><span data-stu-id="9c72d-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="9c72d-115">Na przykład dostawca konsoli wyświetla dzienniki na konsoli, a dostawca usługi Azure Application Insights przechowuje je w usłudze Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="9c72d-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="9c72d-116">Dzienniki mogą być wysyłane do wielu miejsc docelowych, dodając wielu dostawców.</span><span class="sxs-lookup"><span data-stu-id="9c72d-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="9c72d-117">Aby dodać dostawcę w aplikacji korzystającej z hosta `Add{provider name}` ogólnego, należy wywołać metodę rozszerzenia dostawcy w *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="9c72d-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="9c72d-118">W aplikacji konsoli nie-hosta, wywołać metodę `Add{provider name}` rozszerzenia `LoggerFactory`dostawcy podczas tworzenia:</span><span class="sxs-lookup"><span data-stu-id="9c72d-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="9c72d-119">`LoggerFactory`i `AddConsole` wymagają `using` instrukcji `Microsoft.Extensions.Logging`dla .</span><span class="sxs-lookup"><span data-stu-id="9c72d-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="9c72d-120">Wywołać <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>domyślne szablony projektów ASP.NET Core, które dodaje następujących dostawców rejestrowania:</span><span class="sxs-lookup"><span data-stu-id="9c72d-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="9c72d-121">Konsola</span><span class="sxs-lookup"><span data-stu-id="9c72d-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="9c72d-122">Debugowania</span><span class="sxs-lookup"><span data-stu-id="9c72d-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="9c72d-123">Eventsource</span><span class="sxs-lookup"><span data-stu-id="9c72d-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="9c72d-124">[EventLog](#windows-eventlog-provider) (tylko w przypadku pracy w systemie Windows)</span><span class="sxs-lookup"><span data-stu-id="9c72d-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="9c72d-125">Dostawców domyślnych można zastąpić własnymi wyborami.</span><span class="sxs-lookup"><span data-stu-id="9c72d-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="9c72d-126">Zadzwoń <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>i dodaj dostawców, których chcesz.</span><span class="sxs-lookup"><span data-stu-id="9c72d-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="9c72d-127">Dowiedz się więcej o [wbudowanych dostawców rejestrowania](#built-in-logging-providers) i [dostawców rejestrowania innych firm](#third-party-logging-providers) w dalszej części artykułu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="9c72d-128">Tworzenie dzienników</span><span class="sxs-lookup"><span data-stu-id="9c72d-128">Create logs</span></span>

<span data-ttu-id="9c72d-129">Aby utworzyć dzienniki, <xref:Microsoft.Extensions.Logging.ILogger%601> użyj obiektu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="9c72d-130">W aplikacji sieci web lub usługi `ILogger` hostowanej, uzyskać z iniekcji zależności (DI).</span><span class="sxs-lookup"><span data-stu-id="9c72d-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="9c72d-131">W aplikacjach konsoli nie-hosta użyj go `LoggerFactory` do utworzenia pliku `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="9c72d-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="9c72d-132">Poniższy przykład ASP.NET Core tworzy rejestrator `TodoApiSample.Pages.AboutModel` z jako kategorii.</span><span class="sxs-lookup"><span data-stu-id="9c72d-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="9c72d-133">Kategoria *dziennika* jest ciągiem skojarzonym z każdym dziennikiem.</span><span class="sxs-lookup"><span data-stu-id="9c72d-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="9c72d-134">Wystąpienie `ILogger<T>` dostarczone przez DI tworzy dzienniki, które `T` mają w pełni kwalifikowaną nazwę typu jako kategorii.</span><span class="sxs-lookup"><span data-stu-id="9c72d-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="9c72d-135">Poniższy przykład aplikacji konsoli nie-hosta `LoggingConsoleApp.Program` tworzy rejestrator z jako kategorii.</span><span class="sxs-lookup"><span data-stu-id="9c72d-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="9c72d-136">W poniższych ASP.NET przykłady aplikacji Core i konsoli rejestrator jest używany `Information` do tworzenia dzienników z jako poziom.</span><span class="sxs-lookup"><span data-stu-id="9c72d-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="9c72d-137">Poziom *dziennika* wskazuje ważność zarejestrowanego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="9c72d-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="9c72d-138">[Poziomy](#log-level) i kategorie są [wyjaśnione](#log-category) bardziej szczegółowo w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="9c72d-139">Tworzenie dzienników w klasie Program</span><span class="sxs-lookup"><span data-stu-id="9c72d-139">Create logs in the Program class</span></span>

<span data-ttu-id="9c72d-140">Aby napisać dzienniki `Program` w klasie aplikacji ASP.NET Core, `ILogger` pobierz wystąpienie z DI po zbudowaniu hosta:</span><span class="sxs-lookup"><span data-stu-id="9c72d-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="9c72d-141">Rejestrowanie podczas budowy hosta nie jest bezpośrednio obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="9c72d-142">Można jednak użyć oddzielnego rejestratora.</span><span class="sxs-lookup"><span data-stu-id="9c72d-142">However, a separate logger can be used.</span></span> <span data-ttu-id="9c72d-143">W poniższym przykładzie [rejestrator Serilog](https://serilog.net/) jest `CreateHostBuilder`używany do logowania .</span><span class="sxs-lookup"><span data-stu-id="9c72d-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="9c72d-144">`AddSerilog`wykorzystuje konfigurację statyczną `Log.Logger`określoną w:</span><span class="sxs-lookup"><span data-stu-id="9c72d-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="9c72d-145">Tworzenie dzienników w klasie Uruchamiani</span><span class="sxs-lookup"><span data-stu-id="9c72d-145">Create logs in the Startup class</span></span>

<span data-ttu-id="9c72d-146">Aby zapisać dzienniki `Startup.Configure` w metodzie aplikacji ASP.NET Core, `ILogger` dołącz parametr do podpisu metody:</span><span class="sxs-lookup"><span data-stu-id="9c72d-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="9c72d-147">Zapisywanie dzienników przed zakończeniem konfiguracji `Startup.ConfigureServices` kontenera DI w metodzie nie jest obsługiwane:</span><span class="sxs-lookup"><span data-stu-id="9c72d-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="9c72d-148">Logger wtrysku do konstruktora `Startup` nie jest obsługiwany.</span><span class="sxs-lookup"><span data-stu-id="9c72d-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="9c72d-149">Wtrysk `Startup.ConfigureServices` rejestratora do podpisu metody nie jest obsługiwany</span><span class="sxs-lookup"><span data-stu-id="9c72d-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="9c72d-150">Powodem tego ograniczenia jest to, że rejestrowanie zależy od DI i konfiguracji, co z kolei zależy od DI.</span><span class="sxs-lookup"><span data-stu-id="9c72d-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="9c72d-151">Kontener DI nie jest skonfigurowany do zakończenia. `ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="9c72d-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="9c72d-152">Inicjowanie konstruktora rejestratora do `Startup` prac we wcześniejszych wersjach ASP.NET Core, ponieważ dla hosta sieci Web jest tworzony oddzielny kontener DI.</span><span class="sxs-lookup"><span data-stu-id="9c72d-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="9c72d-153">Aby uzyskać informacje o tym, dlaczego dla hosta ogólnego tworzony jest tylko jeden kontener, zobacz [anons zmiany podziału](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="9c72d-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="9c72d-154">Jeśli trzeba skonfigurować usługę, która `ILogger<T>`zależy od , nadal można to zrobić za pomocą wtrysku konstruktora lub pod warunkiem, że metoda fabryczna.</span><span class="sxs-lookup"><span data-stu-id="9c72d-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="9c72d-155">Podejście metody fabrycznej jest zalecane tylko wtedy, gdy nie ma innej opcji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="9c72d-156">Załóżmy na przykład, że musisz wypełnić właściwość usługą z DI:</span><span class="sxs-lookup"><span data-stu-id="9c72d-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="9c72d-157">Poprzedni wyróżniony kod `Func` jest, który uruchamia po raz pierwszy kontener `MyService`DI musi skonstruować wystąpienie .</span><span class="sxs-lookup"><span data-stu-id="9c72d-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="9c72d-158">W ten sposób można uzyskać dostęp do dowolnej z zarejestrowanych usług.</span><span class="sxs-lookup"><span data-stu-id="9c72d-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor-webassembly"></a><span data-ttu-id="9c72d-159">Tworzenie dzienników w blazor webassembly</span><span class="sxs-lookup"><span data-stu-id="9c72d-159">Create logs in Blazor WebAssembly</span></span>

<span data-ttu-id="9c72d-160">Skonfiguruj rejestrowanie w aplikacjach Blazor WebAssembly z właściwością `WebAssemblyHostBuilder.Logging` w: `Program.Main`</span><span class="sxs-lookup"><span data-stu-id="9c72d-160">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="9c72d-161">Właściwość `Logging` jest <xref:Microsoft.Extensions.Logging.ILoggingBuilder>typu, więc wszystkie metody <xref:Microsoft.Extensions.Logging.ILoggingBuilder> rozszerzenia dostępne `Logging`są również dostępne na .</span><span class="sxs-lookup"><span data-stu-id="9c72d-161">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="9c72d-162">Brak metod rejestratora asynchronii</span><span class="sxs-lookup"><span data-stu-id="9c72d-162">No asynchronous logger methods</span></span>

<span data-ttu-id="9c72d-163">Rejestrowanie powinno być tak szybkie, że nie jest warte kosztu wydajności kodu asynchroniowego.</span><span class="sxs-lookup"><span data-stu-id="9c72d-163">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="9c72d-164">Jeśli magazyn danych rejestrowania jest powolny, nie zapisuj do niego bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="9c72d-164">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="9c72d-165">Należy rozważyć zapisanie komunikatów dziennika do szybkiego magazynu początkowo, a następnie przenieść je do wolnego magazynu później.</span><span class="sxs-lookup"><span data-stu-id="9c72d-165">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="9c72d-166">Na przykład jeśli logujesz się do programu SQL Server, nie chcesz tego `Log` robić bezpośrednio `Log` w metodzie, ponieważ metody są synchroniczne.</span><span class="sxs-lookup"><span data-stu-id="9c72d-166">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="9c72d-167">Zamiast tego synchronicznie dodać komunikaty dziennika do kolejki w pamięci i mieć proces roboczy w tle wyciągnąć wiadomości z kolejki, aby wykonać asynchronicznie pracy wypychania danych do programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="9c72d-167">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="9c72d-168">Aby uzyskać więcej informacji, zobacz [ten](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problem z githubem.</span><span class="sxs-lookup"><span data-stu-id="9c72d-168">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="9c72d-169">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="9c72d-169">Configuration</span></span>

<span data-ttu-id="9c72d-170">Konfiguracja dostawcy rejestrowania jest dostarczana przez jednego lub więcej dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9c72d-170">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="9c72d-171">Formaty plików (INI, JSON i XML).</span><span class="sxs-lookup"><span data-stu-id="9c72d-171">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="9c72d-172">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="9c72d-172">Command-line arguments.</span></span>
* <span data-ttu-id="9c72d-173">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="9c72d-173">Environment variables.</span></span>
* <span data-ttu-id="9c72d-174">W pamięci obiektów .NET.</span><span class="sxs-lookup"><span data-stu-id="9c72d-174">In-memory .NET objects.</span></span>
* <span data-ttu-id="9c72d-175">Niezaszyfrowany magazyn [Menedżera tajnych.](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="9c72d-175">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="9c72d-176">Zaszyfrowany magazyn użytkowników, taki jak [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="9c72d-176">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="9c72d-177">Dostawcy niestandardowi (zainstalowane lub utworzone).</span><span class="sxs-lookup"><span data-stu-id="9c72d-177">Custom providers (installed or created).</span></span>

<span data-ttu-id="9c72d-178">Na przykład konfiguracja rejestrowania jest `Logging` często dostarczana przez sekcję plików ustawień aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-178">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="9c72d-179">W poniższym przykładzie przedstawiono zawartość typowych *appsettings. Plik Development.json:*</span><span class="sxs-lookup"><span data-stu-id="9c72d-179">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="9c72d-180">Właściwość `Logging` może `LogLevel` mieć i rejestrować właściwości dostawcy (konsola jest wyświetlana).</span><span class="sxs-lookup"><span data-stu-id="9c72d-180">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="9c72d-181">Właściwość `LogLevel` `Logging` w obszarze określa minimalny [poziom](#log-level) do dziennika dla wybranych kategorii.</span><span class="sxs-lookup"><span data-stu-id="9c72d-181">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="9c72d-182">W przykładzie `System` `Microsoft` i kategorii `Information` dziennika na poziomie, `Debug` a wszystkie inne dziennika na poziomie.</span><span class="sxs-lookup"><span data-stu-id="9c72d-182">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="9c72d-183">Inne właściwości `Logging` w obszarze określić dostawców rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-183">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="9c72d-184">Przykład jest dla dostawcy konsoli.</span><span class="sxs-lookup"><span data-stu-id="9c72d-184">The example is for the Console provider.</span></span> <span data-ttu-id="9c72d-185">Jeśli dostawca obsługuje [zakresy dziennika,](#log-scopes) `IncludeScopes` wskazuje, czy są one włączone.</span><span class="sxs-lookup"><span data-stu-id="9c72d-185">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="9c72d-186">Właściwość dostawcy (na przykład `Console` w przykładzie) może również określić `LogLevel` właściwość.</span><span class="sxs-lookup"><span data-stu-id="9c72d-186">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="9c72d-187">`LogLevel`pod dostawcą określa poziomy do zalogowania dla tego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="9c72d-187">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="9c72d-188">Jeśli poziomy są `Logging.{providername}.LogLevel`określone w , zastępują wszystko ustawione w `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="9c72d-188">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="9c72d-189">Interfejs API rejestrowania nie zawiera scenariusza, aby zmienić poziomy dziennika, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="9c72d-189">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="9c72d-190">Jednak niektórzy dostawcy konfiguracji są w stanie przeładować konfigurację, co ma natychmiastowy wpływ na konfigurację rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-190">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="9c72d-191">Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider), `CreateDefaultBuilder` który jest dodawany przez pliki ustawień odczytu, domyślnie ładuje konfigurację rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-191">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="9c72d-192">Jeśli konfiguracja zostanie zmieniona w kodzie, gdy aplikacja jest uruchomiona, aplikacja może wywołać [IConfigurationRoot.Reload,](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) aby zaktualizować konfigurację rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-192">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="9c72d-193">Aby uzyskać informacje na temat <xref:fundamentals/configuration/index>wdrażania dostawców konfiguracji, zobacz .</span><span class="sxs-lookup"><span data-stu-id="9c72d-193">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="9c72d-194">Przykładowe dane wyjściowe rejestrowania</span><span class="sxs-lookup"><span data-stu-id="9c72d-194">Sample logging output</span></span>

<span data-ttu-id="9c72d-195">Po wyświetleniu przykładowego kodu w poprzedniej sekcji dzienniki są wyświetlane w konsoli, gdy aplikacja jest uruchamiana z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="9c72d-195">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="9c72d-196">Oto przykład danych wyjściowych konsoli:</span><span class="sxs-lookup"><span data-stu-id="9c72d-196">Here's an example of console output:</span></span>

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

<span data-ttu-id="9c72d-197">Poprzednie dzienniki zostały wygenerowane przez wykonanie żądania HTTP `http://localhost:5000/api/todo/0`Get do przykładowej aplikacji o godzinie .</span><span class="sxs-lookup"><span data-stu-id="9c72d-197">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="9c72d-198">Oto przykład tych samych dzienników, które pojawiają się w oknie debugowania po uruchomieniu przykładowej aplikacji w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="9c72d-198">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="9c72d-199">Dzienniki, które są `ILogger` tworzone przez wywołania wyświetlane w poprzedniej sekcji zaczynają się od "TodoApiSample".</span><span class="sxs-lookup"><span data-stu-id="9c72d-199">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="9c72d-200">Dzienniki, które zaczynają się od kategorii "Microsoft" pochodzą z ASP.NET kodu frameworka Core.</span><span class="sxs-lookup"><span data-stu-id="9c72d-200">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="9c72d-201">ASP.NET Core i kod aplikacji są przy użyciu tego samego interfejsu API rejestrowania i dostawców.</span><span class="sxs-lookup"><span data-stu-id="9c72d-201">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="9c72d-202">Poniżej dalsza część artykułu Wyjaśniono niektóre szczegóły i opcje rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-202">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="9c72d-203">Pakiety NuGet</span><span class="sxs-lookup"><span data-stu-id="9c72d-203">NuGet packages</span></span>

<span data-ttu-id="9c72d-204">`ILogger` Interfejsy `ILoggerFactory` i interfejsy znajdują się w witrynie [Microsoft.Extensions.Logging.Abstractions,](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a domyślne implementacje dla nich znajdują się w [witrynie Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="9c72d-204">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="9c72d-205">Kategoria dziennika</span><span class="sxs-lookup"><span data-stu-id="9c72d-205">Log category</span></span>

<span data-ttu-id="9c72d-206">Podczas `ILogger` tworzenia obiektu określana jest dla niego *kategoria.*</span><span class="sxs-lookup"><span data-stu-id="9c72d-206">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="9c72d-207">Ta kategoria jest dołączona do każdej wiadomości `ILogger`dziennika utworzonej przez to wystąpienie .</span><span class="sxs-lookup"><span data-stu-id="9c72d-207">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="9c72d-208">Kategoria może być dowolny ciąg, ale konwencja jest użycie nazwy klasy, takich jak "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="9c72d-208">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="9c72d-209">Użyj, `ILogger<T>` aby `ILogger` uzyskać wystąpienie, które używa `T` w pełni kwalifikowanej nazwy typu jako kategorii:</span><span class="sxs-lookup"><span data-stu-id="9c72d-209">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="9c72d-210">Aby jawnie określić kategorię, zadzwoń: `ILoggerFactory.CreateLogger`</span><span class="sxs-lookup"><span data-stu-id="9c72d-210">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="9c72d-211">`ILogger<T>`jest odpowiednikiem `CreateLogger` wywołania z w `T`pełni kwalifikowaną nazwą typu .</span><span class="sxs-lookup"><span data-stu-id="9c72d-211">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="9c72d-212">Poziom dziennika</span><span class="sxs-lookup"><span data-stu-id="9c72d-212">Log level</span></span>

<span data-ttu-id="9c72d-213">Każdy dziennik określa <xref:Microsoft.Extensions.Logging.LogLevel> wartość.</span><span class="sxs-lookup"><span data-stu-id="9c72d-213">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="9c72d-214">Poziom dziennika wskazuje ważność lub ważność.</span><span class="sxs-lookup"><span data-stu-id="9c72d-214">The log level indicates the severity or importance.</span></span> <span data-ttu-id="9c72d-215">Na przykład można napisać `Information` dziennik, gdy metoda kończy `Warning` się normalnie i dziennika, gdy metoda zwraca kod stanu *404 Nie znaleziono.*</span><span class="sxs-lookup"><span data-stu-id="9c72d-215">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="9c72d-216">Następujący kod `Information` tworzy `Warning` i rejestruje:</span><span class="sxs-lookup"><span data-stu-id="9c72d-216">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="9c72d-217">W poprzednim kodzie pierwszym parametrem jest [identyfikator zdarzenia Log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="9c72d-217">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="9c72d-218">Drugi parametr jest szablonem wiadomości z symbolami zastępczymi dla wartości argumentów dostarczonych przez pozostałe parametry metody.</span><span class="sxs-lookup"><span data-stu-id="9c72d-218">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="9c72d-219">Parametry metody są wyjaśnione w [sekcji szablonu wiadomości](#log-message-template) w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-219">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="9c72d-220">Metody dziennika, które zawierają poziom w nazwie `LogInformation` `LogWarning`metody (na przykład i ) są [metody rozszerzenia dla ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="9c72d-220">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="9c72d-221">Te metody `Log` wywołać metodę, która przyjmuje `LogLevel` parametr.</span><span class="sxs-lookup"><span data-stu-id="9c72d-221">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="9c72d-222">Można wywołać `Log` metodę bezpośrednio, a nie jedną z tych metod rozszerzenia, ale składnia jest stosunkowo skomplikowana.</span><span class="sxs-lookup"><span data-stu-id="9c72d-222">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="9c72d-223">Aby uzyskać więcej <xref:Microsoft.Extensions.Logging.ILogger> informacji, zobacz kod [źródłowy rozszerzeń rejestratora](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="9c72d-223">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="9c72d-224">ASP.NET Core definiuje następujące poziomy dziennika, uporządkowane w tym miejscu od najniższej do najwyższej ważności.</span><span class="sxs-lookup"><span data-stu-id="9c72d-224">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="9c72d-225">Ślad = 0</span><span class="sxs-lookup"><span data-stu-id="9c72d-225">Trace = 0</span></span>

  <span data-ttu-id="9c72d-226">Aby uzyskać informacje, które są zazwyczaj cenne tylko do debugowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-226">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="9c72d-227">Te komunikaty mogą zawierać poufne dane aplikacji, a więc nie powinny być włączone w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="9c72d-227">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="9c72d-228">*Domyślnie wyłączone.*</span><span class="sxs-lookup"><span data-stu-id="9c72d-228">*Disabled by default.*</span></span>

* <span data-ttu-id="9c72d-229">Debugowanie = 1</span><span class="sxs-lookup"><span data-stu-id="9c72d-229">Debug = 1</span></span>

  <span data-ttu-id="9c72d-230">Aby uzyskać informacje, które mogą być przydatne w rozwoju i debugowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-230">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="9c72d-231">Przykład: `Entering method Configure with flag set to true.` `Debug` Włącz logi poziomu w produkcji tylko podczas rozwiązywania problemów, ze względu na dużą liczbę dzienników.</span><span class="sxs-lookup"><span data-stu-id="9c72d-231">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="9c72d-232">Informacje = 2</span><span class="sxs-lookup"><span data-stu-id="9c72d-232">Information = 2</span></span>

  <span data-ttu-id="9c72d-233">Do śledzenia ogólnego przepływu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-233">For tracking the general flow of the app.</span></span> <span data-ttu-id="9c72d-234">Te dzienniki zazwyczaj mają pewną wartość długoterminową.</span><span class="sxs-lookup"><span data-stu-id="9c72d-234">These logs typically have some long-term value.</span></span> <span data-ttu-id="9c72d-235">Przykład: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="9c72d-235">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="9c72d-236">Ostrzeżenie = 3</span><span class="sxs-lookup"><span data-stu-id="9c72d-236">Warning = 3</span></span>

  <span data-ttu-id="9c72d-237">W przypadku nieprawidłowych lub nieoczekiwanych zdarzeń w przepływie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-237">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="9c72d-238">Mogą to być błędy lub inne warunki, które nie powodują zatrzymania aplikacji, ale mogą wymagać zbadania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-238">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="9c72d-239">Obsługiwane wyjątki są wspólne miejsce `Warning` do korzystania z poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="9c72d-239">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="9c72d-240">Przykład: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="9c72d-240">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="9c72d-241">Błąd = 4</span><span class="sxs-lookup"><span data-stu-id="9c72d-241">Error = 4</span></span>

  <span data-ttu-id="9c72d-242">Dla błędów i wyjątków, które nie mogą być obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-242">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="9c72d-243">Te komunikaty wskazują na błąd w bieżącym działaniu lub operacji (takich jak bieżące żądanie HTTP), a nie błąd całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-243">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="9c72d-244">Przykładowy komunikat dziennika:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="9c72d-244">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="9c72d-245">Krytyczny = 5</span><span class="sxs-lookup"><span data-stu-id="9c72d-245">Critical = 5</span></span>

  <span data-ttu-id="9c72d-246">W przypadku awarii, które wymagają natychmiastowej uwagi.</span><span class="sxs-lookup"><span data-stu-id="9c72d-246">For failures that require immediate attention.</span></span> <span data-ttu-id="9c72d-247">Przykłady: scenariusze utraty danych, brak miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="9c72d-247">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="9c72d-248">Użyj poziomu dziennika, aby kontrolować, ile danych wyjściowych dziennika jest zapisywany na określonym nośniku pamięci lub oknie wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-248">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="9c72d-249">Przykład:</span><span class="sxs-lookup"><span data-stu-id="9c72d-249">For example:</span></span>

* <span data-ttu-id="9c72d-250">W produkcji:</span><span class="sxs-lookup"><span data-stu-id="9c72d-250">In production:</span></span>
  * <span data-ttu-id="9c72d-251">Rejestrowanie na `Trace` `Information` poziomach za pośrednictwem tworzy dużą ilość szczegółowych komunikatów dziennika.</span><span class="sxs-lookup"><span data-stu-id="9c72d-251">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="9c72d-252">Aby kontrolować koszty i nie przekraczać `Information` limitów magazynowania danych, rejestruj `Trace` komunikaty poziomu do magazynu danych o dużej objętości i niskich kosztach.</span><span class="sxs-lookup"><span data-stu-id="9c72d-252">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="9c72d-253">Rejestrowanie `Warning` na `Critical` poziomach za pośrednictwem zazwyczaj generuje mniej, mniejsze komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="9c72d-253">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="9c72d-254">W związku z tym koszty i limity magazynowania zwykle nie są problemem, co skutkuje większą elastycznością wyboru magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="9c72d-254">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="9c72d-255">Podczas rozwoju:</span><span class="sxs-lookup"><span data-stu-id="9c72d-255">During development:</span></span>
  * <span data-ttu-id="9c72d-256">Zaloguj `Warning` `Critical` się za pośrednictwem wiadomości do konsoli.</span><span class="sxs-lookup"><span data-stu-id="9c72d-256">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="9c72d-257">Dodaj `Trace` `Information` za pośrednictwem wiadomości podczas rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="9c72d-257">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="9c72d-258">[Sekcja filtrowania dziennika](#log-filtering) w dalszej części tego artykułu wyjaśnia, jak kontrolować poziomy dziennika, które obsługuje dostawca.</span><span class="sxs-lookup"><span data-stu-id="9c72d-258">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="9c72d-259">ASP.NET Core zapisuje dzienniki dla zdarzeń struktury.</span><span class="sxs-lookup"><span data-stu-id="9c72d-259">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="9c72d-260">Przykłady dziennika wcześniej w tym artykule wykluczone dzienniki poniżej `Information` poziomu, więc nie `Debug` lub `Trace` poziom dzienniki zostały utworzone.</span><span class="sxs-lookup"><span data-stu-id="9c72d-260">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="9c72d-261">Oto przykład dzienników konsoli wyprodukowanych przez uruchomienie przykładowej `Debug` aplikacji skonfigurowanej do pokazywalek dzienników:</span><span class="sxs-lookup"><span data-stu-id="9c72d-261">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="9c72d-262">Identyfikator zdarzenia dziennika</span><span class="sxs-lookup"><span data-stu-id="9c72d-262">Log event ID</span></span>

<span data-ttu-id="9c72d-263">Każdy dziennik może określić *identyfikator zdarzenia*.</span><span class="sxs-lookup"><span data-stu-id="9c72d-263">Each log can specify an *event ID*.</span></span> <span data-ttu-id="9c72d-264">Przykładowa aplikacja robi to przy `LoggingEvents` użyciu klasy zdefiniowanej lokalnie:</span><span class="sxs-lookup"><span data-stu-id="9c72d-264">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="9c72d-265">Identyfikator zdarzenia kojarzy zestaw zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="9c72d-265">An event ID associates a set of events.</span></span> <span data-ttu-id="9c72d-266">Na przykład wszystkie dzienniki związane z wyświetlaniem listy elementów na stronie może być 1001.</span><span class="sxs-lookup"><span data-stu-id="9c72d-266">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="9c72d-267">Dostawca rejestrowania może przechowywać identyfikator zdarzenia w polu identyfikator, w komunikacie rejestrowania lub w ogóle.</span><span class="sxs-lookup"><span data-stu-id="9c72d-267">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="9c72d-268">Dostawca debugowania nie pokazuje identyfikatorów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="9c72d-268">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="9c72d-269">Dostawca konsoli wyświetla identyfikatory zdarzeń w nawiasach po kategorii:</span><span class="sxs-lookup"><span data-stu-id="9c72d-269">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="9c72d-270">Szablon komunikatu dziennika</span><span class="sxs-lookup"><span data-stu-id="9c72d-270">Log message template</span></span>

<span data-ttu-id="9c72d-271">Każdy dziennik określa szablon wiadomości.</span><span class="sxs-lookup"><span data-stu-id="9c72d-271">Each log specifies a message template.</span></span> <span data-ttu-id="9c72d-272">Szablon wiadomości może zawierać symbole zastępcze, dla których są dostarczane argumenty.</span><span class="sxs-lookup"><span data-stu-id="9c72d-272">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="9c72d-273">Użyj nazw symboli zastępczych, a nie liczb.</span><span class="sxs-lookup"><span data-stu-id="9c72d-273">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="9c72d-274">Kolejność symboli zastępczych, a nie ich nazw, określa, które parametry są używane do dostarczania ich wartości.</span><span class="sxs-lookup"><span data-stu-id="9c72d-274">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="9c72d-275">W poniższym kodzie należy zauważyć, że nazwy parametrów są niesekwencjonowane w szablonie wiadomości:</span><span class="sxs-lookup"><span data-stu-id="9c72d-275">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="9c72d-276">Ten kod tworzy komunikat dziennika z wartościami parametrów w sekwencji:</span><span class="sxs-lookup"><span data-stu-id="9c72d-276">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="9c72d-277">Struktura rejestrowania działa w ten sposób, dzięki czemu dostawcy rejestrowania mogą zaimplementować [rejestrowanie semantyczne, znane również jako rejestrowanie strukturalne.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)</span><span class="sxs-lookup"><span data-stu-id="9c72d-277">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="9c72d-278">Same argumenty są przekazywane do systemu rejestrowania, a nie tylko do sformatowanego szablonu wiadomości.</span><span class="sxs-lookup"><span data-stu-id="9c72d-278">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="9c72d-279">Te informacje umożliwiają dostawcom rejestrowania przechowywanie wartości parametrów jako pól.</span><span class="sxs-lookup"><span data-stu-id="9c72d-279">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="9c72d-280">Załóżmy na przykład, że wywołania metody rejestratora wyglądają następująco:</span><span class="sxs-lookup"><span data-stu-id="9c72d-280">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="9c72d-281">Jeśli wysyłasz dzienniki do usługi Azure Table Storage, `ID` każda `RequestTime` jednostka tabeli platformy Azure może mieć i właściwości, co upraszcza zapytania dotyczące danych dziennika.</span><span class="sxs-lookup"><span data-stu-id="9c72d-281">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="9c72d-282">Kwerenda może znaleźć wszystkie dzienniki w określonym `RequestTime` zakresie bez analizowania czasu poza wiadomością tekstową.</span><span class="sxs-lookup"><span data-stu-id="9c72d-282">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="9c72d-283">Rejestrowanie wyjątków</span><span class="sxs-lookup"><span data-stu-id="9c72d-283">Logging exceptions</span></span>

<span data-ttu-id="9c72d-284">Metody rejestratora mają przeciążenia, które umożliwiają przekazywanie w wyjątku, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="9c72d-284">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="9c72d-285">Różni dostawcy obsługują informacje o wyjątkach na różne sposoby.</span><span class="sxs-lookup"><span data-stu-id="9c72d-285">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="9c72d-286">Oto przykład danych wyjściowych dostawcy debugowania z kodu pokazanego powyżej.</span><span class="sxs-lookup"><span data-stu-id="9c72d-286">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="9c72d-287">Filtrowanie dzienników</span><span class="sxs-lookup"><span data-stu-id="9c72d-287">Log filtering</span></span>

<span data-ttu-id="9c72d-288">Można określić minimalny poziom dziennika dla określonego dostawcy i kategorii lub dla wszystkich dostawców lub wszystkich kategorii.</span><span class="sxs-lookup"><span data-stu-id="9c72d-288">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="9c72d-289">Wszelkie dzienniki poniżej minimalnego poziomu nie są przekazywane do tego dostawcy, więc nie są wyświetlane ani przechowywane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-289">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="9c72d-290">Aby pominąć wszystkie `LogLevel.None` dzienniki, należy określić jako minimalny poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="9c72d-290">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="9c72d-291">Wartość całkowita `LogLevel.None` wynosi 6, która jest `LogLevel.Critical` wyższa niż (5).</span><span class="sxs-lookup"><span data-stu-id="9c72d-291">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="9c72d-292">Tworzenie reguł filtrowania w konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9c72d-292">Create filter rules in configuration</span></span>

<span data-ttu-id="9c72d-293">Kod szablonu `CreateDefaultBuilder` projektu wywołuje skonfigurowanie rejestrowania dla dostawców konsoli, debugowania i źródła zdarzeń (ASP.NET Core 2.2 lub nowszych).</span><span class="sxs-lookup"><span data-stu-id="9c72d-293">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="9c72d-294">Metoda `CreateDefaultBuilder` konfiguruje rejestrowanie w celu `Logging` wyszukywania konfiguracji w sekcji, jak wyjaśniono [wcześniej w tym artykule](#configuration).</span><span class="sxs-lookup"><span data-stu-id="9c72d-294">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="9c72d-295">Dane konfiguracyjne określają minimalne poziomy dziennika według dostawcy i kategorii, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="9c72d-295">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="9c72d-296">Ten JSON tworzy sześć reguł filtrowania: jeden dla dostawcy debugowania, cztery dla dostawcy konsoli i jeden dla wszystkich dostawców.</span><span class="sxs-lookup"><span data-stu-id="9c72d-296">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="9c72d-297">Pojedyncza reguła jest wybierana `ILogger` dla każdego dostawcy podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-297">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="9c72d-298">Reguły filtrowania w kodzie</span><span class="sxs-lookup"><span data-stu-id="9c72d-298">Filter rules in code</span></span>

<span data-ttu-id="9c72d-299">W poniższym przykładzie pokazano, jak zarejestrować reguły filtrowania w kodzie:</span><span class="sxs-lookup"><span data-stu-id="9c72d-299">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="9c72d-300">Drugi `AddFilter` określa dostawcę debugowania przy użyciu jego nazwy typu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-300">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="9c72d-301">Pierwszy `AddFilter` dotyczy wszystkich dostawców, ponieważ nie określa typu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="9c72d-301">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="9c72d-302">Jak stosowane są reguły filtrowania</span><span class="sxs-lookup"><span data-stu-id="9c72d-302">How filtering rules are applied</span></span>

<span data-ttu-id="9c72d-303">Dane konfiguracji i `AddFilter` kod pokazany w poprzednich przykładach tworzą reguły pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="9c72d-303">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="9c72d-304">Pierwsze sześć pochodzą z przykładu konfiguracji i dwa ostatnie pochodzą z przykładu kodu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-304">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="9c72d-305">Liczba</span><span class="sxs-lookup"><span data-stu-id="9c72d-305">Number</span></span> | <span data-ttu-id="9c72d-306">Dostawca</span><span class="sxs-lookup"><span data-stu-id="9c72d-306">Provider</span></span>      | <span data-ttu-id="9c72d-307">Kategorie, które zaczynają się od ...</span><span class="sxs-lookup"><span data-stu-id="9c72d-307">Categories that begin with ...</span></span>          | <span data-ttu-id="9c72d-308">Minimalny poziom dziennika</span><span class="sxs-lookup"><span data-stu-id="9c72d-308">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="9c72d-309">1</span><span class="sxs-lookup"><span data-stu-id="9c72d-309">1</span></span>      | <span data-ttu-id="9c72d-310">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="9c72d-310">Debug</span></span>         | <span data-ttu-id="9c72d-311">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="9c72d-311">All categories</span></span>                          | <span data-ttu-id="9c72d-312">Informacje</span><span class="sxs-lookup"><span data-stu-id="9c72d-312">Information</span></span>       |
| <span data-ttu-id="9c72d-313">2</span><span class="sxs-lookup"><span data-stu-id="9c72d-313">2</span></span>      | <span data-ttu-id="9c72d-314">Konsola</span><span class="sxs-lookup"><span data-stu-id="9c72d-314">Console</span></span>       | <span data-ttu-id="9c72d-315">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="9c72d-315">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="9c72d-316">Ostrzeżenie</span><span class="sxs-lookup"><span data-stu-id="9c72d-316">Warning</span></span>           |
| <span data-ttu-id="9c72d-317">3</span><span class="sxs-lookup"><span data-stu-id="9c72d-317">3</span></span>      | <span data-ttu-id="9c72d-318">Konsola</span><span class="sxs-lookup"><span data-stu-id="9c72d-318">Console</span></span>       | <span data-ttu-id="9c72d-319">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="9c72d-319">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="9c72d-320">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="9c72d-320">Debug</span></span>             |
| <span data-ttu-id="9c72d-321">4</span><span class="sxs-lookup"><span data-stu-id="9c72d-321">4</span></span>      | <span data-ttu-id="9c72d-322">Konsola</span><span class="sxs-lookup"><span data-stu-id="9c72d-322">Console</span></span>       | <span data-ttu-id="9c72d-323">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="9c72d-323">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="9c72d-324">Błąd</span><span class="sxs-lookup"><span data-stu-id="9c72d-324">Error</span></span>             |
| <span data-ttu-id="9c72d-325">5</span><span class="sxs-lookup"><span data-stu-id="9c72d-325">5</span></span>      | <span data-ttu-id="9c72d-326">Konsola</span><span class="sxs-lookup"><span data-stu-id="9c72d-326">Console</span></span>       | <span data-ttu-id="9c72d-327">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="9c72d-327">All categories</span></span>                          | <span data-ttu-id="9c72d-328">Informacje</span><span class="sxs-lookup"><span data-stu-id="9c72d-328">Information</span></span>       |
| <span data-ttu-id="9c72d-329">6</span><span class="sxs-lookup"><span data-stu-id="9c72d-329">6</span></span>      | <span data-ttu-id="9c72d-330">Wszyscy dostawcy</span><span class="sxs-lookup"><span data-stu-id="9c72d-330">All providers</span></span> | <span data-ttu-id="9c72d-331">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="9c72d-331">All categories</span></span>                          | <span data-ttu-id="9c72d-332">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="9c72d-332">Debug</span></span>             |
| <span data-ttu-id="9c72d-333">7</span><span class="sxs-lookup"><span data-stu-id="9c72d-333">7</span></span>      | <span data-ttu-id="9c72d-334">Wszyscy dostawcy</span><span class="sxs-lookup"><span data-stu-id="9c72d-334">All providers</span></span> | <span data-ttu-id="9c72d-335">System</span><span class="sxs-lookup"><span data-stu-id="9c72d-335">System</span></span>                                  | <span data-ttu-id="9c72d-336">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="9c72d-336">Debug</span></span>             |
| <span data-ttu-id="9c72d-337">8</span><span class="sxs-lookup"><span data-stu-id="9c72d-337">8</span></span>      | <span data-ttu-id="9c72d-338">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="9c72d-338">Debug</span></span>         | <span data-ttu-id="9c72d-339">Microsoft</span><span class="sxs-lookup"><span data-stu-id="9c72d-339">Microsoft</span></span>                               | <span data-ttu-id="9c72d-340">Ślad</span><span class="sxs-lookup"><span data-stu-id="9c72d-340">Trace</span></span>             |

<span data-ttu-id="9c72d-341">Po `ILogger` utworzeniu `ILoggerFactory` obiektu obiekt wybiera jedną regułę na dostawcę, aby zastosować do tego rejestratora.</span><span class="sxs-lookup"><span data-stu-id="9c72d-341">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="9c72d-342">Wszystkie wiadomości napisane `ILogger` przez wystąpienie są filtrowane na podstawie wybranych reguł.</span><span class="sxs-lookup"><span data-stu-id="9c72d-342">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="9c72d-343">Najbardziej specyficzna reguła możliwa dla każdego dostawcy i pary kategorii jest wybierana z dostępnych reguł.</span><span class="sxs-lookup"><span data-stu-id="9c72d-343">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="9c72d-344">Następujący algorytm jest używany dla `ILogger` każdego dostawcy, gdy jest tworzony dla danej kategorii:</span><span class="sxs-lookup"><span data-stu-id="9c72d-344">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="9c72d-345">Wybierz wszystkie reguły, które pasują do dostawcy lub jego aliasu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-345">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="9c72d-346">Jeśli nie zostanie znaleziony żaden mecz, wybierz wszystkie reguły z pustym dostawcą.</span><span class="sxs-lookup"><span data-stu-id="9c72d-346">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="9c72d-347">Z wyniku poprzedniego kroku wybierz reguły z najdłuższym pasującym prefiksem kategorii.</span><span class="sxs-lookup"><span data-stu-id="9c72d-347">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="9c72d-348">Jeśli nie zostanie znalezione żadne dopasowanie, wybierz wszystkie reguły, które nie określają kategorii.</span><span class="sxs-lookup"><span data-stu-id="9c72d-348">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="9c72d-349">Jeśli zaznaczono wiele reguł, weź **ostatni.**</span><span class="sxs-lookup"><span data-stu-id="9c72d-349">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="9c72d-350">Jeśli nie wybrano żadnych reguł, użyj . `MinimumLevel`</span><span class="sxs-lookup"><span data-stu-id="9c72d-350">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="9c72d-351">Załóżmy, że na powyższej `ILogger` liście reguł zostanie utworzony obiekt dla kategorii "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="9c72d-351">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="9c72d-352">W przypadku dostawcy debugowania obowiązują reguły 1, 6 i 8.</span><span class="sxs-lookup"><span data-stu-id="9c72d-352">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="9c72d-353">Reguła 8 jest najbardziej szczegółowa, więc to jest ta wybrana.</span><span class="sxs-lookup"><span data-stu-id="9c72d-353">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="9c72d-354">W przypadku dostawcy konsoli obowiązują zasady 3, 4, 5 i 6.</span><span class="sxs-lookup"><span data-stu-id="9c72d-354">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="9c72d-355">Zasada 3 jest najbardziej szczegółowa.</span><span class="sxs-lookup"><span data-stu-id="9c72d-355">Rule 3 is most specific.</span></span>

<span data-ttu-id="9c72d-356">Wynikowe `ILogger` wystąpienie wysyła `Trace` dzienniki poziomu i powyżej do dostawcy debugowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-356">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="9c72d-357">Dzienniki `Debug` poziomu i powyżej są wysyłane do dostawcy konsoli.</span><span class="sxs-lookup"><span data-stu-id="9c72d-357">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="9c72d-358">Aliasy dostawców</span><span class="sxs-lookup"><span data-stu-id="9c72d-358">Provider aliases</span></span>

<span data-ttu-id="9c72d-359">Każdy dostawca definiuje *alias,* który może być używany w konfiguracji zamiast w pełni kwalifikowanej nazwy typu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-359">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="9c72d-360">W przypadku wbudowanych dostawców użyj następujących aliasów:</span><span class="sxs-lookup"><span data-stu-id="9c72d-360">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="9c72d-361">Konsola</span><span class="sxs-lookup"><span data-stu-id="9c72d-361">Console</span></span>
* <span data-ttu-id="9c72d-362">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="9c72d-362">Debug</span></span>
* <span data-ttu-id="9c72d-363">EventSource</span><span class="sxs-lookup"><span data-stu-id="9c72d-363">EventSource</span></span>
* <span data-ttu-id="9c72d-364">Eventlog</span><span class="sxs-lookup"><span data-stu-id="9c72d-364">EventLog</span></span>
* <span data-ttu-id="9c72d-365">TraceSource</span><span class="sxs-lookup"><span data-stu-id="9c72d-365">TraceSource</span></span>
* <span data-ttu-id="9c72d-366">Plik usługi AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="9c72d-366">AzureAppServicesFile</span></span>
* <span data-ttu-id="9c72d-367">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="9c72d-367">AzureAppServicesBlob</span></span>
* <span data-ttu-id="9c72d-368">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="9c72d-368">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="9c72d-369">Domyślny poziom minimalny</span><span class="sxs-lookup"><span data-stu-id="9c72d-369">Default minimum level</span></span>

<span data-ttu-id="9c72d-370">Istnieje minimalne ustawienie poziomu, które staje się skuteczne tylko wtedy, gdy żadne reguły z konfiguracji lub kodu nie mają zastosowania do danego dostawcy i kategorii.</span><span class="sxs-lookup"><span data-stu-id="9c72d-370">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="9c72d-371">W poniższym przykładzie pokazano, jak ustawić minimalny poziom:</span><span class="sxs-lookup"><span data-stu-id="9c72d-371">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="9c72d-372">Jeśli nie ustawisz jawnie minimalnego poziomu, `Information`wartością `Trace` domyślną jest , co oznacza, że i `Debug` dzienniki są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-372">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="9c72d-373">Funkcje filtrowania</span><span class="sxs-lookup"><span data-stu-id="9c72d-373">Filter functions</span></span>

<span data-ttu-id="9c72d-374">Funkcja filtrowania jest wywoływana dla wszystkich dostawców i kategorii, które nie mają reguł przypisanych do nich przez konfigurację lub kod.</span><span class="sxs-lookup"><span data-stu-id="9c72d-374">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="9c72d-375">Kod w funkcji ma dostęp do typu dostawcy, kategorii i poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="9c72d-375">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="9c72d-376">Przykład:</span><span class="sxs-lookup"><span data-stu-id="9c72d-376">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="9c72d-377">Kategorie i poziomy systemu</span><span class="sxs-lookup"><span data-stu-id="9c72d-377">System categories and levels</span></span>

<span data-ttu-id="9c72d-378">Oto kilka kategorii używanych przez ASP.NET Core i Entity Framework Core, z uwagami na temat tego, jakich dzienników można się po nich spodziewać:</span><span class="sxs-lookup"><span data-stu-id="9c72d-378">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="9c72d-379">Kategoria</span><span class="sxs-lookup"><span data-stu-id="9c72d-379">Category</span></span>                            | <span data-ttu-id="9c72d-380">Uwagi</span><span class="sxs-lookup"><span data-stu-id="9c72d-380">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="9c72d-381">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="9c72d-381">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="9c72d-382">Diagnostyka ASP.NET podstawowa.</span><span class="sxs-lookup"><span data-stu-id="9c72d-382">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="9c72d-383">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="9c72d-383">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="9c72d-384">Które klucze zostały uznane, znalezione i używane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-384">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="9c72d-385">Filtrowanie pliku Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="9c72d-385">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="9c72d-386">Hosty dozwolone.</span><span class="sxs-lookup"><span data-stu-id="9c72d-386">Hosts allowed.</span></span> |
| <span data-ttu-id="9c72d-387">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="9c72d-387">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="9c72d-388">Jak długo trwa wypełnianie żądań HTTP i o której godzinie rozpoczęto.</span><span class="sxs-lookup"><span data-stu-id="9c72d-388">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="9c72d-389">Które zestawy uruchamiania hostingu zostały załadowane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-389">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="9c72d-390">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="9c72d-390">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="9c72d-391">Diagnostyka MVC i Razor.</span><span class="sxs-lookup"><span data-stu-id="9c72d-391">MVC and Razor diagnostics.</span></span> <span data-ttu-id="9c72d-392">Powiązanie modelu, wykonanie filtru, kompilacja widoku, wybór akcji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-392">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="9c72d-393">Marszruta Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="9c72d-393">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="9c72d-394">Informacje o dopasowaniu trasy.</span><span class="sxs-lookup"><span data-stu-id="9c72d-394">Route matching information.</span></span> |
| <span data-ttu-id="9c72d-395">Serwer Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="9c72d-395">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="9c72d-396">Połączenie rozpoczyna, zatrzymuje i utrzymuje żywe odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9c72d-396">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="9c72d-397">Informacje o certyfikacie HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9c72d-397">HTTPS certificate information.</span></span> |
| <span data-ttu-id="9c72d-398">Plikami Microsoft.AspNetCore.Static</span><span class="sxs-lookup"><span data-stu-id="9c72d-398">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="9c72d-399">Pliki obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-399">Files served.</span></span> |
| <span data-ttu-id="9c72d-400">Microsoft.EntityFrameCore</span><span class="sxs-lookup"><span data-stu-id="9c72d-400">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="9c72d-401">Diagnostyka rdzenia podstawowej struktury jednostki ogólne.</span><span class="sxs-lookup"><span data-stu-id="9c72d-401">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="9c72d-402">Aktywność i konfiguracja bazy danych, wykrywanie zmian, migracje.</span><span class="sxs-lookup"><span data-stu-id="9c72d-402">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="9c72d-403">Zakresy dzienników</span><span class="sxs-lookup"><span data-stu-id="9c72d-403">Log scopes</span></span>

 <span data-ttu-id="9c72d-404">*Zakres* można grupować zestaw operacji logicznych.</span><span class="sxs-lookup"><span data-stu-id="9c72d-404">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="9c72d-405">To grupowanie może służyć do dołączania tych samych danych do każdego dziennika, który jest tworzony jako część zestawu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-405">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="9c72d-406">Na przykład każdy dziennik utworzony w ramach przetwarzania transakcji może zawierać identyfikator transakcji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-406">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="9c72d-407">Zakres jest `IDisposable` typem, który jest <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> zwracany przez metodę i trwa do momentu jego usunięcia.</span><span class="sxs-lookup"><span data-stu-id="9c72d-407">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="9c72d-408">Użyj zakresu przez zawijania wywołań rejestratora `using` w bloku:</span><span class="sxs-lookup"><span data-stu-id="9c72d-408">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="9c72d-409">Poniższy kod umożliwia zakresy dla dostawcy konsoli:</span><span class="sxs-lookup"><span data-stu-id="9c72d-409">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="9c72d-410">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="9c72d-410">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="9c72d-411">Konfigurowanie `IncludeScopes` opcji rejestratora konsoli jest wymagane, aby włączyć rejestrowanie oparte na zakresie.</span><span class="sxs-lookup"><span data-stu-id="9c72d-411">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="9c72d-412">Aby uzyskać informacje na temat konfiguracji, zobacz [sekcję Konfiguracja.](#configuration)</span><span class="sxs-lookup"><span data-stu-id="9c72d-412">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="9c72d-413">Każdy komunikat dziennika zawiera informacje o zakresie:</span><span class="sxs-lookup"><span data-stu-id="9c72d-413">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="9c72d-414">Wbudowane dostawcy rejestrowania</span><span class="sxs-lookup"><span data-stu-id="9c72d-414">Built-in logging providers</span></span>

<span data-ttu-id="9c72d-415">ASP.NET Core wysyła następujących dostawców:</span><span class="sxs-lookup"><span data-stu-id="9c72d-415">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="9c72d-416">Konsola</span><span class="sxs-lookup"><span data-stu-id="9c72d-416">Console</span></span>](#console-provider)
* [<span data-ttu-id="9c72d-417">Debugowania</span><span class="sxs-lookup"><span data-stu-id="9c72d-417">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="9c72d-418">Eventsource</span><span class="sxs-lookup"><span data-stu-id="9c72d-418">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="9c72d-419">Eventlog</span><span class="sxs-lookup"><span data-stu-id="9c72d-419">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="9c72d-420">TraceSource</span><span class="sxs-lookup"><span data-stu-id="9c72d-420">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="9c72d-421">Plik usługi AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="9c72d-421">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="9c72d-422">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="9c72d-422">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="9c72d-423">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="9c72d-423">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="9c72d-424">Aby uzyskać informacje na temat rejestrowania stdout i debugowania za pomocą ASP.NET Core Module, zobacz <xref:test/troubleshoot-azure-iis> i <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="9c72d-424">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="9c72d-425">Dostawca konsoli</span><span class="sxs-lookup"><span data-stu-id="9c72d-425">Console provider</span></span>

<span data-ttu-id="9c72d-426">Pakiet dostawcy [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) wysyła dane wyjściowe dziennika do konsoli.</span><span class="sxs-lookup"><span data-stu-id="9c72d-426">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="9c72d-427">Aby wyświetlić dane wyjściowe rejestrowania konsoli, otwórz wiersz polecenia w folderze projektu i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9c72d-427">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="9c72d-428">Dostawca debugowania</span><span class="sxs-lookup"><span data-stu-id="9c72d-428">Debug provider</span></span>

<span data-ttu-id="9c72d-429">Pakiet dostawcy [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje dane wyjściowe dziennika przy użyciu`Debug.WriteLine` klasy [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) ( wywołania metody).</span><span class="sxs-lookup"><span data-stu-id="9c72d-429">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="9c72d-430">W systemie Linux ten dostawca zapisuje dzienniki do */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="9c72d-430">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="9c72d-431">Dostawca źródła zdarzeń</span><span class="sxs-lookup"><span data-stu-id="9c72d-431">Event Source provider</span></span>

<span data-ttu-id="9c72d-432">Pakiet dostawcy [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na międzyplatformie `Microsoft-Extensions-Logging`źródła zdarzeń o nazwie .</span><span class="sxs-lookup"><span data-stu-id="9c72d-432">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="9c72d-433">W systemie Windows dostawca używa [etw](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="9c72d-433">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="9c72d-434">Dostawca źródła zdarzeń jest dodawany automatycznie po `CreateDefaultBuilder` wywołaniu do utworzenia hosta.</span><span class="sxs-lookup"><span data-stu-id="9c72d-434">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="9c72d-435">narzędzia śledzenia dotnet</span><span class="sxs-lookup"><span data-stu-id="9c72d-435">dotnet trace tooling</span></span>

<span data-ttu-id="9c72d-436">Narzędzie [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) jest wieloplatformowym narzędziem interfejsu wiersza polecenia, które umożliwia zbieranie śladów rdzenia .NET uruchomionego procesu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-436">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="9c72d-437">Narzędzie zbiera <xref:Microsoft.Extensions.Logging.EventSource> dane dostawcy <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>przy użyciu pliku .</span><span class="sxs-lookup"><span data-stu-id="9c72d-437">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="9c72d-438">Zainstaluj narzędzia śledzenia dotnet za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="9c72d-438">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="9c72d-439">Użyj narzędzia śledzenia dotnet, aby zebrać ślad z aplikacji:</span><span class="sxs-lookup"><span data-stu-id="9c72d-439">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="9c72d-440">Jeśli aplikacja nie tworzy hosta `CreateDefaultBuilder`za pomocą , dodaj [dostawcę źródła zdarzeń](#event-source-provider) do konfiguracji rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-440">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="9c72d-441">Uruchom aplikację za `dotnet run` pomocą polecenia.</span><span class="sxs-lookup"><span data-stu-id="9c72d-441">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="9c72d-442">Określ identyfikator procesu (PID) aplikacji .NET Core:</span><span class="sxs-lookup"><span data-stu-id="9c72d-442">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="9c72d-443">W systemie Windows należy użyć jednego z następujących podejść:</span><span class="sxs-lookup"><span data-stu-id="9c72d-443">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="9c72d-444">Menedżer zadań (Ctrl+Alt+Del)</span><span class="sxs-lookup"><span data-stu-id="9c72d-444">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="9c72d-445">polecenie lista zadań</span><span class="sxs-lookup"><span data-stu-id="9c72d-445">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="9c72d-446">Polecenie Get-Process Powershell</span><span class="sxs-lookup"><span data-stu-id="9c72d-446">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="9c72d-447">W systemie Linux użyj [polecenia pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="9c72d-447">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="9c72d-448">Znajdź identyfikator PID dla procesu, który ma taką samą nazwę jak zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-448">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="9c72d-449">Wykonaj `dotnet trace` polecenie.</span><span class="sxs-lookup"><span data-stu-id="9c72d-449">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="9c72d-450">Składnia polecenia ogólnego:</span><span class="sxs-lookup"><span data-stu-id="9c72d-450">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="9c72d-451">W przypadku korzystania z powłoki polecenia `--providers` programu PowerShell`'`należy ująć wartość w pojedyncze cudzysłowy ( ):</span><span class="sxs-lookup"><span data-stu-id="9c72d-451">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="9c72d-452">Na platformach innych niż `-f speedscope` Windows dodaj opcję zmiany formatu `speedscope`wyjściowego pliku śledzenia na .</span><span class="sxs-lookup"><span data-stu-id="9c72d-452">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="9c72d-453">Słowo kluczowe</span><span class="sxs-lookup"><span data-stu-id="9c72d-453">Keyword</span></span> | <span data-ttu-id="9c72d-454">Opis</span><span class="sxs-lookup"><span data-stu-id="9c72d-454">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="9c72d-455">1</span><span class="sxs-lookup"><span data-stu-id="9c72d-455">1</span></span>       | <span data-ttu-id="9c72d-456">Rejestrowanie zdarzeń `LoggingEventSource`meta dotyczących pliku .</span><span class="sxs-lookup"><span data-stu-id="9c72d-456">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="9c72d-457">Nie rejestruje zdarzeń `ILogger`z ).</span><span class="sxs-lookup"><span data-stu-id="9c72d-457">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="9c72d-458">2</span><span class="sxs-lookup"><span data-stu-id="9c72d-458">2</span></span>       | <span data-ttu-id="9c72d-459">Włącza zdarzenie, `Message` `ILogger.Log()` gdy jest wywoływane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-459">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="9c72d-460">Zawiera informacje w sposób programowy (niesformowany).</span><span class="sxs-lookup"><span data-stu-id="9c72d-460">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="9c72d-461">4</span><span class="sxs-lookup"><span data-stu-id="9c72d-461">4</span></span>       | <span data-ttu-id="9c72d-462">Włącza zdarzenie, `FormatMessage` `ILogger.Log()` gdy jest wywoływane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-462">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="9c72d-463">Zawiera sformatowaną wersję ciągu informacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-463">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="9c72d-464">8</span><span class="sxs-lookup"><span data-stu-id="9c72d-464">8</span></span>       | <span data-ttu-id="9c72d-465">Włącza zdarzenie, `MessageJson` `ILogger.Log()` gdy jest wywoływane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-465">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="9c72d-466">Zapewnia reprezentację JSON argumentów.</span><span class="sxs-lookup"><span data-stu-id="9c72d-466">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="9c72d-467">Poziom zdarzenia</span><span class="sxs-lookup"><span data-stu-id="9c72d-467">Event Level</span></span> | <span data-ttu-id="9c72d-468">Opis</span><span class="sxs-lookup"><span data-stu-id="9c72d-468">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="9c72d-469">0</span><span class="sxs-lookup"><span data-stu-id="9c72d-469">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="9c72d-470">1</span><span class="sxs-lookup"><span data-stu-id="9c72d-470">1</span></span>           | `Critical`      |
   | <span data-ttu-id="9c72d-471">2</span><span class="sxs-lookup"><span data-stu-id="9c72d-471">2</span></span>           | `Error`         |
   | <span data-ttu-id="9c72d-472">3</span><span class="sxs-lookup"><span data-stu-id="9c72d-472">3</span></span>           | `Warning`       |
   | <span data-ttu-id="9c72d-473">4</span><span class="sxs-lookup"><span data-stu-id="9c72d-473">4</span></span>           | `Informational` |
   | <span data-ttu-id="9c72d-474">5</span><span class="sxs-lookup"><span data-stu-id="9c72d-474">5</span></span>           | `Verbose`       |

   <span data-ttu-id="9c72d-475">`FilterSpecs`wpisy `{Logger Category}` dla `{Event Level}` i reprezentują dodatkowe warunki filtrowania dziennika.</span><span class="sxs-lookup"><span data-stu-id="9c72d-475">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="9c72d-476">Oddzielne `FilterSpecs` wpisy ze średnikiem`;`( ).</span><span class="sxs-lookup"><span data-stu-id="9c72d-476">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="9c72d-477">Przykład użycia powłoki**no** poleceń systemu Windows `--providers` ( brak pojedynczych cudzysłowów wokół wartości):</span><span class="sxs-lookup"><span data-stu-id="9c72d-477">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="9c72d-478">Poprzednie polecenie aktywuje:</span><span class="sxs-lookup"><span data-stu-id="9c72d-478">The preceding command activates:</span></span>

   * <span data-ttu-id="9c72d-479">Rejestrator źródła zdarzeń do tworzenia sformatowanych ciągów (`4`) dla błędów (`2`).</span><span class="sxs-lookup"><span data-stu-id="9c72d-479">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="9c72d-480">`Microsoft.AspNetCore.Hosting`rejestrowania na `Informational` poziomie rejestrowania (`4`).</span><span class="sxs-lookup"><span data-stu-id="9c72d-480">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="9c72d-481">Zatrzymaj narzędzie śledzenia dotnetu, naciskając klawisz Enter lub Klawisze Ctrl+C.</span><span class="sxs-lookup"><span data-stu-id="9c72d-481">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="9c72d-482">Śledzenie jest zapisywane z nazwą *trace.nettrace* `dotnet trace` w folderze, w którym polecenie jest wykonywane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-482">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="9c72d-483">Otwórz śledzenie za pomocą [programu Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="9c72d-483">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="9c72d-484">Otwórz plik *trace.nettrace* i eksploruj zdarzenia śledzenia.</span><span class="sxs-lookup"><span data-stu-id="9c72d-484">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="9c72d-485">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="9c72d-485">For more information, see:</span></span>

* <span data-ttu-id="9c72d-486">[Narzędzie do analizy wydajności śledzenia (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (dokumentacja rdzenia.NET)</span><span class="sxs-lookup"><span data-stu-id="9c72d-486">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="9c72d-487">[Narzędzie do analizy wydajności śledzenia (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dokumentacja repozytorium GitHub dotnet/diagnostyka)</span><span class="sxs-lookup"><span data-stu-id="9c72d-487">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="9c72d-488">[Klasa LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (przeglądarka interfejsu API.NET)</span><span class="sxs-lookup"><span data-stu-id="9c72d-488">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="9c72d-489">[LoggingEventSource źródło odniesienia (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Aby uzyskać źródło referencyjne `release/{Version}`dla `{Version}` innej wersji, zmień gałąź na , gdzie jest żądana wersja ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9c72d-489">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="9c72d-490">[Perfview](#perfview) &ndash; Przydatne do wyświetlania śladów źródła zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="9c72d-490">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="9c72d-491">Perfview</span><span class="sxs-lookup"><span data-stu-id="9c72d-491">Perfview</span></span>

<span data-ttu-id="9c72d-492">Narzędzie [PerfView](https://github.com/Microsoft/perfview) służy do zbierania i wyświetlania dzienników.</span><span class="sxs-lookup"><span data-stu-id="9c72d-492">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="9c72d-493">Istnieją inne narzędzia do przeglądania dzienników ETW, ale PerfView zapewnia najlepsze środowisko do pracy ze zdarzeniami ETW emitowanymi przez ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9c72d-493">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="9c72d-494">Aby skonfigurować Program PerfView do zbierania zdarzeń zarejestrowanych przez tego dostawcę, dodaj ten ciąg `*Microsoft-Extensions-Logging` do listy Dostawcy **dodatkowi.**</span><span class="sxs-lookup"><span data-stu-id="9c72d-494">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="9c72d-495">(Nie przegap gwiazdki na początku ciągu.)</span><span class="sxs-lookup"><span data-stu-id="9c72d-495">(Don't miss the asterisk at the start of the string.)</span></span>

![Dodatkowi dostawcy Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="9c72d-497">Dostawca usługi Windows EventLog</span><span class="sxs-lookup"><span data-stu-id="9c72d-497">Windows EventLog provider</span></span>

<span data-ttu-id="9c72d-498">Pakiet dostawcy [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) wysyła dane wyjściowe dziennika do dziennika zdarzeń systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="9c72d-498">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="9c72d-499">[Przeciążenia AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) pozwalają <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>przejść w .</span><span class="sxs-lookup"><span data-stu-id="9c72d-499">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="9c72d-500">Jeśli `null` zostanie określony, używane są następujące ustawienia domyślne:</span><span class="sxs-lookup"><span data-stu-id="9c72d-500">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="9c72d-501">`LogName`&ndash; "Aplikacja"</span><span class="sxs-lookup"><span data-stu-id="9c72d-501">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="9c72d-502">`SourceName`&ndash; ".NET Środowisko uruchomieniowe"</span><span class="sxs-lookup"><span data-stu-id="9c72d-502">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="9c72d-503">`MachineName`&ndash; maszyna lokalna</span><span class="sxs-lookup"><span data-stu-id="9c72d-503">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="9c72d-504">Zdarzenia są rejestrowane dla [poziomu ostrzeżenia i wyższej](#log-level).</span><span class="sxs-lookup"><span data-stu-id="9c72d-504">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="9c72d-505">Aby rejestrować `Warning`zdarzenia niższe niż , jawnie ustawić poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="9c72d-505">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="9c72d-506">Na przykład dodaj następujące elementy do pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="9c72d-506">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="9c72d-507">Dostawca tracesource</span><span class="sxs-lookup"><span data-stu-id="9c72d-507">TraceSource provider</span></span>

<span data-ttu-id="9c72d-508">Pakiet dostawcy [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) używa <xref:System.Diagnostics.TraceSource> bibliotek i dostawców.</span><span class="sxs-lookup"><span data-stu-id="9c72d-508">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="9c72d-509">[AddTraceSource przeciążenia](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umożliwiają przekazywanie w przełączniku źródłowym i odbiornik śledzenia.</span><span class="sxs-lookup"><span data-stu-id="9c72d-509">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="9c72d-510">Aby korzystać z tego dostawcy, aplikacja musi działać w programie .NET Framework (a nie .NET Core).</span><span class="sxs-lookup"><span data-stu-id="9c72d-510">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="9c72d-511">Dostawca może kierować wiadomości do różnych [odbiorników,](/dotnet/framework/debug-trace-profile/trace-listeners)takich jak <xref:System.Diagnostics.TextWriterTraceListener> używane w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-511">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="9c72d-512">Dostawca usługi aplikacji platformy Azure</span><span class="sxs-lookup"><span data-stu-id="9c72d-512">Azure App Service provider</span></span>

<span data-ttu-id="9c72d-513">Pakiet dostawcy [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje dzienniki do plików tekstowych w systemie plików aplikacji usługi Azure App Service i magazynu [obiektów blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) na koncie usługi Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="9c72d-513">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="9c72d-514">Pakiet dostawcy nie jest uwzględniony w udostępnionej ramach.</span><span class="sxs-lookup"><span data-stu-id="9c72d-514">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="9c72d-515">Aby użyć dostawcy, dodaj pakiet dostawcy do projektu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-515">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="9c72d-516">Aby skonfigurować ustawienia <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> dostawcy, należy użyć i <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="9c72d-516">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="9c72d-517">Podczas wdrażania w aplikacji usługi App Service aplikacja honoruje ustawienia w sekcji [Dzienniki usługi aplikacji](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stronie **Usługi aplikacji** w witrynie Azure portal.</span><span class="sxs-lookup"><span data-stu-id="9c72d-517">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="9c72d-518">Po zaktualizowaniu następujących ustawień zmiany zaczynają obowiązywać natychmiast bez konieczności ponownego uruchamiania lub ponownego wprowadzania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-518">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="9c72d-519">**Rejestrowanie aplikacji (system plików)**</span><span class="sxs-lookup"><span data-stu-id="9c72d-519">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="9c72d-520">**Rejestrowanie aplikacji (obiekt blob)**</span><span class="sxs-lookup"><span data-stu-id="9c72d-520">**Application Logging (Blob)**</span></span>

<span data-ttu-id="9c72d-521">Domyślna lokalizacja plików dziennika znajduje się w folderze *D:\\\\home LogFiles\\Application,* a domyślną nazwą pliku jest *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="9c72d-521">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="9c72d-522">Domyślny limit rozmiaru pliku wynosi 10 MB, a domyślna maksymalna liczba zachowanych plików to 2.</span><span class="sxs-lookup"><span data-stu-id="9c72d-522">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="9c72d-523">Domyślna nazwa obiektu blob to *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="9c72d-523">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="9c72d-524">Dostawca działa tylko wtedy, gdy projekt jest uruchamiany w środowisku platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="9c72d-524">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="9c72d-525">Nie ma wpływu, gdy projekt&mdash;jest uruchamiany lokalnie nie zapisuje do plików lokalnych lub magazynu lokalnego rozwoju dla obiektów blob.</span><span class="sxs-lookup"><span data-stu-id="9c72d-525">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="9c72d-526">Przesyłanie strumieniowe dziennika platformy Azure</span><span class="sxs-lookup"><span data-stu-id="9c72d-526">Azure log streaming</span></span>

<span data-ttu-id="9c72d-527">Przesyłanie strumieniowe dziennika platformy Azure umożliwia wyświetlanie aktywności dziennika w czasie rzeczywistym z:</span><span class="sxs-lookup"><span data-stu-id="9c72d-527">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="9c72d-528">Serwer aplikacji</span><span class="sxs-lookup"><span data-stu-id="9c72d-528">The app server</span></span>
* <span data-ttu-id="9c72d-529">Serwer www</span><span class="sxs-lookup"><span data-stu-id="9c72d-529">The web server</span></span>
* <span data-ttu-id="9c72d-530">Śledzenie żądań nie powiodło się</span><span class="sxs-lookup"><span data-stu-id="9c72d-530">Failed request tracing</span></span>

<span data-ttu-id="9c72d-531">Aby skonfigurować przesyłanie strumieniowe dziennika platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="9c72d-531">To configure Azure log streaming:</span></span>

* <span data-ttu-id="9c72d-532">Przejdź do strony **Dzienniki usługi app service** ze strony portalu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-532">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="9c72d-533">Ustaw **rejestrowanie aplikacji (system plików)** **na Włączone**.</span><span class="sxs-lookup"><span data-stu-id="9c72d-533">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="9c72d-534">Wybierz **poziom**dziennika .</span><span class="sxs-lookup"><span data-stu-id="9c72d-534">Choose the log **Level**.</span></span> <span data-ttu-id="9c72d-535">To ustawienie dotyczy tylko przesyłania strumieniowego dziennika platformy Azure, a nie innych dostawców rejestrowania w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-535">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="9c72d-536">Przejdź do strony **Strumień dziennika,** aby wyświetlić komunikaty aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-536">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="9c72d-537">Są one rejestrowane przez aplikację `ILogger` za pośrednictwem interfejsu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-537">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="9c72d-538">Rejestrowanie śledzenia usługi Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="9c72d-538">Azure Application Insights trace logging</span></span>

<span data-ttu-id="9c72d-539">Pakiet dostawcy [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje dzienniki w usłudze Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="9c72d-539">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="9c72d-540">Usługa Application Insights to usługa, która monitoruje aplikację sieci web i udostępnia narzędzia do wykonywania zapytań i analizowania danych telemetrycznych.</span><span class="sxs-lookup"><span data-stu-id="9c72d-540">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="9c72d-541">Jeśli używasz tego dostawcy, można kwerendy i analizować dzienniki za pomocą narzędzi usługi Application Insights.</span><span class="sxs-lookup"><span data-stu-id="9c72d-541">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="9c72d-542">Dostawca rejestrowania jest uwzględniony jako zależność [microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), który jest pakietem, który udostępnia wszystkie dostępne dane telemetryczne dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9c72d-542">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="9c72d-543">Jeśli używasz tego pakietu, nie trzeba instalować pakietu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="9c72d-543">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="9c72d-544">Nie należy używać pakietu&mdash; [Microsoft.ApplicationInsights.Web,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) który jest przeznaczony dla ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="9c72d-544">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="9c72d-545">Więcej informacji zawierają następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="9c72d-545">For more information, see the following resources:</span></span>

* [<span data-ttu-id="9c72d-546">Omówienie usługi Application Insights</span><span class="sxs-lookup"><span data-stu-id="9c72d-546">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="9c72d-547">[Usługa Application Insights dla aplikacji ASP.NET Core](/azure/azure-monitor/app/asp-net-core) — uruchom tutaj, jeśli chcesz zaimplementować pełny zakres danych telemetrycznych usługi Application Insights wraz z rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="9c72d-547">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="9c72d-548">[ApplicationInsightsLoggerProvider dla dzienników .NET Core ILogger](/azure/azure-monitor/app/ilogger) — uruchom tutaj, jeśli chcesz zaimplementować dostawcę rejestrowania bez pozostałej części telemetrii usługi Application Insights.</span><span class="sxs-lookup"><span data-stu-id="9c72d-548">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="9c72d-549">[Karty rejestrowania usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="9c72d-549">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="9c72d-550">[Instalowanie, konfigurowanie i inicjowanie zestawu SDK usługi Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) — interaktywny samouczek w witrynie Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="9c72d-550">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="9c72d-551">Zewnętrzni dostawcy usług rejestrowania</span><span class="sxs-lookup"><span data-stu-id="9c72d-551">Third-party logging providers</span></span>

<span data-ttu-id="9c72d-552">Struktury rejestrowania innych firm, które współpracują z ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9c72d-552">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="9c72d-553">[elmah.io](https://elmah.io/) ([Repozytorium GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="9c72d-553">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="9c72d-554">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([Repozytorium GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="9c72d-554">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="9c72d-555">[JSNLog](https://jsnlog.com/) ([Repozytorium GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="9c72d-555">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="9c72d-556">[KissLog.net](https://kisslog.net/) ([Repozytorium GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="9c72d-556">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="9c72d-557">[Log4Net](https://logging.apache.org/log4net/) ([Repozytorium GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="9c72d-557">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="9c72d-558">[Loggr](https://loggr.net/) ([Repozytorium GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="9c72d-558">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="9c72d-559">[NLog](https://nlog-project.org/) ([Repozytorium GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="9c72d-559">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="9c72d-560">[Działko](https://sentry.io/welcome/) [(Repozytorium GitHub)](https://github.com/getsentry/sentry-dotnet)</span><span class="sxs-lookup"><span data-stu-id="9c72d-560">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="9c72d-561">[Serilog](https://serilog.net/) ([Repozytorium GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="9c72d-561">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="9c72d-562">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="9c72d-562">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="9c72d-563">Niektóre struktury innych firm mogą wykonywać [rejestrowanie semantyczne, znane również jako rejestrowanie strukturalne.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)</span><span class="sxs-lookup"><span data-stu-id="9c72d-563">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="9c72d-564">Korzystanie z struktury innej firmy jest podobne do korzystania z jednego z wbudowanych dostawców:</span><span class="sxs-lookup"><span data-stu-id="9c72d-564">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="9c72d-565">Dodaj pakiet NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-565">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="9c72d-566">Wywołanie `ILoggerFactory` metody rozszerzenia dostarczone przez strukturę rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-566">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="9c72d-567">Aby uzyskać więcej informacji, zobacz dokumentację każdego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="9c72d-567">For more information, see each provider's documentation.</span></span> <span data-ttu-id="9c72d-568">Dostawcy rejestrowania innych firm nie są obsługiwani przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9c72d-568">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9c72d-569">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="9c72d-569">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9c72d-570">Przez [Tom Dykstra](https://github.com/tdykstra) i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="9c72d-570">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="9c72d-571">Program .NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi wbudowanymi i zewnętrznymi dostawcami rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-571">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="9c72d-572">W tym artykule pokazano, jak używać interfejsu API rejestrowania z wbudowanymi dostawcami.</span><span class="sxs-lookup"><span data-stu-id="9c72d-572">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="9c72d-573">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9c72d-573">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="9c72d-574">Dodawanie dostawców</span><span class="sxs-lookup"><span data-stu-id="9c72d-574">Add providers</span></span>

<span data-ttu-id="9c72d-575">Dostawca rejestrowania wyświetla lub przechowuje dzienniki.</span><span class="sxs-lookup"><span data-stu-id="9c72d-575">A logging provider displays or stores logs.</span></span> <span data-ttu-id="9c72d-576">Na przykład dostawca konsoli wyświetla dzienniki na konsoli, a dostawca usługi Azure Application Insights przechowuje je w usłudze Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="9c72d-576">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="9c72d-577">Dzienniki mogą być wysyłane do wielu miejsc docelowych, dodając wielu dostawców.</span><span class="sxs-lookup"><span data-stu-id="9c72d-577">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="9c72d-578">Aby dodać dostawcę, zadzwoń `Add{provider name}` do metody rozszerzenia dostawcy w *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="9c72d-578">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="9c72d-579">Powyższy kod wymaga odwołań do `Microsoft.Extensions.Logging` i `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="9c72d-579">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="9c72d-580">Domyślny szablon <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>projektu wywołuje, który dodaje następujących dostawców rejestrowania:</span><span class="sxs-lookup"><span data-stu-id="9c72d-580">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="9c72d-581">Konsola</span><span class="sxs-lookup"><span data-stu-id="9c72d-581">Console</span></span>
* <span data-ttu-id="9c72d-582">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="9c72d-582">Debug</span></span>
* <span data-ttu-id="9c72d-583">EventSource (począwszy od ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="9c72d-583">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="9c72d-584">Jeśli używasz, `CreateDefaultBuilder`możesz zastąpić domyślnych dostawców własnymi wyborami.</span><span class="sxs-lookup"><span data-stu-id="9c72d-584">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="9c72d-585">Zadzwoń <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>i dodaj dostawców, których chcesz.</span><span class="sxs-lookup"><span data-stu-id="9c72d-585">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="9c72d-586">Dowiedz się więcej o [wbudowanych dostawców rejestrowania](#built-in-logging-providers) i [dostawców rejestrowania innych firm](#third-party-logging-providers) w dalszej części artykułu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-586">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="9c72d-587">Tworzenie dzienników</span><span class="sxs-lookup"><span data-stu-id="9c72d-587">Create logs</span></span>

<span data-ttu-id="9c72d-588">Aby utworzyć dzienniki, <xref:Microsoft.Extensions.Logging.ILogger%601> użyj obiektu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-588">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="9c72d-589">W aplikacji sieci web lub usługi `ILogger` hostowanej, uzyskać z iniekcji zależności (DI).</span><span class="sxs-lookup"><span data-stu-id="9c72d-589">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="9c72d-590">W aplikacjach konsoli nie-hosta użyj go `LoggerFactory` do utworzenia pliku `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="9c72d-590">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="9c72d-591">Poniższy przykład ASP.NET Core tworzy rejestrator `TodoApiSample.Pages.AboutModel` z jako kategorii.</span><span class="sxs-lookup"><span data-stu-id="9c72d-591">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="9c72d-592">Kategoria *dziennika* jest ciągiem skojarzonym z każdym dziennikiem.</span><span class="sxs-lookup"><span data-stu-id="9c72d-592">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="9c72d-593">Wystąpienie `ILogger<T>` dostarczone przez DI tworzy dzienniki, które `T` mają w pełni kwalifikowaną nazwę typu jako kategorii.</span><span class="sxs-lookup"><span data-stu-id="9c72d-593">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="9c72d-594">W poniższych ASP.NET przykłady aplikacji Core i konsoli rejestrator jest używany `Information` do tworzenia dzienników z jako poziom.</span><span class="sxs-lookup"><span data-stu-id="9c72d-594">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="9c72d-595">Poziom *dziennika* wskazuje ważność zarejestrowanego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="9c72d-595">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="9c72d-596">[Poziomy](#log-level) i kategorie są [wyjaśnione](#log-category) bardziej szczegółowo w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-596">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="9c72d-597">Tworzenie dzienników w starcie</span><span class="sxs-lookup"><span data-stu-id="9c72d-597">Create logs in Startup</span></span>

<span data-ttu-id="9c72d-598">Aby zapisać dzienniki `Startup` w klasie, dołącz `ILogger` parametr w podpisie konstruktora:</span><span class="sxs-lookup"><span data-stu-id="9c72d-598">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="9c72d-599">Tworzenie dzienników w klasie Program</span><span class="sxs-lookup"><span data-stu-id="9c72d-599">Create logs in the Program class</span></span>

<span data-ttu-id="9c72d-600">Aby napisać dzienniki `Program` w klasie, pobierz wystąpienie `ILogger` z DI:</span><span class="sxs-lookup"><span data-stu-id="9c72d-600">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="9c72d-601">Rejestrowanie podczas budowy hosta nie jest bezpośrednio obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-601">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="9c72d-602">Można jednak użyć oddzielnego rejestratora.</span><span class="sxs-lookup"><span data-stu-id="9c72d-602">However, a separate logger can be used.</span></span> <span data-ttu-id="9c72d-603">W poniższym przykładzie [rejestrator Serilog](https://serilog.net/) jest `CreateWebHostBuilder`używany do logowania .</span><span class="sxs-lookup"><span data-stu-id="9c72d-603">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="9c72d-604">`AddSerilog`wykorzystuje konfigurację statyczną `Log.Logger`określoną w:</span><span class="sxs-lookup"><span data-stu-id="9c72d-604">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="9c72d-605">Brak metod rejestratora asynchronii</span><span class="sxs-lookup"><span data-stu-id="9c72d-605">No asynchronous logger methods</span></span>

<span data-ttu-id="9c72d-606">Rejestrowanie powinno być tak szybkie, że nie jest warte kosztu wydajności kodu asynchroniowego.</span><span class="sxs-lookup"><span data-stu-id="9c72d-606">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="9c72d-607">Jeśli magazyn danych rejestrowania jest powolny, nie zapisuj do niego bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="9c72d-607">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="9c72d-608">Należy rozważyć zapisanie komunikatów dziennika do szybkiego magazynu początkowo, a następnie przenieść je do wolnego magazynu później.</span><span class="sxs-lookup"><span data-stu-id="9c72d-608">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="9c72d-609">Na przykład jeśli logujesz się do programu SQL Server, nie chcesz tego `Log` robić bezpośrednio `Log` w metodzie, ponieważ metody są synchroniczne.</span><span class="sxs-lookup"><span data-stu-id="9c72d-609">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="9c72d-610">Zamiast tego synchronicznie dodać komunikaty dziennika do kolejki w pamięci i mieć proces roboczy w tle wyciągnąć wiadomości z kolejki, aby wykonać asynchronicznie pracy wypychania danych do programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="9c72d-610">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="9c72d-611">Aby uzyskać więcej informacji, zobacz [ten](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problem z githubem.</span><span class="sxs-lookup"><span data-stu-id="9c72d-611">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="9c72d-612">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="9c72d-612">Configuration</span></span>

<span data-ttu-id="9c72d-613">Konfiguracja dostawcy rejestrowania jest dostarczana przez jednego lub więcej dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="9c72d-613">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="9c72d-614">Formaty plików (INI, JSON i XML).</span><span class="sxs-lookup"><span data-stu-id="9c72d-614">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="9c72d-615">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="9c72d-615">Command-line arguments.</span></span>
* <span data-ttu-id="9c72d-616">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="9c72d-616">Environment variables.</span></span>
* <span data-ttu-id="9c72d-617">W pamięci obiektów .NET.</span><span class="sxs-lookup"><span data-stu-id="9c72d-617">In-memory .NET objects.</span></span>
* <span data-ttu-id="9c72d-618">Niezaszyfrowany magazyn [Menedżera tajnych.](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="9c72d-618">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="9c72d-619">Zaszyfrowany magazyn użytkowników, taki jak [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="9c72d-619">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="9c72d-620">Dostawcy niestandardowi (zainstalowane lub utworzone).</span><span class="sxs-lookup"><span data-stu-id="9c72d-620">Custom providers (installed or created).</span></span>

<span data-ttu-id="9c72d-621">Na przykład konfiguracja rejestrowania jest `Logging` często dostarczana przez sekcję plików ustawień aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-621">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="9c72d-622">W poniższym przykładzie przedstawiono zawartość typowych *appsettings. Plik Development.json:*</span><span class="sxs-lookup"><span data-stu-id="9c72d-622">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="9c72d-623">Właściwość `Logging` może `LogLevel` mieć i rejestrować właściwości dostawcy (konsola jest wyświetlana).</span><span class="sxs-lookup"><span data-stu-id="9c72d-623">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="9c72d-624">Właściwość `LogLevel` `Logging` w obszarze określa minimalny [poziom](#log-level) do dziennika dla wybranych kategorii.</span><span class="sxs-lookup"><span data-stu-id="9c72d-624">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="9c72d-625">W przykładzie `System` `Microsoft` i kategorii `Information` dziennika na poziomie, `Debug` a wszystkie inne dziennika na poziomie.</span><span class="sxs-lookup"><span data-stu-id="9c72d-625">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="9c72d-626">Inne właściwości `Logging` w obszarze określić dostawców rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-626">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="9c72d-627">Przykład jest dla dostawcy konsoli.</span><span class="sxs-lookup"><span data-stu-id="9c72d-627">The example is for the Console provider.</span></span> <span data-ttu-id="9c72d-628">Jeśli dostawca obsługuje [zakresy dziennika,](#log-scopes) `IncludeScopes` wskazuje, czy są one włączone.</span><span class="sxs-lookup"><span data-stu-id="9c72d-628">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="9c72d-629">Właściwość dostawcy (na przykład `Console` w przykładzie) może również określić `LogLevel` właściwość.</span><span class="sxs-lookup"><span data-stu-id="9c72d-629">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="9c72d-630">`LogLevel`pod dostawcą określa poziomy do zalogowania dla tego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="9c72d-630">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="9c72d-631">Jeśli poziomy są `Logging.{providername}.LogLevel`określone w , zastępują wszystko ustawione w `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="9c72d-631">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="9c72d-632">Interfejs API rejestrowania nie zawiera scenariusza, aby zmienić poziomy dziennika, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="9c72d-632">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="9c72d-633">Jednak niektórzy dostawcy konfiguracji są w stanie przeładować konfigurację, co ma natychmiastowy wpływ na konfigurację rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-633">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="9c72d-634">Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider), `CreateDefaultBuilder` który jest dodawany przez pliki ustawień odczytu, domyślnie ładuje konfigurację rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-634">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="9c72d-635">Jeśli konfiguracja zostanie zmieniona w kodzie, gdy aplikacja jest uruchomiona, aplikacja może wywołać [IConfigurationRoot.Reload,](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) aby zaktualizować konfigurację rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-635">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="9c72d-636">Aby uzyskać informacje na temat <xref:fundamentals/configuration/index>wdrażania dostawców konfiguracji, zobacz .</span><span class="sxs-lookup"><span data-stu-id="9c72d-636">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="9c72d-637">Przykładowe dane wyjściowe rejestrowania</span><span class="sxs-lookup"><span data-stu-id="9c72d-637">Sample logging output</span></span>

<span data-ttu-id="9c72d-638">Po wyświetleniu przykładowego kodu w poprzedniej sekcji dzienniki są wyświetlane w konsoli, gdy aplikacja jest uruchamiana z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="9c72d-638">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="9c72d-639">Oto przykład danych wyjściowych konsoli:</span><span class="sxs-lookup"><span data-stu-id="9c72d-639">Here's an example of console output:</span></span>

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

<span data-ttu-id="9c72d-640">Poprzednie dzienniki zostały wygenerowane przez wykonanie żądania HTTP `http://localhost:5000/api/todo/0`Get do przykładowej aplikacji o godzinie .</span><span class="sxs-lookup"><span data-stu-id="9c72d-640">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="9c72d-641">Oto przykład tych samych dzienników, które pojawiają się w oknie debugowania po uruchomieniu przykładowej aplikacji w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="9c72d-641">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="9c72d-642">Dzienniki, które są `ILogger` tworzone przez wywołania pokazane w poprzedniej sekcji zaczynają się od "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="9c72d-642">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="9c72d-643">Dzienniki, które zaczynają się od kategorii "Microsoft" pochodzą z ASP.NET kodu frameworka Core.</span><span class="sxs-lookup"><span data-stu-id="9c72d-643">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="9c72d-644">ASP.NET Core i kod aplikacji są przy użyciu tego samego interfejsu API rejestrowania i dostawców.</span><span class="sxs-lookup"><span data-stu-id="9c72d-644">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="9c72d-645">Poniżej dalsza część artykułu Wyjaśniono niektóre szczegóły i opcje rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-645">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="9c72d-646">Pakiety NuGet</span><span class="sxs-lookup"><span data-stu-id="9c72d-646">NuGet packages</span></span>

<span data-ttu-id="9c72d-647">`ILogger` Interfejsy `ILoggerFactory` i interfejsy znajdują się w witrynie [Microsoft.Extensions.Logging.Abstractions,](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a domyślne implementacje dla nich znajdują się w [witrynie Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="9c72d-647">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="9c72d-648">Kategoria dziennika</span><span class="sxs-lookup"><span data-stu-id="9c72d-648">Log category</span></span>

<span data-ttu-id="9c72d-649">Podczas `ILogger` tworzenia obiektu określana jest dla niego *kategoria.*</span><span class="sxs-lookup"><span data-stu-id="9c72d-649">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="9c72d-650">Ta kategoria jest dołączona do każdej wiadomości `ILogger`dziennika utworzonej przez to wystąpienie .</span><span class="sxs-lookup"><span data-stu-id="9c72d-650">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="9c72d-651">Kategoria może być dowolny ciąg, ale konwencja jest użycie nazwy klasy, takich jak "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="9c72d-651">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="9c72d-652">Użyj, `ILogger<T>` aby `ILogger` uzyskać wystąpienie, które używa `T` w pełni kwalifikowanej nazwy typu jako kategorii:</span><span class="sxs-lookup"><span data-stu-id="9c72d-652">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="9c72d-653">Aby jawnie określić kategorię, zadzwoń: `ILoggerFactory.CreateLogger`</span><span class="sxs-lookup"><span data-stu-id="9c72d-653">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="9c72d-654">`ILogger<T>`jest odpowiednikiem `CreateLogger` wywołania z w `T`pełni kwalifikowaną nazwą typu .</span><span class="sxs-lookup"><span data-stu-id="9c72d-654">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="9c72d-655">Poziom dziennika</span><span class="sxs-lookup"><span data-stu-id="9c72d-655">Log level</span></span>

<span data-ttu-id="9c72d-656">Każdy dziennik określa <xref:Microsoft.Extensions.Logging.LogLevel> wartość.</span><span class="sxs-lookup"><span data-stu-id="9c72d-656">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="9c72d-657">Poziom dziennika wskazuje ważność lub ważność.</span><span class="sxs-lookup"><span data-stu-id="9c72d-657">The log level indicates the severity or importance.</span></span> <span data-ttu-id="9c72d-658">Na przykład można napisać `Information` dziennik, gdy metoda kończy `Warning` się normalnie i dziennika, gdy metoda zwraca kod stanu *404 Nie znaleziono.*</span><span class="sxs-lookup"><span data-stu-id="9c72d-658">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="9c72d-659">Następujący kod `Information` tworzy `Warning` i rejestruje:</span><span class="sxs-lookup"><span data-stu-id="9c72d-659">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="9c72d-660">W poprzednim kodzie pierwszym parametrem jest [identyfikator zdarzenia Log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="9c72d-660">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="9c72d-661">Drugi parametr jest szablonem wiadomości z symbolami zastępczymi dla wartości argumentów dostarczonych przez pozostałe parametry metody.</span><span class="sxs-lookup"><span data-stu-id="9c72d-661">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="9c72d-662">Parametry metody są wyjaśnione w [sekcji szablonu wiadomości](#log-message-template) w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-662">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="9c72d-663">Metody dziennika, które zawierają poziom w nazwie `LogInformation` `LogWarning`metody (na przykład i ) są [metody rozszerzenia dla ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="9c72d-663">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="9c72d-664">Te metody `Log` wywołać metodę, która przyjmuje `LogLevel` parametr.</span><span class="sxs-lookup"><span data-stu-id="9c72d-664">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="9c72d-665">Można wywołać `Log` metodę bezpośrednio, a nie jedną z tych metod rozszerzenia, ale składnia jest stosunkowo skomplikowana.</span><span class="sxs-lookup"><span data-stu-id="9c72d-665">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="9c72d-666">Aby uzyskać więcej <xref:Microsoft.Extensions.Logging.ILogger> informacji, zobacz kod [źródłowy rozszerzeń rejestratora](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="9c72d-666">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="9c72d-667">ASP.NET Core definiuje następujące poziomy dziennika, uporządkowane w tym miejscu od najniższej do najwyższej ważności.</span><span class="sxs-lookup"><span data-stu-id="9c72d-667">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="9c72d-668">Ślad = 0</span><span class="sxs-lookup"><span data-stu-id="9c72d-668">Trace = 0</span></span>

  <span data-ttu-id="9c72d-669">Aby uzyskać informacje, które są zazwyczaj cenne tylko do debugowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-669">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="9c72d-670">Te komunikaty mogą zawierać poufne dane aplikacji, a więc nie powinny być włączone w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="9c72d-670">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="9c72d-671">*Domyślnie wyłączone.*</span><span class="sxs-lookup"><span data-stu-id="9c72d-671">*Disabled by default.*</span></span>

* <span data-ttu-id="9c72d-672">Debugowanie = 1</span><span class="sxs-lookup"><span data-stu-id="9c72d-672">Debug = 1</span></span>

  <span data-ttu-id="9c72d-673">Aby uzyskać informacje, które mogą być przydatne w rozwoju i debugowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-673">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="9c72d-674">Przykład: `Entering method Configure with flag set to true.` `Debug` Włącz logi poziomu w produkcji tylko podczas rozwiązywania problemów, ze względu na dużą liczbę dzienników.</span><span class="sxs-lookup"><span data-stu-id="9c72d-674">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="9c72d-675">Informacje = 2</span><span class="sxs-lookup"><span data-stu-id="9c72d-675">Information = 2</span></span>

  <span data-ttu-id="9c72d-676">Do śledzenia ogólnego przepływu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-676">For tracking the general flow of the app.</span></span> <span data-ttu-id="9c72d-677">Te dzienniki zazwyczaj mają pewną wartość długoterminową.</span><span class="sxs-lookup"><span data-stu-id="9c72d-677">These logs typically have some long-term value.</span></span> <span data-ttu-id="9c72d-678">Przykład: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="9c72d-678">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="9c72d-679">Ostrzeżenie = 3</span><span class="sxs-lookup"><span data-stu-id="9c72d-679">Warning = 3</span></span>

  <span data-ttu-id="9c72d-680">W przypadku nieprawidłowych lub nieoczekiwanych zdarzeń w przepływie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-680">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="9c72d-681">Mogą to być błędy lub inne warunki, które nie powodują zatrzymania aplikacji, ale mogą wymagać zbadania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-681">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="9c72d-682">Obsługiwane wyjątki są wspólne miejsce `Warning` do korzystania z poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="9c72d-682">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="9c72d-683">Przykład: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="9c72d-683">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="9c72d-684">Błąd = 4</span><span class="sxs-lookup"><span data-stu-id="9c72d-684">Error = 4</span></span>

  <span data-ttu-id="9c72d-685">Dla błędów i wyjątków, które nie mogą być obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-685">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="9c72d-686">Te komunikaty wskazują na błąd w bieżącym działaniu lub operacji (takich jak bieżące żądanie HTTP), a nie błąd całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-686">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="9c72d-687">Przykładowy komunikat dziennika:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="9c72d-687">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="9c72d-688">Krytyczny = 5</span><span class="sxs-lookup"><span data-stu-id="9c72d-688">Critical = 5</span></span>

  <span data-ttu-id="9c72d-689">W przypadku awarii, które wymagają natychmiastowej uwagi.</span><span class="sxs-lookup"><span data-stu-id="9c72d-689">For failures that require immediate attention.</span></span> <span data-ttu-id="9c72d-690">Przykłady: scenariusze utraty danych, brak miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="9c72d-690">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="9c72d-691">Użyj poziomu dziennika, aby kontrolować, ile danych wyjściowych dziennika jest zapisywany na określonym nośniku pamięci lub oknie wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-691">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="9c72d-692">Przykład:</span><span class="sxs-lookup"><span data-stu-id="9c72d-692">For example:</span></span>

* <span data-ttu-id="9c72d-693">W produkcji:</span><span class="sxs-lookup"><span data-stu-id="9c72d-693">In production:</span></span>
  * <span data-ttu-id="9c72d-694">Rejestrowanie na `Trace` `Information` poziomach za pośrednictwem tworzy dużą ilość szczegółowych komunikatów dziennika.</span><span class="sxs-lookup"><span data-stu-id="9c72d-694">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="9c72d-695">Aby kontrolować koszty i nie przekraczać `Information` limitów magazynowania danych, rejestruj `Trace` komunikaty poziomu do magazynu danych o dużej objętości i niskich kosztach.</span><span class="sxs-lookup"><span data-stu-id="9c72d-695">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="9c72d-696">Rejestrowanie `Warning` na `Critical` poziomach za pośrednictwem zazwyczaj generuje mniej, mniejsze komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="9c72d-696">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="9c72d-697">W związku z tym koszty i limity magazynowania zwykle nie są problemem, co skutkuje większą elastycznością wyboru magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="9c72d-697">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="9c72d-698">Podczas rozwoju:</span><span class="sxs-lookup"><span data-stu-id="9c72d-698">During development:</span></span>
  * <span data-ttu-id="9c72d-699">Zaloguj `Warning` `Critical` się za pośrednictwem wiadomości do konsoli.</span><span class="sxs-lookup"><span data-stu-id="9c72d-699">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="9c72d-700">Dodaj `Trace` `Information` za pośrednictwem wiadomości podczas rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="9c72d-700">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="9c72d-701">[Sekcja filtrowania dziennika](#log-filtering) w dalszej części tego artykułu wyjaśnia, jak kontrolować poziomy dziennika, które obsługuje dostawca.</span><span class="sxs-lookup"><span data-stu-id="9c72d-701">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="9c72d-702">ASP.NET Core zapisuje dzienniki dla zdarzeń struktury.</span><span class="sxs-lookup"><span data-stu-id="9c72d-702">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="9c72d-703">Przykłady dziennika wcześniej w tym artykule wykluczone dzienniki poniżej `Information` poziomu, więc nie `Debug` lub `Trace` poziom dzienniki zostały utworzone.</span><span class="sxs-lookup"><span data-stu-id="9c72d-703">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="9c72d-704">Oto przykład dzienników konsoli wyprodukowanych przez uruchomienie przykładowej `Debug` aplikacji skonfigurowanej do pokazywalek dzienników:</span><span class="sxs-lookup"><span data-stu-id="9c72d-704">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="9c72d-705">Identyfikator zdarzenia dziennika</span><span class="sxs-lookup"><span data-stu-id="9c72d-705">Log event ID</span></span>

<span data-ttu-id="9c72d-706">Każdy dziennik może określić *identyfikator zdarzenia*.</span><span class="sxs-lookup"><span data-stu-id="9c72d-706">Each log can specify an *event ID*.</span></span> <span data-ttu-id="9c72d-707">Przykładowa aplikacja robi to przy `LoggingEvents` użyciu klasy zdefiniowanej lokalnie:</span><span class="sxs-lookup"><span data-stu-id="9c72d-707">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="9c72d-708">Identyfikator zdarzenia kojarzy zestaw zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="9c72d-708">An event ID associates a set of events.</span></span> <span data-ttu-id="9c72d-709">Na przykład wszystkie dzienniki związane z wyświetlaniem listy elementów na stronie może być 1001.</span><span class="sxs-lookup"><span data-stu-id="9c72d-709">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="9c72d-710">Dostawca rejestrowania może przechowywać identyfikator zdarzenia w polu identyfikator, w komunikacie rejestrowania lub w ogóle.</span><span class="sxs-lookup"><span data-stu-id="9c72d-710">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="9c72d-711">Dostawca debugowania nie pokazuje identyfikatorów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="9c72d-711">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="9c72d-712">Dostawca konsoli wyświetla identyfikatory zdarzeń w nawiasach po kategorii:</span><span class="sxs-lookup"><span data-stu-id="9c72d-712">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="9c72d-713">Szablon komunikatu dziennika</span><span class="sxs-lookup"><span data-stu-id="9c72d-713">Log message template</span></span>

<span data-ttu-id="9c72d-714">Każdy dziennik określa szablon wiadomości.</span><span class="sxs-lookup"><span data-stu-id="9c72d-714">Each log specifies a message template.</span></span> <span data-ttu-id="9c72d-715">Szablon wiadomości może zawierać symbole zastępcze, dla których są dostarczane argumenty.</span><span class="sxs-lookup"><span data-stu-id="9c72d-715">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="9c72d-716">Użyj nazw symboli zastępczych, a nie liczb.</span><span class="sxs-lookup"><span data-stu-id="9c72d-716">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="9c72d-717">Kolejność symboli zastępczych, a nie ich nazw, określa, które parametry są używane do dostarczania ich wartości.</span><span class="sxs-lookup"><span data-stu-id="9c72d-717">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="9c72d-718">W poniższym kodzie należy zauważyć, że nazwy parametrów są niesekwencjonowane w szablonie wiadomości:</span><span class="sxs-lookup"><span data-stu-id="9c72d-718">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="9c72d-719">Ten kod tworzy komunikat dziennika z wartościami parametrów w sekwencji:</span><span class="sxs-lookup"><span data-stu-id="9c72d-719">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="9c72d-720">Struktura rejestrowania działa w ten sposób, dzięki czemu dostawcy rejestrowania mogą zaimplementować [rejestrowanie semantyczne, znane również jako rejestrowanie strukturalne.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)</span><span class="sxs-lookup"><span data-stu-id="9c72d-720">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="9c72d-721">Same argumenty są przekazywane do systemu rejestrowania, a nie tylko do sformatowanego szablonu wiadomości.</span><span class="sxs-lookup"><span data-stu-id="9c72d-721">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="9c72d-722">Te informacje umożliwiają dostawcom rejestrowania przechowywanie wartości parametrów jako pól.</span><span class="sxs-lookup"><span data-stu-id="9c72d-722">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="9c72d-723">Załóżmy na przykład, że wywołania metody rejestratora wyglądają następująco:</span><span class="sxs-lookup"><span data-stu-id="9c72d-723">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="9c72d-724">Jeśli wysyłasz dzienniki do usługi Azure Table Storage, `ID` każda `RequestTime` jednostka tabeli platformy Azure może mieć i właściwości, co upraszcza zapytania dotyczące danych dziennika.</span><span class="sxs-lookup"><span data-stu-id="9c72d-724">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="9c72d-725">Kwerenda może znaleźć wszystkie dzienniki w określonym `RequestTime` zakresie bez analizowania czasu poza wiadomością tekstową.</span><span class="sxs-lookup"><span data-stu-id="9c72d-725">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="9c72d-726">Rejestrowanie wyjątków</span><span class="sxs-lookup"><span data-stu-id="9c72d-726">Logging exceptions</span></span>

<span data-ttu-id="9c72d-727">Metody rejestratora mają przeciążenia, które umożliwiają przekazywanie w wyjątku, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="9c72d-727">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="9c72d-728">Różni dostawcy obsługują informacje o wyjątkach na różne sposoby.</span><span class="sxs-lookup"><span data-stu-id="9c72d-728">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="9c72d-729">Oto przykład danych wyjściowych dostawcy debugowania z kodu pokazanego powyżej.</span><span class="sxs-lookup"><span data-stu-id="9c72d-729">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="9c72d-730">Filtrowanie dzienników</span><span class="sxs-lookup"><span data-stu-id="9c72d-730">Log filtering</span></span>

<span data-ttu-id="9c72d-731">Można określić minimalny poziom dziennika dla określonego dostawcy i kategorii lub dla wszystkich dostawców lub wszystkich kategorii.</span><span class="sxs-lookup"><span data-stu-id="9c72d-731">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="9c72d-732">Wszelkie dzienniki poniżej minimalnego poziomu nie są przekazywane do tego dostawcy, więc nie są wyświetlane ani przechowywane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-732">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="9c72d-733">Aby pominąć wszystkie `LogLevel.None` dzienniki, należy określić jako minimalny poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="9c72d-733">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="9c72d-734">Wartość całkowita `LogLevel.None` wynosi 6, która jest `LogLevel.Critical` wyższa niż (5).</span><span class="sxs-lookup"><span data-stu-id="9c72d-734">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="9c72d-735">Tworzenie reguł filtrowania w konfiguracji</span><span class="sxs-lookup"><span data-stu-id="9c72d-735">Create filter rules in configuration</span></span>

<span data-ttu-id="9c72d-736">Kod szablonu `CreateDefaultBuilder` projektu wywołuje skonfigurowanie rejestrowania dla dostawców konsoli, debugowania i źródła zdarzeń (ASP.NET Core 2.2 lub nowszych).</span><span class="sxs-lookup"><span data-stu-id="9c72d-736">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="9c72d-737">Metoda `CreateDefaultBuilder` konfiguruje rejestrowanie w celu `Logging` wyszukywania konfiguracji w sekcji, jak wyjaśniono [wcześniej w tym artykule](#configuration).</span><span class="sxs-lookup"><span data-stu-id="9c72d-737">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="9c72d-738">Dane konfiguracyjne określają minimalne poziomy dziennika według dostawcy i kategorii, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="9c72d-738">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="9c72d-739">Ten JSON tworzy sześć reguł filtrowania: jeden dla dostawcy debugowania, cztery dla dostawcy konsoli i jeden dla wszystkich dostawców.</span><span class="sxs-lookup"><span data-stu-id="9c72d-739">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="9c72d-740">Pojedyncza reguła jest wybierana `ILogger` dla każdego dostawcy podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-740">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="9c72d-741">Reguły filtrowania w kodzie</span><span class="sxs-lookup"><span data-stu-id="9c72d-741">Filter rules in code</span></span>

<span data-ttu-id="9c72d-742">W poniższym przykładzie pokazano, jak zarejestrować reguły filtrowania w kodzie:</span><span class="sxs-lookup"><span data-stu-id="9c72d-742">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="9c72d-743">Drugi `AddFilter` określa dostawcę debugowania przy użyciu jego nazwy typu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-743">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="9c72d-744">Pierwszy `AddFilter` dotyczy wszystkich dostawców, ponieważ nie określa typu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="9c72d-744">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="9c72d-745">Jak stosowane są reguły filtrowania</span><span class="sxs-lookup"><span data-stu-id="9c72d-745">How filtering rules are applied</span></span>

<span data-ttu-id="9c72d-746">Dane konfiguracji i `AddFilter` kod pokazany w poprzednich przykładach tworzą reguły pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="9c72d-746">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="9c72d-747">Pierwsze sześć pochodzą z przykładu konfiguracji i dwa ostatnie pochodzą z przykładu kodu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-747">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="9c72d-748">Liczba</span><span class="sxs-lookup"><span data-stu-id="9c72d-748">Number</span></span> | <span data-ttu-id="9c72d-749">Dostawca</span><span class="sxs-lookup"><span data-stu-id="9c72d-749">Provider</span></span>      | <span data-ttu-id="9c72d-750">Kategorie, które zaczynają się od ...</span><span class="sxs-lookup"><span data-stu-id="9c72d-750">Categories that begin with ...</span></span>          | <span data-ttu-id="9c72d-751">Minimalny poziom dziennika</span><span class="sxs-lookup"><span data-stu-id="9c72d-751">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="9c72d-752">1</span><span class="sxs-lookup"><span data-stu-id="9c72d-752">1</span></span>      | <span data-ttu-id="9c72d-753">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="9c72d-753">Debug</span></span>         | <span data-ttu-id="9c72d-754">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="9c72d-754">All categories</span></span>                          | <span data-ttu-id="9c72d-755">Informacje</span><span class="sxs-lookup"><span data-stu-id="9c72d-755">Information</span></span>       |
| <span data-ttu-id="9c72d-756">2</span><span class="sxs-lookup"><span data-stu-id="9c72d-756">2</span></span>      | <span data-ttu-id="9c72d-757">Konsola</span><span class="sxs-lookup"><span data-stu-id="9c72d-757">Console</span></span>       | <span data-ttu-id="9c72d-758">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="9c72d-758">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="9c72d-759">Ostrzeżenie</span><span class="sxs-lookup"><span data-stu-id="9c72d-759">Warning</span></span>           |
| <span data-ttu-id="9c72d-760">3</span><span class="sxs-lookup"><span data-stu-id="9c72d-760">3</span></span>      | <span data-ttu-id="9c72d-761">Konsola</span><span class="sxs-lookup"><span data-stu-id="9c72d-761">Console</span></span>       | <span data-ttu-id="9c72d-762">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="9c72d-762">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="9c72d-763">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="9c72d-763">Debug</span></span>             |
| <span data-ttu-id="9c72d-764">4</span><span class="sxs-lookup"><span data-stu-id="9c72d-764">4</span></span>      | <span data-ttu-id="9c72d-765">Konsola</span><span class="sxs-lookup"><span data-stu-id="9c72d-765">Console</span></span>       | <span data-ttu-id="9c72d-766">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="9c72d-766">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="9c72d-767">Błąd</span><span class="sxs-lookup"><span data-stu-id="9c72d-767">Error</span></span>             |
| <span data-ttu-id="9c72d-768">5</span><span class="sxs-lookup"><span data-stu-id="9c72d-768">5</span></span>      | <span data-ttu-id="9c72d-769">Konsola</span><span class="sxs-lookup"><span data-stu-id="9c72d-769">Console</span></span>       | <span data-ttu-id="9c72d-770">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="9c72d-770">All categories</span></span>                          | <span data-ttu-id="9c72d-771">Informacje</span><span class="sxs-lookup"><span data-stu-id="9c72d-771">Information</span></span>       |
| <span data-ttu-id="9c72d-772">6</span><span class="sxs-lookup"><span data-stu-id="9c72d-772">6</span></span>      | <span data-ttu-id="9c72d-773">Wszyscy dostawcy</span><span class="sxs-lookup"><span data-stu-id="9c72d-773">All providers</span></span> | <span data-ttu-id="9c72d-774">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="9c72d-774">All categories</span></span>                          | <span data-ttu-id="9c72d-775">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="9c72d-775">Debug</span></span>             |
| <span data-ttu-id="9c72d-776">7</span><span class="sxs-lookup"><span data-stu-id="9c72d-776">7</span></span>      | <span data-ttu-id="9c72d-777">Wszyscy dostawcy</span><span class="sxs-lookup"><span data-stu-id="9c72d-777">All providers</span></span> | <span data-ttu-id="9c72d-778">System</span><span class="sxs-lookup"><span data-stu-id="9c72d-778">System</span></span>                                  | <span data-ttu-id="9c72d-779">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="9c72d-779">Debug</span></span>             |
| <span data-ttu-id="9c72d-780">8</span><span class="sxs-lookup"><span data-stu-id="9c72d-780">8</span></span>      | <span data-ttu-id="9c72d-781">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="9c72d-781">Debug</span></span>         | <span data-ttu-id="9c72d-782">Microsoft</span><span class="sxs-lookup"><span data-stu-id="9c72d-782">Microsoft</span></span>                               | <span data-ttu-id="9c72d-783">Ślad</span><span class="sxs-lookup"><span data-stu-id="9c72d-783">Trace</span></span>             |

<span data-ttu-id="9c72d-784">Po `ILogger` utworzeniu `ILoggerFactory` obiektu obiekt wybiera jedną regułę na dostawcę, aby zastosować do tego rejestratora.</span><span class="sxs-lookup"><span data-stu-id="9c72d-784">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="9c72d-785">Wszystkie wiadomości napisane `ILogger` przez wystąpienie są filtrowane na podstawie wybranych reguł.</span><span class="sxs-lookup"><span data-stu-id="9c72d-785">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="9c72d-786">Najbardziej specyficzna reguła możliwa dla każdego dostawcy i pary kategorii jest wybierana z dostępnych reguł.</span><span class="sxs-lookup"><span data-stu-id="9c72d-786">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="9c72d-787">Następujący algorytm jest używany dla `ILogger` każdego dostawcy, gdy jest tworzony dla danej kategorii:</span><span class="sxs-lookup"><span data-stu-id="9c72d-787">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="9c72d-788">Wybierz wszystkie reguły, które pasują do dostawcy lub jego aliasu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-788">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="9c72d-789">Jeśli nie zostanie znaleziony żaden mecz, wybierz wszystkie reguły z pustym dostawcą.</span><span class="sxs-lookup"><span data-stu-id="9c72d-789">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="9c72d-790">Z wyniku poprzedniego kroku wybierz reguły z najdłuższym pasującym prefiksem kategorii.</span><span class="sxs-lookup"><span data-stu-id="9c72d-790">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="9c72d-791">Jeśli nie zostanie znalezione żadne dopasowanie, wybierz wszystkie reguły, które nie określają kategorii.</span><span class="sxs-lookup"><span data-stu-id="9c72d-791">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="9c72d-792">Jeśli zaznaczono wiele reguł, weź **ostatni.**</span><span class="sxs-lookup"><span data-stu-id="9c72d-792">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="9c72d-793">Jeśli nie wybrano żadnych reguł, użyj . `MinimumLevel`</span><span class="sxs-lookup"><span data-stu-id="9c72d-793">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="9c72d-794">Załóżmy, że na powyższej `ILogger` liście reguł zostanie utworzony obiekt dla kategorii "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="9c72d-794">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="9c72d-795">W przypadku dostawcy debugowania obowiązują reguły 1, 6 i 8.</span><span class="sxs-lookup"><span data-stu-id="9c72d-795">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="9c72d-796">Reguła 8 jest najbardziej szczegółowa, więc to jest ta wybrana.</span><span class="sxs-lookup"><span data-stu-id="9c72d-796">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="9c72d-797">W przypadku dostawcy konsoli obowiązują zasady 3, 4, 5 i 6.</span><span class="sxs-lookup"><span data-stu-id="9c72d-797">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="9c72d-798">Zasada 3 jest najbardziej szczegółowa.</span><span class="sxs-lookup"><span data-stu-id="9c72d-798">Rule 3 is most specific.</span></span>

<span data-ttu-id="9c72d-799">Wynikowe `ILogger` wystąpienie wysyła `Trace` dzienniki poziomu i powyżej do dostawcy debugowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-799">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="9c72d-800">Dzienniki `Debug` poziomu i powyżej są wysyłane do dostawcy konsoli.</span><span class="sxs-lookup"><span data-stu-id="9c72d-800">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="9c72d-801">Aliasy dostawców</span><span class="sxs-lookup"><span data-stu-id="9c72d-801">Provider aliases</span></span>

<span data-ttu-id="9c72d-802">Każdy dostawca definiuje *alias,* który może być używany w konfiguracji zamiast w pełni kwalifikowanej nazwy typu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-802">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="9c72d-803">W przypadku wbudowanych dostawców użyj następujących aliasów:</span><span class="sxs-lookup"><span data-stu-id="9c72d-803">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="9c72d-804">Konsola</span><span class="sxs-lookup"><span data-stu-id="9c72d-804">Console</span></span>
* <span data-ttu-id="9c72d-805">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="9c72d-805">Debug</span></span>
* <span data-ttu-id="9c72d-806">EventSource</span><span class="sxs-lookup"><span data-stu-id="9c72d-806">EventSource</span></span>
* <span data-ttu-id="9c72d-807">Eventlog</span><span class="sxs-lookup"><span data-stu-id="9c72d-807">EventLog</span></span>
* <span data-ttu-id="9c72d-808">TraceSource</span><span class="sxs-lookup"><span data-stu-id="9c72d-808">TraceSource</span></span>
* <span data-ttu-id="9c72d-809">Plik usługi AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="9c72d-809">AzureAppServicesFile</span></span>
* <span data-ttu-id="9c72d-810">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="9c72d-810">AzureAppServicesBlob</span></span>
* <span data-ttu-id="9c72d-811">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="9c72d-811">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="9c72d-812">Domyślny poziom minimalny</span><span class="sxs-lookup"><span data-stu-id="9c72d-812">Default minimum level</span></span>

<span data-ttu-id="9c72d-813">Istnieje minimalne ustawienie poziomu, które staje się skuteczne tylko wtedy, gdy żadne reguły z konfiguracji lub kodu nie mają zastosowania do danego dostawcy i kategorii.</span><span class="sxs-lookup"><span data-stu-id="9c72d-813">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="9c72d-814">W poniższym przykładzie pokazano, jak ustawić minimalny poziom:</span><span class="sxs-lookup"><span data-stu-id="9c72d-814">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="9c72d-815">Jeśli nie ustawisz jawnie minimalnego poziomu, `Information`wartością `Trace` domyślną jest , co oznacza, że i `Debug` dzienniki są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-815">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="9c72d-816">Funkcje filtrowania</span><span class="sxs-lookup"><span data-stu-id="9c72d-816">Filter functions</span></span>

<span data-ttu-id="9c72d-817">Funkcja filtrowania jest wywoływana dla wszystkich dostawców i kategorii, które nie mają reguł przypisanych do nich przez konfigurację lub kod.</span><span class="sxs-lookup"><span data-stu-id="9c72d-817">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="9c72d-818">Kod w funkcji ma dostęp do typu dostawcy, kategorii i poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="9c72d-818">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="9c72d-819">Przykład:</span><span class="sxs-lookup"><span data-stu-id="9c72d-819">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="9c72d-820">Kategorie i poziomy systemu</span><span class="sxs-lookup"><span data-stu-id="9c72d-820">System categories and levels</span></span>

<span data-ttu-id="9c72d-821">Oto kilka kategorii używanych przez ASP.NET Core i Entity Framework Core, z uwagami na temat tego, jakich dzienników można się po nich spodziewać:</span><span class="sxs-lookup"><span data-stu-id="9c72d-821">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="9c72d-822">Kategoria</span><span class="sxs-lookup"><span data-stu-id="9c72d-822">Category</span></span>                            | <span data-ttu-id="9c72d-823">Uwagi</span><span class="sxs-lookup"><span data-stu-id="9c72d-823">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="9c72d-824">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="9c72d-824">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="9c72d-825">Diagnostyka ASP.NET podstawowa.</span><span class="sxs-lookup"><span data-stu-id="9c72d-825">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="9c72d-826">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="9c72d-826">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="9c72d-827">Które klucze zostały uznane, znalezione i używane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-827">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="9c72d-828">Filtrowanie pliku Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="9c72d-828">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="9c72d-829">Hosty dozwolone.</span><span class="sxs-lookup"><span data-stu-id="9c72d-829">Hosts allowed.</span></span> |
| <span data-ttu-id="9c72d-830">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="9c72d-830">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="9c72d-831">Jak długo trwa wypełnianie żądań HTTP i o której godzinie rozpoczęto.</span><span class="sxs-lookup"><span data-stu-id="9c72d-831">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="9c72d-832">Które zestawy uruchamiania hostingu zostały załadowane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-832">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="9c72d-833">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="9c72d-833">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="9c72d-834">Diagnostyka MVC i Razor.</span><span class="sxs-lookup"><span data-stu-id="9c72d-834">MVC and Razor diagnostics.</span></span> <span data-ttu-id="9c72d-835">Powiązanie modelu, wykonanie filtru, kompilacja widoku, wybór akcji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-835">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="9c72d-836">Marszruta Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="9c72d-836">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="9c72d-837">Informacje o dopasowaniu trasy.</span><span class="sxs-lookup"><span data-stu-id="9c72d-837">Route matching information.</span></span> |
| <span data-ttu-id="9c72d-838">Serwer Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="9c72d-838">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="9c72d-839">Połączenie rozpoczyna, zatrzymuje i utrzymuje żywe odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9c72d-839">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="9c72d-840">Informacje o certyfikacie HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9c72d-840">HTTPS certificate information.</span></span> |
| <span data-ttu-id="9c72d-841">Plikami Microsoft.AspNetCore.Static</span><span class="sxs-lookup"><span data-stu-id="9c72d-841">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="9c72d-842">Pliki obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="9c72d-842">Files served.</span></span> |
| <span data-ttu-id="9c72d-843">Microsoft.EntityFrameCore</span><span class="sxs-lookup"><span data-stu-id="9c72d-843">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="9c72d-844">Diagnostyka rdzenia podstawowej struktury jednostki ogólne.</span><span class="sxs-lookup"><span data-stu-id="9c72d-844">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="9c72d-845">Aktywność i konfiguracja bazy danych, wykrywanie zmian, migracje.</span><span class="sxs-lookup"><span data-stu-id="9c72d-845">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="9c72d-846">Zakresy dzienników</span><span class="sxs-lookup"><span data-stu-id="9c72d-846">Log scopes</span></span>

 <span data-ttu-id="9c72d-847">*Zakres* można grupować zestaw operacji logicznych.</span><span class="sxs-lookup"><span data-stu-id="9c72d-847">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="9c72d-848">To grupowanie może służyć do dołączania tych samych danych do każdego dziennika, który jest tworzony jako część zestawu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-848">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="9c72d-849">Na przykład każdy dziennik utworzony w ramach przetwarzania transakcji może zawierać identyfikator transakcji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-849">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="9c72d-850">Zakres jest `IDisposable` typem, który jest <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> zwracany przez metodę i trwa do momentu jego usunięcia.</span><span class="sxs-lookup"><span data-stu-id="9c72d-850">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="9c72d-851">Użyj zakresu przez zawijania wywołań rejestratora `using` w bloku:</span><span class="sxs-lookup"><span data-stu-id="9c72d-851">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="9c72d-852">Poniższy kod umożliwia zakresy dla dostawcy konsoli:</span><span class="sxs-lookup"><span data-stu-id="9c72d-852">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="9c72d-853">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="9c72d-853">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="9c72d-854">Konfigurowanie `IncludeScopes` opcji rejestratora konsoli jest wymagane, aby włączyć rejestrowanie oparte na zakresie.</span><span class="sxs-lookup"><span data-stu-id="9c72d-854">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="9c72d-855">Aby uzyskać informacje na temat konfiguracji, zobacz [sekcję Konfiguracja.](#configuration)</span><span class="sxs-lookup"><span data-stu-id="9c72d-855">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="9c72d-856">Każdy komunikat dziennika zawiera informacje o zakresie:</span><span class="sxs-lookup"><span data-stu-id="9c72d-856">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="9c72d-857">Wbudowane dostawcy rejestrowania</span><span class="sxs-lookup"><span data-stu-id="9c72d-857">Built-in logging providers</span></span>

<span data-ttu-id="9c72d-858">ASP.NET Core wysyła następujących dostawców:</span><span class="sxs-lookup"><span data-stu-id="9c72d-858">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="9c72d-859">Konsola</span><span class="sxs-lookup"><span data-stu-id="9c72d-859">Console</span></span>](#console-provider)
* [<span data-ttu-id="9c72d-860">Debugowania</span><span class="sxs-lookup"><span data-stu-id="9c72d-860">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="9c72d-861">Eventsource</span><span class="sxs-lookup"><span data-stu-id="9c72d-861">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="9c72d-862">Eventlog</span><span class="sxs-lookup"><span data-stu-id="9c72d-862">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="9c72d-863">TraceSource</span><span class="sxs-lookup"><span data-stu-id="9c72d-863">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="9c72d-864">Plik usługi AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="9c72d-864">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="9c72d-865">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="9c72d-865">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="9c72d-866">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="9c72d-866">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="9c72d-867">Aby uzyskać informacje na temat rejestrowania stdout i debugowania za pomocą ASP.NET Core Module, zobacz <xref:test/troubleshoot-azure-iis> i <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="9c72d-867">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="9c72d-868">Dostawca konsoli</span><span class="sxs-lookup"><span data-stu-id="9c72d-868">Console provider</span></span>

<span data-ttu-id="9c72d-869">Pakiet dostawcy [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) wysyła dane wyjściowe dziennika do konsoli.</span><span class="sxs-lookup"><span data-stu-id="9c72d-869">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="9c72d-870">Aby wyświetlić dane wyjściowe rejestrowania konsoli, otwórz wiersz polecenia w folderze projektu i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="9c72d-870">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="9c72d-871">Dostawca debugowania</span><span class="sxs-lookup"><span data-stu-id="9c72d-871">Debug provider</span></span>

<span data-ttu-id="9c72d-872">Pakiet dostawcy [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje dane wyjściowe dziennika przy użyciu`Debug.WriteLine` klasy [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) ( wywołania metody).</span><span class="sxs-lookup"><span data-stu-id="9c72d-872">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="9c72d-873">W systemie Linux ten dostawca zapisuje dzienniki do */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="9c72d-873">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="9c72d-874">Dostawca źródła zdarzeń</span><span class="sxs-lookup"><span data-stu-id="9c72d-874">Event Source provider</span></span>

<span data-ttu-id="9c72d-875">Pakiet dostawcy [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na międzyplatformie `Microsoft-Extensions-Logging`źródła zdarzeń o nazwie .</span><span class="sxs-lookup"><span data-stu-id="9c72d-875">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="9c72d-876">W systemie Windows dostawca używa [etw](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="9c72d-876">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="9c72d-877">Dostawca źródła zdarzeń jest dodawany automatycznie po `CreateDefaultBuilder` wywołaniu do utworzenia hosta.</span><span class="sxs-lookup"><span data-stu-id="9c72d-877">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="9c72d-878">Narzędzie [PerfView](https://github.com/Microsoft/perfview) służy do zbierania i wyświetlania dzienników.</span><span class="sxs-lookup"><span data-stu-id="9c72d-878">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="9c72d-879">Istnieją inne narzędzia do przeglądania dzienników ETW, ale PerfView zapewnia najlepsze środowisko do pracy ze zdarzeniami ETW emitowanymi przez ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9c72d-879">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="9c72d-880">Aby skonfigurować Program PerfView do zbierania zdarzeń zarejestrowanych przez tego dostawcę, dodaj ten ciąg `*Microsoft-Extensions-Logging` do listy Dostawcy **dodatkowi.**</span><span class="sxs-lookup"><span data-stu-id="9c72d-880">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="9c72d-881">(Nie przegap gwiazdki na początku ciągu.)</span><span class="sxs-lookup"><span data-stu-id="9c72d-881">(Don't miss the asterisk at the start of the string.)</span></span>

![Dodatkowi dostawcy Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="9c72d-883">Dostawca usługi Windows EventLog</span><span class="sxs-lookup"><span data-stu-id="9c72d-883">Windows EventLog provider</span></span>

<span data-ttu-id="9c72d-884">Pakiet dostawcy [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) wysyła dane wyjściowe dziennika do dziennika zdarzeń systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="9c72d-884">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="9c72d-885">[Przeciążenia AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) pozwalają <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>przejść w .</span><span class="sxs-lookup"><span data-stu-id="9c72d-885">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="9c72d-886">Jeśli `null` zostanie określony, używane są następujące ustawienia domyślne:</span><span class="sxs-lookup"><span data-stu-id="9c72d-886">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="9c72d-887">`LogName`&ndash; "Aplikacja"</span><span class="sxs-lookup"><span data-stu-id="9c72d-887">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="9c72d-888">`SourceName`&ndash; ".NET Środowisko uruchomieniowe"</span><span class="sxs-lookup"><span data-stu-id="9c72d-888">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="9c72d-889">`MachineName`&ndash; maszyna lokalna</span><span class="sxs-lookup"><span data-stu-id="9c72d-889">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="9c72d-890">Zdarzenia są rejestrowane dla [poziomu ostrzeżenia i wyższej](#log-level).</span><span class="sxs-lookup"><span data-stu-id="9c72d-890">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="9c72d-891">Aby rejestrować `Warning`zdarzenia niższe niż , jawnie ustawić poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="9c72d-891">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="9c72d-892">Na przykład dodaj następujące elementy do pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="9c72d-892">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="9c72d-893">Dostawca tracesource</span><span class="sxs-lookup"><span data-stu-id="9c72d-893">TraceSource provider</span></span>

<span data-ttu-id="9c72d-894">Pakiet dostawcy [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) używa <xref:System.Diagnostics.TraceSource> bibliotek i dostawców.</span><span class="sxs-lookup"><span data-stu-id="9c72d-894">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="9c72d-895">[AddTraceSource przeciążenia](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umożliwiają przekazywanie w przełączniku źródłowym i odbiornik śledzenia.</span><span class="sxs-lookup"><span data-stu-id="9c72d-895">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="9c72d-896">Aby korzystać z tego dostawcy, aplikacja musi działać w programie .NET Framework (a nie .NET Core).</span><span class="sxs-lookup"><span data-stu-id="9c72d-896">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="9c72d-897">Dostawca może kierować wiadomości do różnych [odbiorników,](/dotnet/framework/debug-trace-profile/trace-listeners)takich jak <xref:System.Diagnostics.TextWriterTraceListener> używane w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-897">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="9c72d-898">Dostawca usługi aplikacji platformy Azure</span><span class="sxs-lookup"><span data-stu-id="9c72d-898">Azure App Service provider</span></span>

<span data-ttu-id="9c72d-899">Pakiet dostawcy [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje dzienniki do plików tekstowych w systemie plików aplikacji usługi Azure App Service i magazynu [obiektów blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) na koncie usługi Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="9c72d-899">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="9c72d-900">Pakiet dostawcy nie jest uwzględniony w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="9c72d-900">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="9c72d-901">Podczas kierowania .NET Framework lub `Microsoft.AspNetCore.App` odwoływania się do metapakiet, dodaj pakiet dostawcy do projektu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-901">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="9c72d-902">Przeciążenie <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> pozwala przejść <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>w .</span><span class="sxs-lookup"><span data-stu-id="9c72d-902">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="9c72d-903">Obiekt ustawień może zastąpić ustawienia domyślne, takie jak szablon danych wyjściowych rejestrowania, nazwa obiektu blob i limit rozmiaru pliku.</span><span class="sxs-lookup"><span data-stu-id="9c72d-903">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="9c72d-904">(Szablon*wyjściowy* to szablon wiadomości, który jest stosowany do wszystkich dzienników `ILogger` oprócz tego, co jest dostarczane z wywołaniem metody.)</span><span class="sxs-lookup"><span data-stu-id="9c72d-904">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="9c72d-905">Podczas wdrażania w aplikacji usługi App Service aplikacja honoruje ustawienia w sekcji [Dzienniki usługi aplikacji](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stronie **Usługi aplikacji** w witrynie Azure portal.</span><span class="sxs-lookup"><span data-stu-id="9c72d-905">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="9c72d-906">Po zaktualizowaniu następujących ustawień zmiany zaczynają obowiązywać natychmiast bez konieczności ponownego uruchamiania lub ponownego wprowadzania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-906">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="9c72d-907">**Rejestrowanie aplikacji (system plików)**</span><span class="sxs-lookup"><span data-stu-id="9c72d-907">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="9c72d-908">**Rejestrowanie aplikacji (obiekt blob)**</span><span class="sxs-lookup"><span data-stu-id="9c72d-908">**Application Logging (Blob)**</span></span>

<span data-ttu-id="9c72d-909">Domyślna lokalizacja plików dziennika znajduje się w folderze *D:\\\\home LogFiles\\Application,* a domyślną nazwą pliku jest *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="9c72d-909">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="9c72d-910">Domyślny limit rozmiaru pliku wynosi 10 MB, a domyślna maksymalna liczba zachowanych plików to 2.</span><span class="sxs-lookup"><span data-stu-id="9c72d-910">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="9c72d-911">Domyślna nazwa obiektu blob to *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="9c72d-911">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="9c72d-912">Dostawca działa tylko wtedy, gdy projekt jest uruchamiany w środowisku platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="9c72d-912">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="9c72d-913">Nie ma wpływu, gdy projekt&mdash;jest uruchamiany lokalnie nie zapisuje do plików lokalnych lub magazynu lokalnego rozwoju dla obiektów blob.</span><span class="sxs-lookup"><span data-stu-id="9c72d-913">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="9c72d-914">Przesyłanie strumieniowe dziennika platformy Azure</span><span class="sxs-lookup"><span data-stu-id="9c72d-914">Azure log streaming</span></span>

<span data-ttu-id="9c72d-915">Przesyłanie strumieniowe dziennika platformy Azure umożliwia wyświetlanie aktywności dziennika w czasie rzeczywistym z:</span><span class="sxs-lookup"><span data-stu-id="9c72d-915">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="9c72d-916">Serwer aplikacji</span><span class="sxs-lookup"><span data-stu-id="9c72d-916">The app server</span></span>
* <span data-ttu-id="9c72d-917">Serwer www</span><span class="sxs-lookup"><span data-stu-id="9c72d-917">The web server</span></span>
* <span data-ttu-id="9c72d-918">Śledzenie żądań nie powiodło się</span><span class="sxs-lookup"><span data-stu-id="9c72d-918">Failed request tracing</span></span>

<span data-ttu-id="9c72d-919">Aby skonfigurować przesyłanie strumieniowe dziennika platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="9c72d-919">To configure Azure log streaming:</span></span>

* <span data-ttu-id="9c72d-920">Przejdź do strony **Dzienniki usługi app service** ze strony portalu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-920">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="9c72d-921">Ustaw **rejestrowanie aplikacji (system plików)** **na Włączone**.</span><span class="sxs-lookup"><span data-stu-id="9c72d-921">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="9c72d-922">Wybierz **poziom**dziennika .</span><span class="sxs-lookup"><span data-stu-id="9c72d-922">Choose the log **Level**.</span></span> <span data-ttu-id="9c72d-923">To ustawienie dotyczy tylko przesyłania strumieniowego dziennika platformy Azure, a nie innych dostawców rejestrowania w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-923">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="9c72d-924">Przejdź do strony **Strumień dziennika,** aby wyświetlić komunikaty aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9c72d-924">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="9c72d-925">Są one rejestrowane przez aplikację `ILogger` za pośrednictwem interfejsu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-925">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="9c72d-926">Rejestrowanie śledzenia usługi Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="9c72d-926">Azure Application Insights trace logging</span></span>

<span data-ttu-id="9c72d-927">Pakiet dostawcy [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje dzienniki w usłudze Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="9c72d-927">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="9c72d-928">Usługa Application Insights to usługa, która monitoruje aplikację sieci web i udostępnia narzędzia do wykonywania zapytań i analizowania danych telemetrycznych.</span><span class="sxs-lookup"><span data-stu-id="9c72d-928">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="9c72d-929">Jeśli używasz tego dostawcy, można kwerendy i analizować dzienniki za pomocą narzędzi usługi Application Insights.</span><span class="sxs-lookup"><span data-stu-id="9c72d-929">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="9c72d-930">Dostawca rejestrowania jest uwzględniony jako zależność [microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), który jest pakietem, który udostępnia wszystkie dostępne dane telemetryczne dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9c72d-930">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="9c72d-931">Jeśli używasz tego pakietu, nie trzeba instalować pakietu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="9c72d-931">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="9c72d-932">Nie należy używać pakietu&mdash; [Microsoft.ApplicationInsights.Web,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) który jest przeznaczony dla ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="9c72d-932">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="9c72d-933">Więcej informacji zawierają następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="9c72d-933">For more information, see the following resources:</span></span>

* [<span data-ttu-id="9c72d-934">Omówienie usługi Application Insights</span><span class="sxs-lookup"><span data-stu-id="9c72d-934">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="9c72d-935">[Usługa Application Insights dla aplikacji ASP.NET Core](/azure/azure-monitor/app/asp-net-core) — uruchom tutaj, jeśli chcesz zaimplementować pełny zakres danych telemetrycznych usługi Application Insights wraz z rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="9c72d-935">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="9c72d-936">[ApplicationInsightsLoggerProvider dla dzienników .NET Core ILogger](/azure/azure-monitor/app/ilogger) — uruchom tutaj, jeśli chcesz zaimplementować dostawcę rejestrowania bez pozostałej części telemetrii usługi Application Insights.</span><span class="sxs-lookup"><span data-stu-id="9c72d-936">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="9c72d-937">[Karty rejestrowania usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="9c72d-937">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="9c72d-938">[Instalowanie, konfigurowanie i inicjowanie zestawu SDK usługi Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) — interaktywny samouczek w witrynie Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="9c72d-938">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="9c72d-939">Zewnętrzni dostawcy usług rejestrowania</span><span class="sxs-lookup"><span data-stu-id="9c72d-939">Third-party logging providers</span></span>

<span data-ttu-id="9c72d-940">Struktury rejestrowania innych firm, które współpracują z ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9c72d-940">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="9c72d-941">[elmah.io](https://elmah.io/) ([Repozytorium GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="9c72d-941">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="9c72d-942">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([Repozytorium GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="9c72d-942">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="9c72d-943">[JSNLog](https://jsnlog.com/) ([Repozytorium GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="9c72d-943">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="9c72d-944">[KissLog.net](https://kisslog.net/) ([Repozytorium GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="9c72d-944">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="9c72d-945">[Log4Net](https://logging.apache.org/log4net/) ([Repozytorium GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="9c72d-945">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="9c72d-946">[Loggr](https://loggr.net/) ([Repozytorium GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="9c72d-946">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="9c72d-947">[NLog](https://nlog-project.org/) ([Repozytorium GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="9c72d-947">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="9c72d-948">[Działko](https://sentry.io/welcome/) [(Repozytorium GitHub)](https://github.com/getsentry/sentry-dotnet)</span><span class="sxs-lookup"><span data-stu-id="9c72d-948">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="9c72d-949">[Serilog](https://serilog.net/) ([Repozytorium GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="9c72d-949">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="9c72d-950">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="9c72d-950">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="9c72d-951">Niektóre struktury innych firm mogą wykonywać [rejestrowanie semantyczne, znane również jako rejestrowanie strukturalne.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)</span><span class="sxs-lookup"><span data-stu-id="9c72d-951">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="9c72d-952">Korzystanie z struktury innej firmy jest podobne do korzystania z jednego z wbudowanych dostawców:</span><span class="sxs-lookup"><span data-stu-id="9c72d-952">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="9c72d-953">Dodaj pakiet NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="9c72d-953">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="9c72d-954">Wywołanie `ILoggerFactory` metody rozszerzenia dostarczone przez strukturę rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="9c72d-954">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="9c72d-955">Aby uzyskać więcej informacji, zobacz dokumentację każdego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="9c72d-955">For more information, see each provider's documentation.</span></span> <span data-ttu-id="9c72d-956">Dostawcy rejestrowania innych firm nie są obsługiwani przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9c72d-956">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9c72d-957">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="9c72d-957">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
