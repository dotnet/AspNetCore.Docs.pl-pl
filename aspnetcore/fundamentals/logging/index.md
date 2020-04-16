---
title: Logowanie do .NET Core i ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak korzystać z struktury rejestrowania dostarczonej przez pakiet Microsoft.Extensions.Logging NuGet.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/logging/index
ms.openlocfilehash: a3c63b738d3eaa51249475b88d78572038348a7a
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440743"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="14485-103">Logowanie do .NET Core i ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="14485-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="14485-104">Przez [Tom Dykstra](https://github.com/tdykstra) i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="14485-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="14485-105">Program .NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi wbudowanymi i zewnętrznymi dostawcami rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14485-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="14485-106">W tym artykule pokazano, jak używać interfejsu API rejestrowania z wbudowanymi dostawcami.</span><span class="sxs-lookup"><span data-stu-id="14485-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="14485-107">Większość przykładów kodu pokazano w tym artykule są z ASP.NET podstawowych aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="14485-108">Części tych fragmentów kodu specyficzne dla rejestrowania mają zastosowanie do dowolnej aplikacji .NET Core, która używa [hosta ogólnego.](xref:fundamentals/host/generic-host)</span><span class="sxs-lookup"><span data-stu-id="14485-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="14485-109">Na przykład sposobu używania hosta ogólnego w aplikacji konsoli niesienia sieci Web zobacz *Program.cs* plik [przykładowej aplikacji Zadania w tle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span><span class="sxs-lookup"><span data-stu-id="14485-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="14485-110">Rejestrowanie kodu dla aplikacji bez ogólnego hosta różni się sposobem [dodawania dostawców](#add-providers) i [tworzenia rejestratorów.](#create-logs)</span><span class="sxs-lookup"><span data-stu-id="14485-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="14485-111">Przykłady kodu niebędącego hostem są wyświetlane w tych sekcjach artykułu.</span><span class="sxs-lookup"><span data-stu-id="14485-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="14485-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="14485-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="14485-113">Dodawanie dostawców</span><span class="sxs-lookup"><span data-stu-id="14485-113">Add providers</span></span>

<span data-ttu-id="14485-114">Dostawca rejestrowania wyświetla lub przechowuje dzienniki.</span><span class="sxs-lookup"><span data-stu-id="14485-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="14485-115">Na przykład dostawca konsoli wyświetla dzienniki na konsoli, a dostawca usługi Azure Application Insights przechowuje je w usłudze Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="14485-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="14485-116">Dzienniki mogą być wysyłane do wielu miejsc docelowych, dodając wielu dostawców.</span><span class="sxs-lookup"><span data-stu-id="14485-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="14485-117">Aby dodać dostawcę w aplikacji korzystającej z hosta `Add{provider name}` ogólnego, należy wywołać metodę rozszerzenia dostawcy w *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="14485-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="14485-118">W aplikacji konsoli nie-hosta, wywołać metodę `Add{provider name}` rozszerzenia `LoggerFactory`dostawcy podczas tworzenia:</span><span class="sxs-lookup"><span data-stu-id="14485-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="14485-119">`LoggerFactory`i `AddConsole` wymagają `using` instrukcji `Microsoft.Extensions.Logging`dla .</span><span class="sxs-lookup"><span data-stu-id="14485-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="14485-120">Wywołać <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>domyślne szablony projektów ASP.NET Core, które dodaje następujących dostawców rejestrowania:</span><span class="sxs-lookup"><span data-stu-id="14485-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="14485-121">Konsola</span><span class="sxs-lookup"><span data-stu-id="14485-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="14485-122">Debugowania</span><span class="sxs-lookup"><span data-stu-id="14485-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="14485-123">Eventsource</span><span class="sxs-lookup"><span data-stu-id="14485-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="14485-124">[EventLog](#windows-eventlog-provider) (tylko w przypadku pracy w systemie Windows)</span><span class="sxs-lookup"><span data-stu-id="14485-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="14485-125">Dostawców domyślnych można zastąpić własnymi wyborami.</span><span class="sxs-lookup"><span data-stu-id="14485-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="14485-126">Zadzwoń <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>i dodaj dostawców, których chcesz.</span><span class="sxs-lookup"><span data-stu-id="14485-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="14485-127">Dowiedz się więcej o [wbudowanych dostawców rejestrowania](#built-in-logging-providers) i [dostawców rejestrowania innych firm](#third-party-logging-providers) w dalszej części artykułu.</span><span class="sxs-lookup"><span data-stu-id="14485-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="14485-128">Tworzenie dzienników</span><span class="sxs-lookup"><span data-stu-id="14485-128">Create logs</span></span>

<span data-ttu-id="14485-129">Aby utworzyć dzienniki, <xref:Microsoft.Extensions.Logging.ILogger%601> użyj obiektu.</span><span class="sxs-lookup"><span data-stu-id="14485-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="14485-130">W aplikacji sieci web lub usługi `ILogger` hostowanej, uzyskać z iniekcji zależności (DI).</span><span class="sxs-lookup"><span data-stu-id="14485-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="14485-131">W aplikacjach konsoli nie-hosta użyj go `LoggerFactory` do utworzenia pliku `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="14485-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="14485-132">Poniższy przykład ASP.NET Core tworzy rejestrator `TodoApiSample.Pages.AboutModel` z jako kategorii.</span><span class="sxs-lookup"><span data-stu-id="14485-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="14485-133">Kategoria *dziennika* jest ciągiem skojarzonym z każdym dziennikiem.</span><span class="sxs-lookup"><span data-stu-id="14485-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="14485-134">Wystąpienie `ILogger<T>` dostarczone przez DI tworzy dzienniki, które `T` mają w pełni kwalifikowaną nazwę typu jako kategorii.</span><span class="sxs-lookup"><span data-stu-id="14485-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="14485-135">Poniższy przykład aplikacji konsoli nie-hosta `LoggingConsoleApp.Program` tworzy rejestrator z jako kategorii.</span><span class="sxs-lookup"><span data-stu-id="14485-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="14485-136">W poniższych ASP.NET przykłady aplikacji Core i konsoli rejestrator jest używany `Information` do tworzenia dzienników z jako poziom.</span><span class="sxs-lookup"><span data-stu-id="14485-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="14485-137">Poziom *dziennika* wskazuje ważność zarejestrowanego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="14485-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="14485-138">[Poziomy](#log-level) i kategorie są [wyjaśnione](#log-category) bardziej szczegółowo w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="14485-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="14485-139">Tworzenie dzienników w klasie Program</span><span class="sxs-lookup"><span data-stu-id="14485-139">Create logs in the Program class</span></span>

<span data-ttu-id="14485-140">Aby napisać dzienniki `Program` w klasie aplikacji ASP.NET Core, `ILogger` pobierz wystąpienie z DI po zbudowaniu hosta:</span><span class="sxs-lookup"><span data-stu-id="14485-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="14485-141">Rejestrowanie podczas budowy hosta nie jest bezpośrednio obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="14485-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="14485-142">Można jednak użyć oddzielnego rejestratora.</span><span class="sxs-lookup"><span data-stu-id="14485-142">However, a separate logger can be used.</span></span> <span data-ttu-id="14485-143">W poniższym przykładzie [rejestrator Serilog](https://serilog.net/) jest `CreateHostBuilder`używany do logowania .</span><span class="sxs-lookup"><span data-stu-id="14485-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="14485-144">`AddSerilog`wykorzystuje konfigurację statyczną `Log.Logger`określoną w:</span><span class="sxs-lookup"><span data-stu-id="14485-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="14485-145">Tworzenie dzienników w klasie Uruchamiani</span><span class="sxs-lookup"><span data-stu-id="14485-145">Create logs in the Startup class</span></span>

<span data-ttu-id="14485-146">Aby zapisać dzienniki `Startup.Configure` w metodzie aplikacji ASP.NET Core, `ILogger` dołącz parametr do podpisu metody:</span><span class="sxs-lookup"><span data-stu-id="14485-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="14485-147">Zapisywanie dzienników przed zakończeniem konfiguracji `Startup.ConfigureServices` kontenera DI w metodzie nie jest obsługiwane:</span><span class="sxs-lookup"><span data-stu-id="14485-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="14485-148">Logger wtrysku do konstruktora `Startup` nie jest obsługiwany.</span><span class="sxs-lookup"><span data-stu-id="14485-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="14485-149">Wtrysk `Startup.ConfigureServices` rejestratora do podpisu metody nie jest obsługiwany</span><span class="sxs-lookup"><span data-stu-id="14485-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="14485-150">Powodem tego ograniczenia jest to, że rejestrowanie zależy od DI i konfiguracji, co z kolei zależy od DI.</span><span class="sxs-lookup"><span data-stu-id="14485-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="14485-151">Kontener DI nie jest skonfigurowany do zakończenia. `ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="14485-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="14485-152">Inicjowanie konstruktora rejestratora do `Startup` prac we wcześniejszych wersjach ASP.NET Core, ponieważ dla hosta sieci Web jest tworzony oddzielny kontener DI.</span><span class="sxs-lookup"><span data-stu-id="14485-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="14485-153">Aby uzyskać informacje o tym, dlaczego dla hosta ogólnego tworzony jest tylko jeden kontener, zobacz [anons zmiany podziału](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="14485-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="14485-154">Jeśli trzeba skonfigurować usługę, która `ILogger<T>`zależy od , nadal można to zrobić za pomocą wtrysku konstruktora lub pod warunkiem, że metoda fabryczna.</span><span class="sxs-lookup"><span data-stu-id="14485-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="14485-155">Podejście metody fabrycznej jest zalecane tylko wtedy, gdy nie ma innej opcji.</span><span class="sxs-lookup"><span data-stu-id="14485-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="14485-156">Załóżmy na przykład, że musisz wypełnić właściwość usługą z DI:</span><span class="sxs-lookup"><span data-stu-id="14485-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="14485-157">Poprzedni wyróżniony kod `Func` jest, który uruchamia po raz pierwszy kontener `MyService`DI musi skonstruować wystąpienie .</span><span class="sxs-lookup"><span data-stu-id="14485-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="14485-158">W ten sposób można uzyskać dostęp do dowolnej z zarejestrowanych usług.</span><span class="sxs-lookup"><span data-stu-id="14485-158">You can access any of the registered services in this way.</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="14485-159">Brak metod rejestratora asynchronii</span><span class="sxs-lookup"><span data-stu-id="14485-159">No asynchronous logger methods</span></span>

<span data-ttu-id="14485-160">Rejestrowanie powinno być tak szybkie, że nie jest warte kosztu wydajności kodu asynchroniowego.</span><span class="sxs-lookup"><span data-stu-id="14485-160">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="14485-161">Jeśli magazyn danych rejestrowania jest powolny, nie zapisuj do niego bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="14485-161">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="14485-162">Należy rozważyć zapisanie komunikatów dziennika do szybkiego magazynu początkowo, a następnie przenieść je do wolnego magazynu później.</span><span class="sxs-lookup"><span data-stu-id="14485-162">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="14485-163">Na przykład jeśli logujesz się do programu SQL Server, nie chcesz tego `Log` robić bezpośrednio `Log` w metodzie, ponieważ metody są synchroniczne.</span><span class="sxs-lookup"><span data-stu-id="14485-163">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="14485-164">Zamiast tego synchronicznie dodać komunikaty dziennika do kolejki w pamięci i mieć proces roboczy w tle wyciągnąć wiadomości z kolejki, aby wykonać asynchronicznie pracy wypychania danych do programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="14485-164">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="14485-165">Aby uzyskać więcej informacji, zobacz [ten](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problem z githubem.</span><span class="sxs-lookup"><span data-stu-id="14485-165">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="14485-166">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="14485-166">Configuration</span></span>

<span data-ttu-id="14485-167">Konfiguracja dostawcy rejestrowania jest dostarczana przez jednego lub więcej dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="14485-167">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="14485-168">Formaty plików (INI, JSON i XML).</span><span class="sxs-lookup"><span data-stu-id="14485-168">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="14485-169">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="14485-169">Command-line arguments.</span></span>
* <span data-ttu-id="14485-170">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="14485-170">Environment variables.</span></span>
* <span data-ttu-id="14485-171">W pamięci obiektów .NET.</span><span class="sxs-lookup"><span data-stu-id="14485-171">In-memory .NET objects.</span></span>
* <span data-ttu-id="14485-172">Niezaszyfrowany magazyn [Menedżera tajnych.](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="14485-172">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="14485-173">Zaszyfrowany magazyn użytkowników, taki jak [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="14485-173">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="14485-174">Dostawcy niestandardowi (zainstalowane lub utworzone).</span><span class="sxs-lookup"><span data-stu-id="14485-174">Custom providers (installed or created).</span></span>

<span data-ttu-id="14485-175">Na przykład konfiguracja rejestrowania jest `Logging` często dostarczana przez sekcję plików ustawień aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-175">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="14485-176">W poniższym przykładzie przedstawiono zawartość typowych *appsettings. Plik Development.json:*</span><span class="sxs-lookup"><span data-stu-id="14485-176">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="14485-177">Właściwość `Logging` może `LogLevel` mieć i rejestrować właściwości dostawcy (konsola jest wyświetlana).</span><span class="sxs-lookup"><span data-stu-id="14485-177">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="14485-178">Właściwość `LogLevel` `Logging` w obszarze określa minimalny [poziom](#log-level) do dziennika dla wybranych kategorii.</span><span class="sxs-lookup"><span data-stu-id="14485-178">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="14485-179">W przykładzie `System` `Microsoft` i kategorii `Information` dziennika na poziomie, `Debug` a wszystkie inne dziennika na poziomie.</span><span class="sxs-lookup"><span data-stu-id="14485-179">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="14485-180">Inne właściwości `Logging` w obszarze określić dostawców rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14485-180">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="14485-181">Przykład jest dla dostawcy konsoli.</span><span class="sxs-lookup"><span data-stu-id="14485-181">The example is for the Console provider.</span></span> <span data-ttu-id="14485-182">Jeśli dostawca obsługuje [zakresy dziennika,](#log-scopes) `IncludeScopes` wskazuje, czy są one włączone.</span><span class="sxs-lookup"><span data-stu-id="14485-182">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="14485-183">Właściwość dostawcy (na przykład `Console` w przykładzie) może również określić `LogLevel` właściwość.</span><span class="sxs-lookup"><span data-stu-id="14485-183">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="14485-184">`LogLevel`pod dostawcą określa poziomy do zalogowania dla tego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="14485-184">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="14485-185">Jeśli poziomy są `Logging.{providername}.LogLevel`określone w , zastępują wszystko ustawione w `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="14485-185">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="14485-186">Interfejs API rejestrowania nie zawiera scenariusza, aby zmienić poziomy dziennika, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="14485-186">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="14485-187">Jednak niektórzy dostawcy konfiguracji są w stanie przeładować konfigurację, co ma natychmiastowy wpływ na konfigurację rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14485-187">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="14485-188">Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider), `CreateDefaultBuilder` który jest dodawany przez pliki ustawień odczytu, domyślnie ładuje konfigurację rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14485-188">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="14485-189">Jeśli konfiguracja zostanie zmieniona w kodzie, gdy aplikacja jest uruchomiona, aplikacja może wywołać [IConfigurationRoot.Reload,](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) aby zaktualizować konfigurację rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-189">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="14485-190">Aby uzyskać informacje na temat <xref:fundamentals/configuration/index>wdrażania dostawców konfiguracji, zobacz .</span><span class="sxs-lookup"><span data-stu-id="14485-190">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="14485-191">Przykładowe dane wyjściowe rejestrowania</span><span class="sxs-lookup"><span data-stu-id="14485-191">Sample logging output</span></span>

<span data-ttu-id="14485-192">Po wyświetleniu przykładowego kodu w poprzedniej sekcji dzienniki są wyświetlane w konsoli, gdy aplikacja jest uruchamiana z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="14485-192">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="14485-193">Oto przykład danych wyjściowych konsoli:</span><span class="sxs-lookup"><span data-stu-id="14485-193">Here's an example of console output:</span></span>

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

<span data-ttu-id="14485-194">Poprzednie dzienniki zostały wygenerowane przez wykonanie żądania HTTP `http://localhost:5000/api/todo/0`Get do przykładowej aplikacji o godzinie .</span><span class="sxs-lookup"><span data-stu-id="14485-194">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="14485-195">Oto przykład tych samych dzienników, które pojawiają się w oknie debugowania po uruchomieniu przykładowej aplikacji w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="14485-195">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="14485-196">Dzienniki, które są `ILogger` tworzone przez wywołania wyświetlane w poprzedniej sekcji zaczynają się od "TodoApiSample".</span><span class="sxs-lookup"><span data-stu-id="14485-196">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="14485-197">Dzienniki, które zaczynają się od kategorii "Microsoft" pochodzą z ASP.NET kodu frameworka Core.</span><span class="sxs-lookup"><span data-stu-id="14485-197">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="14485-198">ASP.NET Core i kod aplikacji są przy użyciu tego samego interfejsu API rejestrowania i dostawców.</span><span class="sxs-lookup"><span data-stu-id="14485-198">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="14485-199">Poniżej dalsza część artykułu Wyjaśniono niektóre szczegóły i opcje rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14485-199">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="14485-200">Pakiety NuGet</span><span class="sxs-lookup"><span data-stu-id="14485-200">NuGet packages</span></span>

<span data-ttu-id="14485-201">`ILogger` Interfejsy `ILoggerFactory` i interfejsy znajdują się w witrynie [Microsoft.Extensions.Logging.Abstractions,](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a domyślne implementacje dla nich znajdują się w [witrynie Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="14485-201">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="14485-202">Kategoria dziennika</span><span class="sxs-lookup"><span data-stu-id="14485-202">Log category</span></span>

<span data-ttu-id="14485-203">Podczas `ILogger` tworzenia obiektu określana jest dla niego *kategoria.*</span><span class="sxs-lookup"><span data-stu-id="14485-203">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="14485-204">Ta kategoria jest dołączona do każdej wiadomości `ILogger`dziennika utworzonej przez to wystąpienie .</span><span class="sxs-lookup"><span data-stu-id="14485-204">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="14485-205">Kategoria może być dowolny ciąg, ale konwencja jest użycie nazwy klasy, takich jak "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="14485-205">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="14485-206">Użyj, `ILogger<T>` aby `ILogger` uzyskać wystąpienie, które używa `T` w pełni kwalifikowanej nazwy typu jako kategorii:</span><span class="sxs-lookup"><span data-stu-id="14485-206">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="14485-207">Aby jawnie określić kategorię, zadzwoń: `ILoggerFactory.CreateLogger`</span><span class="sxs-lookup"><span data-stu-id="14485-207">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="14485-208">`ILogger<T>`jest odpowiednikiem `CreateLogger` wywołania z w `T`pełni kwalifikowaną nazwą typu .</span><span class="sxs-lookup"><span data-stu-id="14485-208">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="14485-209">Poziom dziennika</span><span class="sxs-lookup"><span data-stu-id="14485-209">Log level</span></span>

<span data-ttu-id="14485-210">Każdy dziennik określa <xref:Microsoft.Extensions.Logging.LogLevel> wartość.</span><span class="sxs-lookup"><span data-stu-id="14485-210">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="14485-211">Poziom dziennika wskazuje ważność lub ważność.</span><span class="sxs-lookup"><span data-stu-id="14485-211">The log level indicates the severity or importance.</span></span> <span data-ttu-id="14485-212">Na przykład można napisać `Information` dziennik, gdy metoda kończy `Warning` się normalnie i dziennika, gdy metoda zwraca kod stanu *404 Nie znaleziono.*</span><span class="sxs-lookup"><span data-stu-id="14485-212">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="14485-213">Następujący kod `Information` tworzy `Warning` i rejestruje:</span><span class="sxs-lookup"><span data-stu-id="14485-213">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="14485-214">W poprzednim kodzie pierwszym parametrem jest [identyfikator zdarzenia Log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="14485-214">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="14485-215">Drugi parametr jest szablonem wiadomości z symbolami zastępczymi dla wartości argumentów dostarczonych przez pozostałe parametry metody.</span><span class="sxs-lookup"><span data-stu-id="14485-215">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="14485-216">Parametry metody są wyjaśnione w [sekcji szablonu wiadomości](#log-message-template) w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="14485-216">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="14485-217">Metody dziennika, które zawierają poziom w nazwie `LogInformation` `LogWarning`metody (na przykład i ) są [metody rozszerzenia dla ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="14485-217">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="14485-218">Te metody `Log` wywołać metodę, która przyjmuje `LogLevel` parametr.</span><span class="sxs-lookup"><span data-stu-id="14485-218">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="14485-219">Można wywołać `Log` metodę bezpośrednio, a nie jedną z tych metod rozszerzenia, ale składnia jest stosunkowo skomplikowana.</span><span class="sxs-lookup"><span data-stu-id="14485-219">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="14485-220">Aby uzyskać więcej <xref:Microsoft.Extensions.Logging.ILogger> informacji, zobacz kod [źródłowy rozszerzeń rejestratora](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="14485-220">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="14485-221">ASP.NET Core definiuje następujące poziomy dziennika, uporządkowane w tym miejscu od najniższej do najwyższej ważności.</span><span class="sxs-lookup"><span data-stu-id="14485-221">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="14485-222">Ślad = 0</span><span class="sxs-lookup"><span data-stu-id="14485-222">Trace = 0</span></span>

  <span data-ttu-id="14485-223">Aby uzyskać informacje, które są zazwyczaj cenne tylko do debugowania.</span><span class="sxs-lookup"><span data-stu-id="14485-223">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="14485-224">Te komunikaty mogą zawierać poufne dane aplikacji, a więc nie powinny być włączone w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="14485-224">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="14485-225">*Domyślnie wyłączone.*</span><span class="sxs-lookup"><span data-stu-id="14485-225">*Disabled by default.*</span></span>

* <span data-ttu-id="14485-226">Debugowanie = 1</span><span class="sxs-lookup"><span data-stu-id="14485-226">Debug = 1</span></span>

  <span data-ttu-id="14485-227">Aby uzyskać informacje, które mogą być przydatne w rozwoju i debugowania.</span><span class="sxs-lookup"><span data-stu-id="14485-227">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="14485-228">Przykład: `Entering method Configure with flag set to true.` `Debug` Włącz logi poziomu w produkcji tylko podczas rozwiązywania problemów, ze względu na dużą liczbę dzienników.</span><span class="sxs-lookup"><span data-stu-id="14485-228">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="14485-229">Informacje = 2</span><span class="sxs-lookup"><span data-stu-id="14485-229">Information = 2</span></span>

  <span data-ttu-id="14485-230">Do śledzenia ogólnego przepływu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-230">For tracking the general flow of the app.</span></span> <span data-ttu-id="14485-231">Te dzienniki zazwyczaj mają pewną wartość długoterminową.</span><span class="sxs-lookup"><span data-stu-id="14485-231">These logs typically have some long-term value.</span></span> <span data-ttu-id="14485-232">Przykład: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="14485-232">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="14485-233">Ostrzeżenie = 3</span><span class="sxs-lookup"><span data-stu-id="14485-233">Warning = 3</span></span>

  <span data-ttu-id="14485-234">W przypadku nieprawidłowych lub nieoczekiwanych zdarzeń w przepływie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-234">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="14485-235">Mogą to być błędy lub inne warunki, które nie powodują zatrzymania aplikacji, ale mogą wymagać zbadania.</span><span class="sxs-lookup"><span data-stu-id="14485-235">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="14485-236">Obsługiwane wyjątki są wspólne miejsce `Warning` do korzystania z poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="14485-236">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="14485-237">Przykład: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="14485-237">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="14485-238">Błąd = 4</span><span class="sxs-lookup"><span data-stu-id="14485-238">Error = 4</span></span>

  <span data-ttu-id="14485-239">Dla błędów i wyjątków, które nie mogą być obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="14485-239">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="14485-240">Te komunikaty wskazują na błąd w bieżącym działaniu lub operacji (takich jak bieżące żądanie HTTP), a nie błąd całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-240">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="14485-241">Przykładowy komunikat dziennika:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="14485-241">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="14485-242">Krytyczny = 5</span><span class="sxs-lookup"><span data-stu-id="14485-242">Critical = 5</span></span>

  <span data-ttu-id="14485-243">W przypadku awarii, które wymagają natychmiastowej uwagi.</span><span class="sxs-lookup"><span data-stu-id="14485-243">For failures that require immediate attention.</span></span> <span data-ttu-id="14485-244">Przykłady: scenariusze utraty danych, brak miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="14485-244">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="14485-245">Użyj poziomu dziennika, aby kontrolować, ile danych wyjściowych dziennika jest zapisywany na określonym nośniku pamięci lub oknie wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="14485-245">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="14485-246">Przykład:</span><span class="sxs-lookup"><span data-stu-id="14485-246">For example:</span></span>

* <span data-ttu-id="14485-247">W produkcji:</span><span class="sxs-lookup"><span data-stu-id="14485-247">In production:</span></span>
  * <span data-ttu-id="14485-248">Rejestrowanie na `Trace` `Information` poziomach za pośrednictwem tworzy dużą ilość szczegółowych komunikatów dziennika.</span><span class="sxs-lookup"><span data-stu-id="14485-248">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="14485-249">Aby kontrolować koszty i nie przekraczać `Information` limitów magazynowania danych, rejestruj `Trace` komunikaty poziomu do magazynu danych o dużej objętości i niskich kosztach.</span><span class="sxs-lookup"><span data-stu-id="14485-249">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="14485-250">Rejestrowanie `Warning` na `Critical` poziomach za pośrednictwem zazwyczaj generuje mniej, mniejsze komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="14485-250">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="14485-251">W związku z tym koszty i limity magazynowania zwykle nie są problemem, co skutkuje większą elastycznością wyboru magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="14485-251">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="14485-252">Podczas rozwoju:</span><span class="sxs-lookup"><span data-stu-id="14485-252">During development:</span></span>
  * <span data-ttu-id="14485-253">Zaloguj `Warning` `Critical` się za pośrednictwem wiadomości do konsoli.</span><span class="sxs-lookup"><span data-stu-id="14485-253">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="14485-254">Dodaj `Trace` `Information` za pośrednictwem wiadomości podczas rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="14485-254">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="14485-255">[Sekcja filtrowania dziennika](#log-filtering) w dalszej części tego artykułu wyjaśnia, jak kontrolować poziomy dziennika, które obsługuje dostawca.</span><span class="sxs-lookup"><span data-stu-id="14485-255">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="14485-256">ASP.NET Core zapisuje dzienniki dla zdarzeń struktury.</span><span class="sxs-lookup"><span data-stu-id="14485-256">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="14485-257">Przykłady dziennika wcześniej w tym artykule wykluczone dzienniki poniżej `Information` poziomu, więc nie `Debug` lub `Trace` poziom dzienniki zostały utworzone.</span><span class="sxs-lookup"><span data-stu-id="14485-257">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="14485-258">Oto przykład dzienników konsoli wyprodukowanych przez uruchomienie przykładowej `Debug` aplikacji skonfigurowanej do pokazywalek dzienników:</span><span class="sxs-lookup"><span data-stu-id="14485-258">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="14485-259">Identyfikator zdarzenia dziennika</span><span class="sxs-lookup"><span data-stu-id="14485-259">Log event ID</span></span>

<span data-ttu-id="14485-260">Każdy dziennik może określić *identyfikator zdarzenia*.</span><span class="sxs-lookup"><span data-stu-id="14485-260">Each log can specify an *event ID*.</span></span> <span data-ttu-id="14485-261">Przykładowa aplikacja robi to przy `LoggingEvents` użyciu klasy zdefiniowanej lokalnie:</span><span class="sxs-lookup"><span data-stu-id="14485-261">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="14485-262">Identyfikator zdarzenia kojarzy zestaw zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="14485-262">An event ID associates a set of events.</span></span> <span data-ttu-id="14485-263">Na przykład wszystkie dzienniki związane z wyświetlaniem listy elementów na stronie może być 1001.</span><span class="sxs-lookup"><span data-stu-id="14485-263">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="14485-264">Dostawca rejestrowania może przechowywać identyfikator zdarzenia w polu identyfikator, w komunikacie rejestrowania lub w ogóle.</span><span class="sxs-lookup"><span data-stu-id="14485-264">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="14485-265">Dostawca debugowania nie pokazuje identyfikatorów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="14485-265">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="14485-266">Dostawca konsoli wyświetla identyfikatory zdarzeń w nawiasach po kategorii:</span><span class="sxs-lookup"><span data-stu-id="14485-266">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="14485-267">Szablon komunikatu dziennika</span><span class="sxs-lookup"><span data-stu-id="14485-267">Log message template</span></span>

<span data-ttu-id="14485-268">Każdy dziennik określa szablon wiadomości.</span><span class="sxs-lookup"><span data-stu-id="14485-268">Each log specifies a message template.</span></span> <span data-ttu-id="14485-269">Szablon wiadomości może zawierać symbole zastępcze, dla których są dostarczane argumenty.</span><span class="sxs-lookup"><span data-stu-id="14485-269">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="14485-270">Użyj nazw symboli zastępczych, a nie liczb.</span><span class="sxs-lookup"><span data-stu-id="14485-270">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="14485-271">Kolejność symboli zastępczych, a nie ich nazw, określa, które parametry są używane do dostarczania ich wartości.</span><span class="sxs-lookup"><span data-stu-id="14485-271">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="14485-272">W poniższym kodzie należy zauważyć, że nazwy parametrów są niesekwencjonowane w szablonie wiadomości:</span><span class="sxs-lookup"><span data-stu-id="14485-272">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="14485-273">Ten kod tworzy komunikat dziennika z wartościami parametrów w sekwencji:</span><span class="sxs-lookup"><span data-stu-id="14485-273">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="14485-274">Struktura rejestrowania działa w ten sposób, dzięki czemu dostawcy rejestrowania mogą zaimplementować [rejestrowanie semantyczne, znane również jako rejestrowanie strukturalne.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)</span><span class="sxs-lookup"><span data-stu-id="14485-274">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="14485-275">Same argumenty są przekazywane do systemu rejestrowania, a nie tylko do sformatowanego szablonu wiadomości.</span><span class="sxs-lookup"><span data-stu-id="14485-275">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="14485-276">Te informacje umożliwiają dostawcom rejestrowania przechowywanie wartości parametrów jako pól.</span><span class="sxs-lookup"><span data-stu-id="14485-276">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="14485-277">Załóżmy na przykład, że wywołania metody rejestratora wyglądają następująco:</span><span class="sxs-lookup"><span data-stu-id="14485-277">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="14485-278">Jeśli wysyłasz dzienniki do usługi Azure Table Storage, `ID` każda `RequestTime` jednostka tabeli platformy Azure może mieć i właściwości, co upraszcza zapytania dotyczące danych dziennika.</span><span class="sxs-lookup"><span data-stu-id="14485-278">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="14485-279">Kwerenda może znaleźć wszystkie dzienniki w określonym `RequestTime` zakresie bez analizowania czasu poza wiadomością tekstową.</span><span class="sxs-lookup"><span data-stu-id="14485-279">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="14485-280">Rejestrowanie wyjątków</span><span class="sxs-lookup"><span data-stu-id="14485-280">Logging exceptions</span></span>

<span data-ttu-id="14485-281">Metody rejestratora mają przeciążenia, które umożliwiają przekazywanie w wyjątku, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="14485-281">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="14485-282">Różni dostawcy obsługują informacje o wyjątkach na różne sposoby.</span><span class="sxs-lookup"><span data-stu-id="14485-282">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="14485-283">Oto przykład danych wyjściowych dostawcy debugowania z kodu pokazanego powyżej.</span><span class="sxs-lookup"><span data-stu-id="14485-283">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="14485-284">Filtrowanie dzienników</span><span class="sxs-lookup"><span data-stu-id="14485-284">Log filtering</span></span>

<span data-ttu-id="14485-285">Można określić minimalny poziom dziennika dla określonego dostawcy i kategorii lub dla wszystkich dostawców lub wszystkich kategorii.</span><span class="sxs-lookup"><span data-stu-id="14485-285">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="14485-286">Wszelkie dzienniki poniżej minimalnego poziomu nie są przekazywane do tego dostawcy, więc nie są wyświetlane ani przechowywane.</span><span class="sxs-lookup"><span data-stu-id="14485-286">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="14485-287">Aby pominąć wszystkie `LogLevel.None` dzienniki, należy określić jako minimalny poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="14485-287">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="14485-288">Wartość całkowita `LogLevel.None` wynosi 6, która jest `LogLevel.Critical` wyższa niż (5).</span><span class="sxs-lookup"><span data-stu-id="14485-288">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="14485-289">Tworzenie reguł filtrowania w konfiguracji</span><span class="sxs-lookup"><span data-stu-id="14485-289">Create filter rules in configuration</span></span>

<span data-ttu-id="14485-290">Kod szablonu `CreateDefaultBuilder` projektu wywołuje skonfigurowanie rejestrowania dla dostawców konsoli, debugowania i źródła zdarzeń (ASP.NET Core 2.2 lub nowszych).</span><span class="sxs-lookup"><span data-stu-id="14485-290">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="14485-291">Metoda `CreateDefaultBuilder` konfiguruje rejestrowanie w celu `Logging` wyszukywania konfiguracji w sekcji, jak wyjaśniono [wcześniej w tym artykule](#configuration).</span><span class="sxs-lookup"><span data-stu-id="14485-291">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="14485-292">Dane konfiguracyjne określają minimalne poziomy dziennika według dostawcy i kategorii, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="14485-292">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="14485-293">Ten JSON tworzy sześć reguł filtrowania: jeden dla dostawcy debugowania, cztery dla dostawcy konsoli i jeden dla wszystkich dostawców.</span><span class="sxs-lookup"><span data-stu-id="14485-293">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="14485-294">Pojedyncza reguła jest wybierana `ILogger` dla każdego dostawcy podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="14485-294">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="14485-295">Reguły filtrowania w kodzie</span><span class="sxs-lookup"><span data-stu-id="14485-295">Filter rules in code</span></span>

<span data-ttu-id="14485-296">W poniższym przykładzie pokazano, jak zarejestrować reguły filtrowania w kodzie:</span><span class="sxs-lookup"><span data-stu-id="14485-296">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="14485-297">Drugi `AddFilter` określa dostawcę debugowania przy użyciu jego nazwy typu.</span><span class="sxs-lookup"><span data-stu-id="14485-297">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="14485-298">Pierwszy `AddFilter` dotyczy wszystkich dostawców, ponieważ nie określa typu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="14485-298">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="14485-299">Jak stosowane są reguły filtrowania</span><span class="sxs-lookup"><span data-stu-id="14485-299">How filtering rules are applied</span></span>

<span data-ttu-id="14485-300">Dane konfiguracji i `AddFilter` kod pokazany w poprzednich przykładach tworzą reguły pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="14485-300">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="14485-301">Pierwsze sześć pochodzą z przykładu konfiguracji i dwa ostatnie pochodzą z przykładu kodu.</span><span class="sxs-lookup"><span data-stu-id="14485-301">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="14485-302">Liczba</span><span class="sxs-lookup"><span data-stu-id="14485-302">Number</span></span> | <span data-ttu-id="14485-303">Dostawca</span><span class="sxs-lookup"><span data-stu-id="14485-303">Provider</span></span>      | <span data-ttu-id="14485-304">Kategorie, które zaczynają się od ...</span><span class="sxs-lookup"><span data-stu-id="14485-304">Categories that begin with ...</span></span>          | <span data-ttu-id="14485-305">Minimalny poziom dziennika</span><span class="sxs-lookup"><span data-stu-id="14485-305">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="14485-306">1</span><span class="sxs-lookup"><span data-stu-id="14485-306">1</span></span>      | <span data-ttu-id="14485-307">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="14485-307">Debug</span></span>         | <span data-ttu-id="14485-308">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="14485-308">All categories</span></span>                          | <span data-ttu-id="14485-309">Informacje</span><span class="sxs-lookup"><span data-stu-id="14485-309">Information</span></span>       |
| <span data-ttu-id="14485-310">2</span><span class="sxs-lookup"><span data-stu-id="14485-310">2</span></span>      | <span data-ttu-id="14485-311">Konsola</span><span class="sxs-lookup"><span data-stu-id="14485-311">Console</span></span>       | <span data-ttu-id="14485-312">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="14485-312">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="14485-313">Ostrzeżenie</span><span class="sxs-lookup"><span data-stu-id="14485-313">Warning</span></span>           |
| <span data-ttu-id="14485-314">3</span><span class="sxs-lookup"><span data-stu-id="14485-314">3</span></span>      | <span data-ttu-id="14485-315">Konsola</span><span class="sxs-lookup"><span data-stu-id="14485-315">Console</span></span>       | <span data-ttu-id="14485-316">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="14485-316">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="14485-317">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="14485-317">Debug</span></span>             |
| <span data-ttu-id="14485-318">4</span><span class="sxs-lookup"><span data-stu-id="14485-318">4</span></span>      | <span data-ttu-id="14485-319">Konsola</span><span class="sxs-lookup"><span data-stu-id="14485-319">Console</span></span>       | <span data-ttu-id="14485-320">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="14485-320">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="14485-321">Błąd</span><span class="sxs-lookup"><span data-stu-id="14485-321">Error</span></span>             |
| <span data-ttu-id="14485-322">5</span><span class="sxs-lookup"><span data-stu-id="14485-322">5</span></span>      | <span data-ttu-id="14485-323">Konsola</span><span class="sxs-lookup"><span data-stu-id="14485-323">Console</span></span>       | <span data-ttu-id="14485-324">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="14485-324">All categories</span></span>                          | <span data-ttu-id="14485-325">Informacje</span><span class="sxs-lookup"><span data-stu-id="14485-325">Information</span></span>       |
| <span data-ttu-id="14485-326">6</span><span class="sxs-lookup"><span data-stu-id="14485-326">6</span></span>      | <span data-ttu-id="14485-327">Wszyscy dostawcy</span><span class="sxs-lookup"><span data-stu-id="14485-327">All providers</span></span> | <span data-ttu-id="14485-328">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="14485-328">All categories</span></span>                          | <span data-ttu-id="14485-329">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="14485-329">Debug</span></span>             |
| <span data-ttu-id="14485-330">7</span><span class="sxs-lookup"><span data-stu-id="14485-330">7</span></span>      | <span data-ttu-id="14485-331">Wszyscy dostawcy</span><span class="sxs-lookup"><span data-stu-id="14485-331">All providers</span></span> | <span data-ttu-id="14485-332">System</span><span class="sxs-lookup"><span data-stu-id="14485-332">System</span></span>                                  | <span data-ttu-id="14485-333">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="14485-333">Debug</span></span>             |
| <span data-ttu-id="14485-334">8</span><span class="sxs-lookup"><span data-stu-id="14485-334">8</span></span>      | <span data-ttu-id="14485-335">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="14485-335">Debug</span></span>         | <span data-ttu-id="14485-336">Microsoft</span><span class="sxs-lookup"><span data-stu-id="14485-336">Microsoft</span></span>                               | <span data-ttu-id="14485-337">Ślad</span><span class="sxs-lookup"><span data-stu-id="14485-337">Trace</span></span>             |

<span data-ttu-id="14485-338">Po `ILogger` utworzeniu `ILoggerFactory` obiektu obiekt wybiera jedną regułę na dostawcę, aby zastosować do tego rejestratora.</span><span class="sxs-lookup"><span data-stu-id="14485-338">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="14485-339">Wszystkie wiadomości napisane `ILogger` przez wystąpienie są filtrowane na podstawie wybranych reguł.</span><span class="sxs-lookup"><span data-stu-id="14485-339">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="14485-340">Najbardziej specyficzna reguła możliwa dla każdego dostawcy i pary kategorii jest wybierana z dostępnych reguł.</span><span class="sxs-lookup"><span data-stu-id="14485-340">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="14485-341">Następujący algorytm jest używany dla `ILogger` każdego dostawcy, gdy jest tworzony dla danej kategorii:</span><span class="sxs-lookup"><span data-stu-id="14485-341">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="14485-342">Wybierz wszystkie reguły, które pasują do dostawcy lub jego aliasu.</span><span class="sxs-lookup"><span data-stu-id="14485-342">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="14485-343">Jeśli nie zostanie znaleziony żaden mecz, wybierz wszystkie reguły z pustym dostawcą.</span><span class="sxs-lookup"><span data-stu-id="14485-343">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="14485-344">Z wyniku poprzedniego kroku wybierz reguły z najdłuższym pasującym prefiksem kategorii.</span><span class="sxs-lookup"><span data-stu-id="14485-344">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="14485-345">Jeśli nie zostanie znalezione żadne dopasowanie, wybierz wszystkie reguły, które nie określają kategorii.</span><span class="sxs-lookup"><span data-stu-id="14485-345">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="14485-346">Jeśli zaznaczono wiele reguł, weź **ostatni.**</span><span class="sxs-lookup"><span data-stu-id="14485-346">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="14485-347">Jeśli nie wybrano żadnych reguł, użyj . `MinimumLevel`</span><span class="sxs-lookup"><span data-stu-id="14485-347">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="14485-348">Załóżmy, że na powyższej `ILogger` liście reguł zostanie utworzony obiekt dla kategorii "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="14485-348">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="14485-349">W przypadku dostawcy debugowania obowiązują reguły 1, 6 i 8.</span><span class="sxs-lookup"><span data-stu-id="14485-349">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="14485-350">Reguła 8 jest najbardziej szczegółowa, więc to jest ta wybrana.</span><span class="sxs-lookup"><span data-stu-id="14485-350">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="14485-351">W przypadku dostawcy konsoli obowiązują zasady 3, 4, 5 i 6.</span><span class="sxs-lookup"><span data-stu-id="14485-351">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="14485-352">Zasada 3 jest najbardziej szczegółowa.</span><span class="sxs-lookup"><span data-stu-id="14485-352">Rule 3 is most specific.</span></span>

<span data-ttu-id="14485-353">Wynikowe `ILogger` wystąpienie wysyła `Trace` dzienniki poziomu i powyżej do dostawcy debugowania.</span><span class="sxs-lookup"><span data-stu-id="14485-353">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="14485-354">Dzienniki `Debug` poziomu i powyżej są wysyłane do dostawcy konsoli.</span><span class="sxs-lookup"><span data-stu-id="14485-354">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="14485-355">Aliasy dostawców</span><span class="sxs-lookup"><span data-stu-id="14485-355">Provider aliases</span></span>

<span data-ttu-id="14485-356">Każdy dostawca definiuje *alias,* który może być używany w konfiguracji zamiast w pełni kwalifikowanej nazwy typu.</span><span class="sxs-lookup"><span data-stu-id="14485-356">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="14485-357">W przypadku wbudowanych dostawców użyj następujących aliasów:</span><span class="sxs-lookup"><span data-stu-id="14485-357">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="14485-358">Konsola</span><span class="sxs-lookup"><span data-stu-id="14485-358">Console</span></span>
* <span data-ttu-id="14485-359">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="14485-359">Debug</span></span>
* <span data-ttu-id="14485-360">EventSource</span><span class="sxs-lookup"><span data-stu-id="14485-360">EventSource</span></span>
* <span data-ttu-id="14485-361">Eventlog</span><span class="sxs-lookup"><span data-stu-id="14485-361">EventLog</span></span>
* <span data-ttu-id="14485-362">TraceSource</span><span class="sxs-lookup"><span data-stu-id="14485-362">TraceSource</span></span>
* <span data-ttu-id="14485-363">Plik usługi AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="14485-363">AzureAppServicesFile</span></span>
* <span data-ttu-id="14485-364">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="14485-364">AzureAppServicesBlob</span></span>
* <span data-ttu-id="14485-365">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="14485-365">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="14485-366">Domyślny poziom minimalny</span><span class="sxs-lookup"><span data-stu-id="14485-366">Default minimum level</span></span>

<span data-ttu-id="14485-367">Istnieje minimalne ustawienie poziomu, które staje się skuteczne tylko wtedy, gdy żadne reguły z konfiguracji lub kodu nie mają zastosowania do danego dostawcy i kategorii.</span><span class="sxs-lookup"><span data-stu-id="14485-367">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="14485-368">W poniższym przykładzie pokazano, jak ustawić minimalny poziom:</span><span class="sxs-lookup"><span data-stu-id="14485-368">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="14485-369">Jeśli nie ustawisz jawnie minimalnego poziomu, `Information`wartością `Trace` domyślną jest , co oznacza, że i `Debug` dzienniki są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="14485-369">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="14485-370">Funkcje filtrowania</span><span class="sxs-lookup"><span data-stu-id="14485-370">Filter functions</span></span>

<span data-ttu-id="14485-371">Funkcja filtrowania jest wywoływana dla wszystkich dostawców i kategorii, które nie mają reguł przypisanych do nich przez konfigurację lub kod.</span><span class="sxs-lookup"><span data-stu-id="14485-371">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="14485-372">Kod w funkcji ma dostęp do typu dostawcy, kategorii i poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="14485-372">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="14485-373">Przykład:</span><span class="sxs-lookup"><span data-stu-id="14485-373">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="14485-374">Kategorie i poziomy systemu</span><span class="sxs-lookup"><span data-stu-id="14485-374">System categories and levels</span></span>

<span data-ttu-id="14485-375">Oto kilka kategorii używanych przez ASP.NET Core i Entity Framework Core, z uwagami na temat tego, jakich dzienników można się po nich spodziewać:</span><span class="sxs-lookup"><span data-stu-id="14485-375">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="14485-376">Kategoria</span><span class="sxs-lookup"><span data-stu-id="14485-376">Category</span></span>                            | <span data-ttu-id="14485-377">Uwagi</span><span class="sxs-lookup"><span data-stu-id="14485-377">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="14485-378">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="14485-378">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="14485-379">Diagnostyka ASP.NET podstawowa.</span><span class="sxs-lookup"><span data-stu-id="14485-379">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="14485-380">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="14485-380">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="14485-381">Które klucze zostały uznane, znalezione i używane.</span><span class="sxs-lookup"><span data-stu-id="14485-381">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="14485-382">Filtrowanie pliku Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="14485-382">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="14485-383">Hosty dozwolone.</span><span class="sxs-lookup"><span data-stu-id="14485-383">Hosts allowed.</span></span> |
| <span data-ttu-id="14485-384">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="14485-384">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="14485-385">Jak długo trwa wypełnianie żądań HTTP i o której godzinie rozpoczęto.</span><span class="sxs-lookup"><span data-stu-id="14485-385">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="14485-386">Które zestawy uruchamiania hostingu zostały załadowane.</span><span class="sxs-lookup"><span data-stu-id="14485-386">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="14485-387">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="14485-387">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="14485-388">Diagnostyka MVC i Razor.</span><span class="sxs-lookup"><span data-stu-id="14485-388">MVC and Razor diagnostics.</span></span> <span data-ttu-id="14485-389">Powiązanie modelu, wykonanie filtru, kompilacja widoku, wybór akcji.</span><span class="sxs-lookup"><span data-stu-id="14485-389">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="14485-390">Marszruta Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="14485-390">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="14485-391">Informacje o dopasowaniu trasy.</span><span class="sxs-lookup"><span data-stu-id="14485-391">Route matching information.</span></span> |
| <span data-ttu-id="14485-392">Serwer Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="14485-392">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="14485-393">Połączenie rozpoczyna, zatrzymuje i utrzymuje żywe odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="14485-393">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="14485-394">Informacje o certyfikacie HTTPS.</span><span class="sxs-lookup"><span data-stu-id="14485-394">HTTPS certificate information.</span></span> |
| <span data-ttu-id="14485-395">Plikami Microsoft.AspNetCore.Static</span><span class="sxs-lookup"><span data-stu-id="14485-395">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="14485-396">Pliki obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="14485-396">Files served.</span></span> |
| <span data-ttu-id="14485-397">Microsoft.EntityFrameCore</span><span class="sxs-lookup"><span data-stu-id="14485-397">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="14485-398">Diagnostyka rdzenia podstawowej struktury jednostki ogólne.</span><span class="sxs-lookup"><span data-stu-id="14485-398">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="14485-399">Aktywność i konfiguracja bazy danych, wykrywanie zmian, migracje.</span><span class="sxs-lookup"><span data-stu-id="14485-399">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="14485-400">Zakresy dzienników</span><span class="sxs-lookup"><span data-stu-id="14485-400">Log scopes</span></span>

 <span data-ttu-id="14485-401">*Zakres* można grupować zestaw operacji logicznych.</span><span class="sxs-lookup"><span data-stu-id="14485-401">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="14485-402">To grupowanie może służyć do dołączania tych samych danych do każdego dziennika, który jest tworzony jako część zestawu.</span><span class="sxs-lookup"><span data-stu-id="14485-402">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="14485-403">Na przykład każdy dziennik utworzony w ramach przetwarzania transakcji może zawierać identyfikator transakcji.</span><span class="sxs-lookup"><span data-stu-id="14485-403">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="14485-404">Zakres jest `IDisposable` typem, który jest <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> zwracany przez metodę i trwa do momentu jego usunięcia.</span><span class="sxs-lookup"><span data-stu-id="14485-404">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="14485-405">Użyj zakresu przez zawijania wywołań rejestratora `using` w bloku:</span><span class="sxs-lookup"><span data-stu-id="14485-405">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="14485-406">Poniższy kod umożliwia zakresy dla dostawcy konsoli:</span><span class="sxs-lookup"><span data-stu-id="14485-406">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="14485-407">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="14485-407">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="14485-408">Konfigurowanie `IncludeScopes` opcji rejestratora konsoli jest wymagane, aby włączyć rejestrowanie oparte na zakresie.</span><span class="sxs-lookup"><span data-stu-id="14485-408">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="14485-409">Aby uzyskać informacje na temat konfiguracji, zobacz [sekcję Konfiguracja.](#configuration)</span><span class="sxs-lookup"><span data-stu-id="14485-409">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="14485-410">Każdy komunikat dziennika zawiera informacje o zakresie:</span><span class="sxs-lookup"><span data-stu-id="14485-410">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="14485-411">Wbudowane dostawcy rejestrowania</span><span class="sxs-lookup"><span data-stu-id="14485-411">Built-in logging providers</span></span>

<span data-ttu-id="14485-412">ASP.NET Core wysyła następujących dostawców:</span><span class="sxs-lookup"><span data-stu-id="14485-412">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="14485-413">Konsola</span><span class="sxs-lookup"><span data-stu-id="14485-413">Console</span></span>](#console-provider)
* [<span data-ttu-id="14485-414">Debugowania</span><span class="sxs-lookup"><span data-stu-id="14485-414">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="14485-415">Eventsource</span><span class="sxs-lookup"><span data-stu-id="14485-415">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="14485-416">Eventlog</span><span class="sxs-lookup"><span data-stu-id="14485-416">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="14485-417">TraceSource</span><span class="sxs-lookup"><span data-stu-id="14485-417">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="14485-418">Plik usługi AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="14485-418">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="14485-419">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="14485-419">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="14485-420">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="14485-420">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="14485-421">Aby uzyskać informacje na temat rejestrowania stdout i debugowania za pomocą ASP.NET Core Module, zobacz <xref:test/troubleshoot-azure-iis> i <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="14485-421">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="14485-422">Dostawca konsoli</span><span class="sxs-lookup"><span data-stu-id="14485-422">Console provider</span></span>

<span data-ttu-id="14485-423">Pakiet dostawcy [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) wysyła dane wyjściowe dziennika do konsoli.</span><span class="sxs-lookup"><span data-stu-id="14485-423">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="14485-424">Aby wyświetlić dane wyjściowe rejestrowania konsoli, otwórz wiersz polecenia w folderze projektu i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="14485-424">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="14485-425">Dostawca debugowania</span><span class="sxs-lookup"><span data-stu-id="14485-425">Debug provider</span></span>

<span data-ttu-id="14485-426">Pakiet dostawcy [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje dane wyjściowe dziennika przy użyciu`Debug.WriteLine` klasy [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) ( wywołania metody).</span><span class="sxs-lookup"><span data-stu-id="14485-426">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="14485-427">W systemie Linux ten dostawca zapisuje dzienniki do */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="14485-427">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="14485-428">Dostawca źródła zdarzeń</span><span class="sxs-lookup"><span data-stu-id="14485-428">Event Source provider</span></span>

<span data-ttu-id="14485-429">Pakiet dostawcy [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na międzyplatformie `Microsoft-Extensions-Logging`źródła zdarzeń o nazwie .</span><span class="sxs-lookup"><span data-stu-id="14485-429">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="14485-430">W systemie Windows dostawca używa [etw](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="14485-430">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="14485-431">Dostawca źródła zdarzeń jest dodawany automatycznie po `CreateDefaultBuilder` wywołaniu do utworzenia hosta.</span><span class="sxs-lookup"><span data-stu-id="14485-431">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="14485-432">narzędzia śledzenia dotnet</span><span class="sxs-lookup"><span data-stu-id="14485-432">dotnet trace tooling</span></span>

<span data-ttu-id="14485-433">Narzędzie [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) jest wieloplatformowym narzędziem interfejsu wiersza polecenia, które umożliwia zbieranie śladów rdzenia .NET uruchomionego procesu.</span><span class="sxs-lookup"><span data-stu-id="14485-433">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="14485-434">Narzędzie zbiera <xref:Microsoft.Extensions.Logging.EventSource> dane dostawcy <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>przy użyciu pliku .</span><span class="sxs-lookup"><span data-stu-id="14485-434">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="14485-435">Zainstaluj narzędzia śledzenia dotnet za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="14485-435">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="14485-436">Użyj narzędzia śledzenia dotnet, aby zebrać ślad z aplikacji:</span><span class="sxs-lookup"><span data-stu-id="14485-436">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="14485-437">Jeśli aplikacja nie tworzy hosta `CreateDefaultBuilder`za pomocą , dodaj [dostawcę źródła zdarzeń](#event-source-provider) do konfiguracji rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-437">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="14485-438">Uruchom aplikację za `dotnet run` pomocą polecenia.</span><span class="sxs-lookup"><span data-stu-id="14485-438">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="14485-439">Określ identyfikator procesu (PID) aplikacji .NET Core:</span><span class="sxs-lookup"><span data-stu-id="14485-439">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="14485-440">W systemie Windows należy użyć jednego z następujących podejść:</span><span class="sxs-lookup"><span data-stu-id="14485-440">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="14485-441">Menedżer zadań (Ctrl+Alt+Del)</span><span class="sxs-lookup"><span data-stu-id="14485-441">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="14485-442">polecenie lista zadań</span><span class="sxs-lookup"><span data-stu-id="14485-442">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="14485-443">Polecenie Get-Process Powershell</span><span class="sxs-lookup"><span data-stu-id="14485-443">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="14485-444">W systemie Linux użyj [polecenia pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="14485-444">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="14485-445">Znajdź identyfikator PID dla procesu, który ma taką samą nazwę jak zestaw aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-445">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="14485-446">Wykonaj `dotnet trace` polecenie.</span><span class="sxs-lookup"><span data-stu-id="14485-446">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="14485-447">Składnia polecenia ogólnego:</span><span class="sxs-lookup"><span data-stu-id="14485-447">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="14485-448">W przypadku korzystania z powłoki polecenia `--providers` programu PowerShell`'`należy ująć wartość w pojedyncze cudzysłowy ( ):</span><span class="sxs-lookup"><span data-stu-id="14485-448">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="14485-449">Na platformach innych niż `-f speedscope` Windows dodaj opcję zmiany formatu `speedscope`wyjściowego pliku śledzenia na .</span><span class="sxs-lookup"><span data-stu-id="14485-449">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="14485-450">Słowo kluczowe</span><span class="sxs-lookup"><span data-stu-id="14485-450">Keyword</span></span> | <span data-ttu-id="14485-451">Opis</span><span class="sxs-lookup"><span data-stu-id="14485-451">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="14485-452">1</span><span class="sxs-lookup"><span data-stu-id="14485-452">1</span></span>       | <span data-ttu-id="14485-453">Rejestrowanie zdarzeń `LoggingEventSource`meta dotyczących pliku .</span><span class="sxs-lookup"><span data-stu-id="14485-453">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="14485-454">Nie rejestruje zdarzeń `ILogger`z ).</span><span class="sxs-lookup"><span data-stu-id="14485-454">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="14485-455">2</span><span class="sxs-lookup"><span data-stu-id="14485-455">2</span></span>       | <span data-ttu-id="14485-456">Włącza zdarzenie, `Message` `ILogger.Log()` gdy jest wywoływane.</span><span class="sxs-lookup"><span data-stu-id="14485-456">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="14485-457">Zawiera informacje w sposób programowy (niesformowany).</span><span class="sxs-lookup"><span data-stu-id="14485-457">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="14485-458">4</span><span class="sxs-lookup"><span data-stu-id="14485-458">4</span></span>       | <span data-ttu-id="14485-459">Włącza zdarzenie, `FormatMessage` `ILogger.Log()` gdy jest wywoływane.</span><span class="sxs-lookup"><span data-stu-id="14485-459">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="14485-460">Zawiera sformatowaną wersję ciągu informacji.</span><span class="sxs-lookup"><span data-stu-id="14485-460">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="14485-461">8</span><span class="sxs-lookup"><span data-stu-id="14485-461">8</span></span>       | <span data-ttu-id="14485-462">Włącza zdarzenie, `MessageJson` `ILogger.Log()` gdy jest wywoływane.</span><span class="sxs-lookup"><span data-stu-id="14485-462">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="14485-463">Zapewnia reprezentację JSON argumentów.</span><span class="sxs-lookup"><span data-stu-id="14485-463">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="14485-464">Poziom zdarzenia</span><span class="sxs-lookup"><span data-stu-id="14485-464">Event Level</span></span> | <span data-ttu-id="14485-465">Opis</span><span class="sxs-lookup"><span data-stu-id="14485-465">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="14485-466">0</span><span class="sxs-lookup"><span data-stu-id="14485-466">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="14485-467">1</span><span class="sxs-lookup"><span data-stu-id="14485-467">1</span></span>           | `Critical`      |
   | <span data-ttu-id="14485-468">2</span><span class="sxs-lookup"><span data-stu-id="14485-468">2</span></span>           | `Error`         |
   | <span data-ttu-id="14485-469">3</span><span class="sxs-lookup"><span data-stu-id="14485-469">3</span></span>           | `Warning`       |
   | <span data-ttu-id="14485-470">4</span><span class="sxs-lookup"><span data-stu-id="14485-470">4</span></span>           | `Informational` |
   | <span data-ttu-id="14485-471">5</span><span class="sxs-lookup"><span data-stu-id="14485-471">5</span></span>           | `Verbose`       |

   <span data-ttu-id="14485-472">`FilterSpecs`wpisy `{Logger Category}` dla `{Event Level}` i reprezentują dodatkowe warunki filtrowania dziennika.</span><span class="sxs-lookup"><span data-stu-id="14485-472">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="14485-473">Oddzielne `FilterSpecs` wpisy ze średnikiem`;`( ).</span><span class="sxs-lookup"><span data-stu-id="14485-473">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="14485-474">Przykład użycia powłoki**no** poleceń systemu Windows `--providers` ( brak pojedynczych cudzysłowów wokół wartości):</span><span class="sxs-lookup"><span data-stu-id="14485-474">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="14485-475">Poprzednie polecenie aktywuje:</span><span class="sxs-lookup"><span data-stu-id="14485-475">The preceding command activates:</span></span>

   * <span data-ttu-id="14485-476">Rejestrator źródła zdarzeń do tworzenia sformatowanych ciągów (`4`) dla błędów (`2`).</span><span class="sxs-lookup"><span data-stu-id="14485-476">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="14485-477">`Microsoft.AspNetCore.Hosting`rejestrowania na `Informational` poziomie rejestrowania (`4`).</span><span class="sxs-lookup"><span data-stu-id="14485-477">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="14485-478">Zatrzymaj narzędzie śledzenia dotnetu, naciskając klawisz Enter lub Klawisze Ctrl+C.</span><span class="sxs-lookup"><span data-stu-id="14485-478">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="14485-479">Śledzenie jest zapisywane z nazwą *trace.nettrace* `dotnet trace` w folderze, w którym polecenie jest wykonywane.</span><span class="sxs-lookup"><span data-stu-id="14485-479">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="14485-480">Otwórz śledzenie za pomocą [programu Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="14485-480">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="14485-481">Otwórz plik *trace.nettrace* i eksploruj zdarzenia śledzenia.</span><span class="sxs-lookup"><span data-stu-id="14485-481">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="14485-482">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="14485-482">For more information, see:</span></span>

* <span data-ttu-id="14485-483">[Narzędzie do analizy wydajności śledzenia (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (dokumentacja rdzenia.NET)</span><span class="sxs-lookup"><span data-stu-id="14485-483">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="14485-484">[Narzędzie do analizy wydajności śledzenia (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dokumentacja repozytorium GitHub dotnet/diagnostyka)</span><span class="sxs-lookup"><span data-stu-id="14485-484">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="14485-485">[Klasa LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (przeglądarka interfejsu API.NET)</span><span class="sxs-lookup"><span data-stu-id="14485-485">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="14485-486">[LoggingEventSource źródło odniesienia (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Aby uzyskać źródło referencyjne `release/{Version}`dla `{Version}` innej wersji, zmień gałąź na , gdzie jest żądana wersja ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="14485-486">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="14485-487">[Perfview](#perfview) &ndash; Przydatne do wyświetlania śladów źródła zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="14485-487">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="14485-488">Perfview</span><span class="sxs-lookup"><span data-stu-id="14485-488">Perfview</span></span>

<span data-ttu-id="14485-489">Narzędzie [PerfView](https://github.com/Microsoft/perfview) służy do zbierania i wyświetlania dzienników.</span><span class="sxs-lookup"><span data-stu-id="14485-489">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="14485-490">Istnieją inne narzędzia do przeglądania dzienników ETW, ale PerfView zapewnia najlepsze środowisko do pracy ze zdarzeniami ETW emitowanymi przez ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="14485-490">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="14485-491">Aby skonfigurować Program PerfView do zbierania zdarzeń zarejestrowanych przez tego dostawcę, dodaj ten ciąg `*Microsoft-Extensions-Logging` do listy Dostawcy **dodatkowi.**</span><span class="sxs-lookup"><span data-stu-id="14485-491">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="14485-492">(Nie przegap gwiazdki na początku ciągu.)</span><span class="sxs-lookup"><span data-stu-id="14485-492">(Don't miss the asterisk at the start of the string.)</span></span>

![Dodatkowi dostawcy Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="14485-494">Dostawca usługi Windows EventLog</span><span class="sxs-lookup"><span data-stu-id="14485-494">Windows EventLog provider</span></span>

<span data-ttu-id="14485-495">Pakiet dostawcy [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) wysyła dane wyjściowe dziennika do dziennika zdarzeń systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="14485-495">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="14485-496">[Przeciążenia AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) pozwalają <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>przejść w .</span><span class="sxs-lookup"><span data-stu-id="14485-496">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="14485-497">Jeśli `null` zostanie określony, używane są następujące ustawienia domyślne:</span><span class="sxs-lookup"><span data-stu-id="14485-497">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="14485-498">`LogName`&ndash; "Aplikacja"</span><span class="sxs-lookup"><span data-stu-id="14485-498">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="14485-499">`SourceName`&ndash; ".NET Środowisko uruchomieniowe"</span><span class="sxs-lookup"><span data-stu-id="14485-499">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="14485-500">`MachineName`&ndash; maszyna lokalna</span><span class="sxs-lookup"><span data-stu-id="14485-500">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="14485-501">Zdarzenia są rejestrowane dla [poziomu ostrzeżenia i wyższej](#log-level).</span><span class="sxs-lookup"><span data-stu-id="14485-501">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="14485-502">Aby rejestrować `Warning`zdarzenia niższe niż , jawnie ustawić poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="14485-502">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="14485-503">Na przykład dodaj następujące elementy do pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="14485-503">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="14485-504">Dostawca tracesource</span><span class="sxs-lookup"><span data-stu-id="14485-504">TraceSource provider</span></span>

<span data-ttu-id="14485-505">Pakiet dostawcy [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) używa <xref:System.Diagnostics.TraceSource> bibliotek i dostawców.</span><span class="sxs-lookup"><span data-stu-id="14485-505">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="14485-506">[AddTraceSource przeciążenia](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umożliwiają przekazywanie w przełączniku źródłowym i odbiornik śledzenia.</span><span class="sxs-lookup"><span data-stu-id="14485-506">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="14485-507">Aby korzystać z tego dostawcy, aplikacja musi działać w programie .NET Framework (a nie .NET Core).</span><span class="sxs-lookup"><span data-stu-id="14485-507">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="14485-508">Dostawca może kierować wiadomości do różnych [odbiorników,](/dotnet/framework/debug-trace-profile/trace-listeners)takich jak <xref:System.Diagnostics.TextWriterTraceListener> używane w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-508">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="14485-509">Dostawca usługi aplikacji platformy Azure</span><span class="sxs-lookup"><span data-stu-id="14485-509">Azure App Service provider</span></span>

<span data-ttu-id="14485-510">Pakiet dostawcy [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje dzienniki do plików tekstowych w systemie plików aplikacji usługi Azure App Service i magazynu [obiektów blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) na koncie usługi Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="14485-510">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="14485-511">Pakiet dostawcy nie jest uwzględniony w udostępnionej ramach.</span><span class="sxs-lookup"><span data-stu-id="14485-511">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="14485-512">Aby użyć dostawcy, dodaj pakiet dostawcy do projektu.</span><span class="sxs-lookup"><span data-stu-id="14485-512">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="14485-513">Aby skonfigurować ustawienia <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> dostawcy, należy użyć i <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, jak pokazano w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="14485-513">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="14485-514">Podczas wdrażania w aplikacji usługi App Service aplikacja honoruje ustawienia w sekcji [Dzienniki usługi aplikacji](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stronie **Usługi aplikacji** w witrynie Azure portal.</span><span class="sxs-lookup"><span data-stu-id="14485-514">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="14485-515">Po zaktualizowaniu następujących ustawień zmiany zaczynają obowiązywać natychmiast bez konieczności ponownego uruchamiania lub ponownego wprowadzania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-515">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="14485-516">**Rejestrowanie aplikacji (system plików)**</span><span class="sxs-lookup"><span data-stu-id="14485-516">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="14485-517">**Rejestrowanie aplikacji (obiekt blob)**</span><span class="sxs-lookup"><span data-stu-id="14485-517">**Application Logging (Blob)**</span></span>

<span data-ttu-id="14485-518">Domyślna lokalizacja plików dziennika znajduje się w folderze *D:\\\\home LogFiles\\Application,* a domyślną nazwą pliku jest *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="14485-518">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="14485-519">Domyślny limit rozmiaru pliku wynosi 10 MB, a domyślna maksymalna liczba zachowanych plików to 2.</span><span class="sxs-lookup"><span data-stu-id="14485-519">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="14485-520">Domyślna nazwa obiektu blob to *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="14485-520">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="14485-521">Dostawca działa tylko wtedy, gdy projekt jest uruchamiany w środowisku platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="14485-521">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="14485-522">Nie ma wpływu, gdy projekt&mdash;jest uruchamiany lokalnie nie zapisuje do plików lokalnych lub magazynu lokalnego rozwoju dla obiektów blob.</span><span class="sxs-lookup"><span data-stu-id="14485-522">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="14485-523">Przesyłanie strumieniowe dziennika platformy Azure</span><span class="sxs-lookup"><span data-stu-id="14485-523">Azure log streaming</span></span>

<span data-ttu-id="14485-524">Przesyłanie strumieniowe dziennika platformy Azure umożliwia wyświetlanie aktywności dziennika w czasie rzeczywistym z:</span><span class="sxs-lookup"><span data-stu-id="14485-524">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="14485-525">Serwer aplikacji</span><span class="sxs-lookup"><span data-stu-id="14485-525">The app server</span></span>
* <span data-ttu-id="14485-526">Serwer www</span><span class="sxs-lookup"><span data-stu-id="14485-526">The web server</span></span>
* <span data-ttu-id="14485-527">Śledzenie żądań nie powiodło się</span><span class="sxs-lookup"><span data-stu-id="14485-527">Failed request tracing</span></span>

<span data-ttu-id="14485-528">Aby skonfigurować przesyłanie strumieniowe dziennika platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="14485-528">To configure Azure log streaming:</span></span>

* <span data-ttu-id="14485-529">Przejdź do strony **Dzienniki usługi app service** ze strony portalu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-529">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="14485-530">Ustaw **rejestrowanie aplikacji (system plików)** **na Włączone**.</span><span class="sxs-lookup"><span data-stu-id="14485-530">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="14485-531">Wybierz **poziom**dziennika .</span><span class="sxs-lookup"><span data-stu-id="14485-531">Choose the log **Level**.</span></span> <span data-ttu-id="14485-532">To ustawienie dotyczy tylko przesyłania strumieniowego dziennika platformy Azure, a nie innych dostawców rejestrowania w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-532">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="14485-533">Przejdź do strony **Strumień dziennika,** aby wyświetlić komunikaty aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-533">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="14485-534">Są one rejestrowane przez aplikację `ILogger` za pośrednictwem interfejsu.</span><span class="sxs-lookup"><span data-stu-id="14485-534">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="14485-535">Rejestrowanie śledzenia usługi Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="14485-535">Azure Application Insights trace logging</span></span>

<span data-ttu-id="14485-536">Pakiet dostawcy [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje dzienniki w usłudze Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="14485-536">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="14485-537">Usługa Application Insights to usługa, która monitoruje aplikację sieci web i udostępnia narzędzia do wykonywania zapytań i analizowania danych telemetrycznych.</span><span class="sxs-lookup"><span data-stu-id="14485-537">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="14485-538">Jeśli używasz tego dostawcy, można kwerendy i analizować dzienniki za pomocą narzędzi usługi Application Insights.</span><span class="sxs-lookup"><span data-stu-id="14485-538">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="14485-539">Dostawca rejestrowania jest uwzględniony jako zależność [microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), który jest pakietem, który udostępnia wszystkie dostępne dane telemetryczne dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="14485-539">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="14485-540">Jeśli używasz tego pakietu, nie trzeba instalować pakietu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="14485-540">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="14485-541">Nie należy używać pakietu&mdash; [Microsoft.ApplicationInsights.Web,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) który jest przeznaczony dla ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="14485-541">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="14485-542">Więcej informacji zawierają następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="14485-542">For more information, see the following resources:</span></span>

* [<span data-ttu-id="14485-543">Omówienie usługi Application Insights</span><span class="sxs-lookup"><span data-stu-id="14485-543">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="14485-544">[Usługa Application Insights dla aplikacji ASP.NET Core](/azure/azure-monitor/app/asp-net-core) — uruchom tutaj, jeśli chcesz zaimplementować pełny zakres danych telemetrycznych usługi Application Insights wraz z rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="14485-544">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="14485-545">[ApplicationInsightsLoggerProvider dla dzienników .NET Core ILogger](/azure/azure-monitor/app/ilogger) — uruchom tutaj, jeśli chcesz zaimplementować dostawcę rejestrowania bez pozostałej części telemetrii usługi Application Insights.</span><span class="sxs-lookup"><span data-stu-id="14485-545">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="14485-546">[Karty rejestrowania usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="14485-546">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="14485-547">[Instalowanie, konfigurowanie i inicjowanie zestawu SDK usługi Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) — interaktywny samouczek w witrynie Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="14485-547">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="14485-548">Zewnętrzni dostawcy usług rejestrowania</span><span class="sxs-lookup"><span data-stu-id="14485-548">Third-party logging providers</span></span>

<span data-ttu-id="14485-549">Struktury rejestrowania innych firm, które współpracują z ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="14485-549">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="14485-550">[elmah.io](https://elmah.io/) ([Repozytorium GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="14485-550">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="14485-551">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([Repozytorium GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="14485-551">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="14485-552">[JSNLog](https://jsnlog.com/) ([Repozytorium GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="14485-552">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="14485-553">[KissLog.net](https://kisslog.net/) ([Repozytorium GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="14485-553">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="14485-554">[Log4Net](https://logging.apache.org/log4net/) ([Repozytorium GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="14485-554">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="14485-555">[Loggr](https://loggr.net/) ([Repozytorium GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="14485-555">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="14485-556">[NLog](https://nlog-project.org/) ([Repozytorium GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="14485-556">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="14485-557">[Działko](https://sentry.io/welcome/) [(Repozytorium GitHub)](https://github.com/getsentry/sentry-dotnet)</span><span class="sxs-lookup"><span data-stu-id="14485-557">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="14485-558">[Serilog](https://serilog.net/) ([Repozytorium GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="14485-558">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="14485-559">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="14485-559">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="14485-560">Niektóre struktury innych firm mogą wykonywać [rejestrowanie semantyczne, znane również jako rejestrowanie strukturalne.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)</span><span class="sxs-lookup"><span data-stu-id="14485-560">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="14485-561">Korzystanie z struktury innej firmy jest podobne do korzystania z jednego z wbudowanych dostawców:</span><span class="sxs-lookup"><span data-stu-id="14485-561">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="14485-562">Dodaj pakiet NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="14485-562">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="14485-563">Wywołanie `ILoggerFactory` metody rozszerzenia dostarczone przez strukturę rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14485-563">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="14485-564">Aby uzyskać więcej informacji, zobacz dokumentację każdego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="14485-564">For more information, see each provider's documentation.</span></span> <span data-ttu-id="14485-565">Dostawcy rejestrowania innych firm nie są obsługiwani przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="14485-565">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14485-566">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="14485-566">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="14485-567">Przez [Tom Dykstra](https://github.com/tdykstra) i [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="14485-567">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="14485-568">Program .NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi wbudowanymi i zewnętrznymi dostawcami rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14485-568">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="14485-569">W tym artykule pokazano, jak używać interfejsu API rejestrowania z wbudowanymi dostawcami.</span><span class="sxs-lookup"><span data-stu-id="14485-569">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="14485-570">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="14485-570">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="14485-571">Dodawanie dostawców</span><span class="sxs-lookup"><span data-stu-id="14485-571">Add providers</span></span>

<span data-ttu-id="14485-572">Dostawca rejestrowania wyświetla lub przechowuje dzienniki.</span><span class="sxs-lookup"><span data-stu-id="14485-572">A logging provider displays or stores logs.</span></span> <span data-ttu-id="14485-573">Na przykład dostawca konsoli wyświetla dzienniki na konsoli, a dostawca usługi Azure Application Insights przechowuje je w usłudze Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="14485-573">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="14485-574">Dzienniki mogą być wysyłane do wielu miejsc docelowych, dodając wielu dostawców.</span><span class="sxs-lookup"><span data-stu-id="14485-574">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="14485-575">Aby dodać dostawcę, zadzwoń `Add{provider name}` do metody rozszerzenia dostawcy w *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="14485-575">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="14485-576">Powyższy kod wymaga odwołań do `Microsoft.Extensions.Logging` i `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="14485-576">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="14485-577">Domyślny szablon <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>projektu wywołuje, który dodaje następujących dostawców rejestrowania:</span><span class="sxs-lookup"><span data-stu-id="14485-577">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="14485-578">Konsola</span><span class="sxs-lookup"><span data-stu-id="14485-578">Console</span></span>
* <span data-ttu-id="14485-579">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="14485-579">Debug</span></span>
* <span data-ttu-id="14485-580">EventSource (począwszy od ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="14485-580">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="14485-581">Jeśli używasz, `CreateDefaultBuilder`możesz zastąpić domyślnych dostawców własnymi wyborami.</span><span class="sxs-lookup"><span data-stu-id="14485-581">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="14485-582">Zadzwoń <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>i dodaj dostawców, których chcesz.</span><span class="sxs-lookup"><span data-stu-id="14485-582">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="14485-583">Dowiedz się więcej o [wbudowanych dostawców rejestrowania](#built-in-logging-providers) i [dostawców rejestrowania innych firm](#third-party-logging-providers) w dalszej części artykułu.</span><span class="sxs-lookup"><span data-stu-id="14485-583">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="14485-584">Tworzenie dzienników</span><span class="sxs-lookup"><span data-stu-id="14485-584">Create logs</span></span>

<span data-ttu-id="14485-585">Aby utworzyć dzienniki, <xref:Microsoft.Extensions.Logging.ILogger%601> użyj obiektu.</span><span class="sxs-lookup"><span data-stu-id="14485-585">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="14485-586">W aplikacji sieci web lub usługi `ILogger` hostowanej, uzyskać z iniekcji zależności (DI).</span><span class="sxs-lookup"><span data-stu-id="14485-586">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="14485-587">W aplikacjach konsoli nie-hosta użyj go `LoggerFactory` do utworzenia pliku `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="14485-587">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="14485-588">Poniższy przykład ASP.NET Core tworzy rejestrator `TodoApiSample.Pages.AboutModel` z jako kategorii.</span><span class="sxs-lookup"><span data-stu-id="14485-588">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="14485-589">Kategoria *dziennika* jest ciągiem skojarzonym z każdym dziennikiem.</span><span class="sxs-lookup"><span data-stu-id="14485-589">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="14485-590">Wystąpienie `ILogger<T>` dostarczone przez DI tworzy dzienniki, które `T` mają w pełni kwalifikowaną nazwę typu jako kategorii.</span><span class="sxs-lookup"><span data-stu-id="14485-590">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="14485-591">W poniższych ASP.NET przykłady aplikacji Core i konsoli rejestrator jest używany `Information` do tworzenia dzienników z jako poziom.</span><span class="sxs-lookup"><span data-stu-id="14485-591">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="14485-592">Poziom *dziennika* wskazuje ważność zarejestrowanego zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="14485-592">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="14485-593">[Poziomy](#log-level) i kategorie są [wyjaśnione](#log-category) bardziej szczegółowo w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="14485-593">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="14485-594">Tworzenie dzienników w starcie</span><span class="sxs-lookup"><span data-stu-id="14485-594">Create logs in Startup</span></span>

<span data-ttu-id="14485-595">Aby zapisać dzienniki `Startup` w klasie, dołącz `ILogger` parametr w podpisie konstruktora:</span><span class="sxs-lookup"><span data-stu-id="14485-595">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="14485-596">Tworzenie dzienników w klasie Program</span><span class="sxs-lookup"><span data-stu-id="14485-596">Create logs in the Program class</span></span>

<span data-ttu-id="14485-597">Aby napisać dzienniki `Program` w klasie, pobierz wystąpienie `ILogger` z DI:</span><span class="sxs-lookup"><span data-stu-id="14485-597">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="14485-598">Rejestrowanie podczas budowy hosta nie jest bezpośrednio obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="14485-598">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="14485-599">Można jednak użyć oddzielnego rejestratora.</span><span class="sxs-lookup"><span data-stu-id="14485-599">However, a separate logger can be used.</span></span> <span data-ttu-id="14485-600">W poniższym przykładzie [rejestrator Serilog](https://serilog.net/) jest `CreateWebHostBuilder`używany do logowania .</span><span class="sxs-lookup"><span data-stu-id="14485-600">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="14485-601">`AddSerilog`wykorzystuje konfigurację statyczną `Log.Logger`określoną w:</span><span class="sxs-lookup"><span data-stu-id="14485-601">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="14485-602">Brak metod rejestratora asynchronii</span><span class="sxs-lookup"><span data-stu-id="14485-602">No asynchronous logger methods</span></span>

<span data-ttu-id="14485-603">Rejestrowanie powinno być tak szybkie, że nie jest warte kosztu wydajności kodu asynchroniowego.</span><span class="sxs-lookup"><span data-stu-id="14485-603">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="14485-604">Jeśli magazyn danych rejestrowania jest powolny, nie zapisuj do niego bezpośrednio.</span><span class="sxs-lookup"><span data-stu-id="14485-604">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="14485-605">Należy rozważyć zapisanie komunikatów dziennika do szybkiego magazynu początkowo, a następnie przenieść je do wolnego magazynu później.</span><span class="sxs-lookup"><span data-stu-id="14485-605">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="14485-606">Na przykład jeśli logujesz się do programu SQL Server, nie chcesz tego `Log` robić bezpośrednio `Log` w metodzie, ponieważ metody są synchroniczne.</span><span class="sxs-lookup"><span data-stu-id="14485-606">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="14485-607">Zamiast tego synchronicznie dodać komunikaty dziennika do kolejki w pamięci i mieć proces roboczy w tle wyciągnąć wiadomości z kolejki, aby wykonać asynchronicznie pracy wypychania danych do programu SQL Server.</span><span class="sxs-lookup"><span data-stu-id="14485-607">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="14485-608">Aby uzyskać więcej informacji, zobacz [ten](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problem z githubem.</span><span class="sxs-lookup"><span data-stu-id="14485-608">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="14485-609">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="14485-609">Configuration</span></span>

<span data-ttu-id="14485-610">Konfiguracja dostawcy rejestrowania jest dostarczana przez jednego lub więcej dostawców konfiguracji:</span><span class="sxs-lookup"><span data-stu-id="14485-610">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="14485-611">Formaty plików (INI, JSON i XML).</span><span class="sxs-lookup"><span data-stu-id="14485-611">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="14485-612">Argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="14485-612">Command-line arguments.</span></span>
* <span data-ttu-id="14485-613">Zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="14485-613">Environment variables.</span></span>
* <span data-ttu-id="14485-614">W pamięci obiektów .NET.</span><span class="sxs-lookup"><span data-stu-id="14485-614">In-memory .NET objects.</span></span>
* <span data-ttu-id="14485-615">Niezaszyfrowany magazyn [Menedżera tajnych.](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="14485-615">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="14485-616">Zaszyfrowany magazyn użytkowników, taki jak [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="14485-616">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="14485-617">Dostawcy niestandardowi (zainstalowane lub utworzone).</span><span class="sxs-lookup"><span data-stu-id="14485-617">Custom providers (installed or created).</span></span>

<span data-ttu-id="14485-618">Na przykład konfiguracja rejestrowania jest `Logging` często dostarczana przez sekcję plików ustawień aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-618">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="14485-619">W poniższym przykładzie przedstawiono zawartość typowych *appsettings. Plik Development.json:*</span><span class="sxs-lookup"><span data-stu-id="14485-619">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="14485-620">Właściwość `Logging` może `LogLevel` mieć i rejestrować właściwości dostawcy (konsola jest wyświetlana).</span><span class="sxs-lookup"><span data-stu-id="14485-620">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="14485-621">Właściwość `LogLevel` `Logging` w obszarze określa minimalny [poziom](#log-level) do dziennika dla wybranych kategorii.</span><span class="sxs-lookup"><span data-stu-id="14485-621">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="14485-622">W przykładzie `System` `Microsoft` i kategorii `Information` dziennika na poziomie, `Debug` a wszystkie inne dziennika na poziomie.</span><span class="sxs-lookup"><span data-stu-id="14485-622">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="14485-623">Inne właściwości `Logging` w obszarze określić dostawców rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14485-623">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="14485-624">Przykład jest dla dostawcy konsoli.</span><span class="sxs-lookup"><span data-stu-id="14485-624">The example is for the Console provider.</span></span> <span data-ttu-id="14485-625">Jeśli dostawca obsługuje [zakresy dziennika,](#log-scopes) `IncludeScopes` wskazuje, czy są one włączone.</span><span class="sxs-lookup"><span data-stu-id="14485-625">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="14485-626">Właściwość dostawcy (na przykład `Console` w przykładzie) może również określić `LogLevel` właściwość.</span><span class="sxs-lookup"><span data-stu-id="14485-626">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="14485-627">`LogLevel`pod dostawcą określa poziomy do zalogowania dla tego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="14485-627">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="14485-628">Jeśli poziomy są `Logging.{providername}.LogLevel`określone w , zastępują wszystko ustawione w `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="14485-628">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="14485-629">Interfejs API rejestrowania nie zawiera scenariusza, aby zmienić poziomy dziennika, gdy aplikacja jest uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="14485-629">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="14485-630">Jednak niektórzy dostawcy konfiguracji są w stanie przeładować konfigurację, co ma natychmiastowy wpływ na konfigurację rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14485-630">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="14485-631">Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider), `CreateDefaultBuilder` który jest dodawany przez pliki ustawień odczytu, domyślnie ładuje konfigurację rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14485-631">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="14485-632">Jeśli konfiguracja zostanie zmieniona w kodzie, gdy aplikacja jest uruchomiona, aplikacja może wywołać [IConfigurationRoot.Reload,](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) aby zaktualizować konfigurację rejestrowania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-632">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="14485-633">Aby uzyskać informacje na temat <xref:fundamentals/configuration/index>wdrażania dostawców konfiguracji, zobacz .</span><span class="sxs-lookup"><span data-stu-id="14485-633">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="14485-634">Przykładowe dane wyjściowe rejestrowania</span><span class="sxs-lookup"><span data-stu-id="14485-634">Sample logging output</span></span>

<span data-ttu-id="14485-635">Po wyświetleniu przykładowego kodu w poprzedniej sekcji dzienniki są wyświetlane w konsoli, gdy aplikacja jest uruchamiana z wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="14485-635">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="14485-636">Oto przykład danych wyjściowych konsoli:</span><span class="sxs-lookup"><span data-stu-id="14485-636">Here's an example of console output:</span></span>

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

<span data-ttu-id="14485-637">Poprzednie dzienniki zostały wygenerowane przez wykonanie żądania HTTP `http://localhost:5000/api/todo/0`Get do przykładowej aplikacji o godzinie .</span><span class="sxs-lookup"><span data-stu-id="14485-637">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="14485-638">Oto przykład tych samych dzienników, które pojawiają się w oknie debugowania po uruchomieniu przykładowej aplikacji w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="14485-638">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="14485-639">Dzienniki, które są `ILogger` tworzone przez wywołania pokazane w poprzedniej sekcji zaczynają się od "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="14485-639">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="14485-640">Dzienniki, które zaczynają się od kategorii "Microsoft" pochodzą z ASP.NET kodu frameworka Core.</span><span class="sxs-lookup"><span data-stu-id="14485-640">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="14485-641">ASP.NET Core i kod aplikacji są przy użyciu tego samego interfejsu API rejestrowania i dostawców.</span><span class="sxs-lookup"><span data-stu-id="14485-641">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="14485-642">Poniżej dalsza część artykułu Wyjaśniono niektóre szczegóły i opcje rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14485-642">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="14485-643">Pakiety NuGet</span><span class="sxs-lookup"><span data-stu-id="14485-643">NuGet packages</span></span>

<span data-ttu-id="14485-644">`ILogger` Interfejsy `ILoggerFactory` i interfejsy znajdują się w witrynie [Microsoft.Extensions.Logging.Abstractions,](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a domyślne implementacje dla nich znajdują się w [witrynie Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="14485-644">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="14485-645">Kategoria dziennika</span><span class="sxs-lookup"><span data-stu-id="14485-645">Log category</span></span>

<span data-ttu-id="14485-646">Podczas `ILogger` tworzenia obiektu określana jest dla niego *kategoria.*</span><span class="sxs-lookup"><span data-stu-id="14485-646">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="14485-647">Ta kategoria jest dołączona do każdej wiadomości `ILogger`dziennika utworzonej przez to wystąpienie .</span><span class="sxs-lookup"><span data-stu-id="14485-647">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="14485-648">Kategoria może być dowolny ciąg, ale konwencja jest użycie nazwy klasy, takich jak "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="14485-648">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="14485-649">Użyj, `ILogger<T>` aby `ILogger` uzyskać wystąpienie, które używa `T` w pełni kwalifikowanej nazwy typu jako kategorii:</span><span class="sxs-lookup"><span data-stu-id="14485-649">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="14485-650">Aby jawnie określić kategorię, zadzwoń: `ILoggerFactory.CreateLogger`</span><span class="sxs-lookup"><span data-stu-id="14485-650">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="14485-651">`ILogger<T>`jest odpowiednikiem `CreateLogger` wywołania z w `T`pełni kwalifikowaną nazwą typu .</span><span class="sxs-lookup"><span data-stu-id="14485-651">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="14485-652">Poziom dziennika</span><span class="sxs-lookup"><span data-stu-id="14485-652">Log level</span></span>

<span data-ttu-id="14485-653">Każdy dziennik określa <xref:Microsoft.Extensions.Logging.LogLevel> wartość.</span><span class="sxs-lookup"><span data-stu-id="14485-653">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="14485-654">Poziom dziennika wskazuje ważność lub ważność.</span><span class="sxs-lookup"><span data-stu-id="14485-654">The log level indicates the severity or importance.</span></span> <span data-ttu-id="14485-655">Na przykład można napisać `Information` dziennik, gdy metoda kończy `Warning` się normalnie i dziennika, gdy metoda zwraca kod stanu *404 Nie znaleziono.*</span><span class="sxs-lookup"><span data-stu-id="14485-655">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="14485-656">Następujący kod `Information` tworzy `Warning` i rejestruje:</span><span class="sxs-lookup"><span data-stu-id="14485-656">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="14485-657">W poprzednim kodzie pierwszym parametrem jest [identyfikator zdarzenia Log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="14485-657">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="14485-658">Drugi parametr jest szablonem wiadomości z symbolami zastępczymi dla wartości argumentów dostarczonych przez pozostałe parametry metody.</span><span class="sxs-lookup"><span data-stu-id="14485-658">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="14485-659">Parametry metody są wyjaśnione w [sekcji szablonu wiadomości](#log-message-template) w dalszej części tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="14485-659">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="14485-660">Metody dziennika, które zawierają poziom w nazwie `LogInformation` `LogWarning`metody (na przykład i ) są [metody rozszerzenia dla ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="14485-660">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="14485-661">Te metody `Log` wywołać metodę, która przyjmuje `LogLevel` parametr.</span><span class="sxs-lookup"><span data-stu-id="14485-661">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="14485-662">Można wywołać `Log` metodę bezpośrednio, a nie jedną z tych metod rozszerzenia, ale składnia jest stosunkowo skomplikowana.</span><span class="sxs-lookup"><span data-stu-id="14485-662">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="14485-663">Aby uzyskać więcej <xref:Microsoft.Extensions.Logging.ILogger> informacji, zobacz kod [źródłowy rozszerzeń rejestratora](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="14485-663">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="14485-664">ASP.NET Core definiuje następujące poziomy dziennika, uporządkowane w tym miejscu od najniższej do najwyższej ważności.</span><span class="sxs-lookup"><span data-stu-id="14485-664">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="14485-665">Ślad = 0</span><span class="sxs-lookup"><span data-stu-id="14485-665">Trace = 0</span></span>

  <span data-ttu-id="14485-666">Aby uzyskać informacje, które są zazwyczaj cenne tylko do debugowania.</span><span class="sxs-lookup"><span data-stu-id="14485-666">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="14485-667">Te komunikaty mogą zawierać poufne dane aplikacji, a więc nie powinny być włączone w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="14485-667">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="14485-668">*Domyślnie wyłączone.*</span><span class="sxs-lookup"><span data-stu-id="14485-668">*Disabled by default.*</span></span>

* <span data-ttu-id="14485-669">Debugowanie = 1</span><span class="sxs-lookup"><span data-stu-id="14485-669">Debug = 1</span></span>

  <span data-ttu-id="14485-670">Aby uzyskać informacje, które mogą być przydatne w rozwoju i debugowania.</span><span class="sxs-lookup"><span data-stu-id="14485-670">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="14485-671">Przykład: `Entering method Configure with flag set to true.` `Debug` Włącz logi poziomu w produkcji tylko podczas rozwiązywania problemów, ze względu na dużą liczbę dzienników.</span><span class="sxs-lookup"><span data-stu-id="14485-671">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="14485-672">Informacje = 2</span><span class="sxs-lookup"><span data-stu-id="14485-672">Information = 2</span></span>

  <span data-ttu-id="14485-673">Do śledzenia ogólnego przepływu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-673">For tracking the general flow of the app.</span></span> <span data-ttu-id="14485-674">Te dzienniki zazwyczaj mają pewną wartość długoterminową.</span><span class="sxs-lookup"><span data-stu-id="14485-674">These logs typically have some long-term value.</span></span> <span data-ttu-id="14485-675">Przykład: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="14485-675">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="14485-676">Ostrzeżenie = 3</span><span class="sxs-lookup"><span data-stu-id="14485-676">Warning = 3</span></span>

  <span data-ttu-id="14485-677">W przypadku nieprawidłowych lub nieoczekiwanych zdarzeń w przepływie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-677">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="14485-678">Mogą to być błędy lub inne warunki, które nie powodują zatrzymania aplikacji, ale mogą wymagać zbadania.</span><span class="sxs-lookup"><span data-stu-id="14485-678">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="14485-679">Obsługiwane wyjątki są wspólne miejsce `Warning` do korzystania z poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="14485-679">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="14485-680">Przykład: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="14485-680">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="14485-681">Błąd = 4</span><span class="sxs-lookup"><span data-stu-id="14485-681">Error = 4</span></span>

  <span data-ttu-id="14485-682">Dla błędów i wyjątków, które nie mogą być obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="14485-682">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="14485-683">Te komunikaty wskazują na błąd w bieżącym działaniu lub operacji (takich jak bieżące żądanie HTTP), a nie błąd całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-683">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="14485-684">Przykładowy komunikat dziennika:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="14485-684">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="14485-685">Krytyczny = 5</span><span class="sxs-lookup"><span data-stu-id="14485-685">Critical = 5</span></span>

  <span data-ttu-id="14485-686">W przypadku awarii, które wymagają natychmiastowej uwagi.</span><span class="sxs-lookup"><span data-stu-id="14485-686">For failures that require immediate attention.</span></span> <span data-ttu-id="14485-687">Przykłady: scenariusze utraty danych, brak miejsca na dysku.</span><span class="sxs-lookup"><span data-stu-id="14485-687">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="14485-688">Użyj poziomu dziennika, aby kontrolować, ile danych wyjściowych dziennika jest zapisywany na określonym nośniku pamięci lub oknie wyświetlania.</span><span class="sxs-lookup"><span data-stu-id="14485-688">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="14485-689">Przykład:</span><span class="sxs-lookup"><span data-stu-id="14485-689">For example:</span></span>

* <span data-ttu-id="14485-690">W produkcji:</span><span class="sxs-lookup"><span data-stu-id="14485-690">In production:</span></span>
  * <span data-ttu-id="14485-691">Rejestrowanie na `Trace` `Information` poziomach za pośrednictwem tworzy dużą ilość szczegółowych komunikatów dziennika.</span><span class="sxs-lookup"><span data-stu-id="14485-691">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="14485-692">Aby kontrolować koszty i nie przekraczać `Information` limitów magazynowania danych, rejestruj `Trace` komunikaty poziomu do magazynu danych o dużej objętości i niskich kosztach.</span><span class="sxs-lookup"><span data-stu-id="14485-692">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="14485-693">Rejestrowanie `Warning` na `Critical` poziomach za pośrednictwem zazwyczaj generuje mniej, mniejsze komunikaty dziennika.</span><span class="sxs-lookup"><span data-stu-id="14485-693">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="14485-694">W związku z tym koszty i limity magazynowania zwykle nie są problemem, co skutkuje większą elastycznością wyboru magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="14485-694">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="14485-695">Podczas rozwoju:</span><span class="sxs-lookup"><span data-stu-id="14485-695">During development:</span></span>
  * <span data-ttu-id="14485-696">Zaloguj `Warning` `Critical` się za pośrednictwem wiadomości do konsoli.</span><span class="sxs-lookup"><span data-stu-id="14485-696">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="14485-697">Dodaj `Trace` `Information` za pośrednictwem wiadomości podczas rozwiązywania problemów.</span><span class="sxs-lookup"><span data-stu-id="14485-697">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="14485-698">[Sekcja filtrowania dziennika](#log-filtering) w dalszej części tego artykułu wyjaśnia, jak kontrolować poziomy dziennika, które obsługuje dostawca.</span><span class="sxs-lookup"><span data-stu-id="14485-698">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="14485-699">ASP.NET Core zapisuje dzienniki dla zdarzeń struktury.</span><span class="sxs-lookup"><span data-stu-id="14485-699">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="14485-700">Przykłady dziennika wcześniej w tym artykule wykluczone dzienniki poniżej `Information` poziomu, więc nie `Debug` lub `Trace` poziom dzienniki zostały utworzone.</span><span class="sxs-lookup"><span data-stu-id="14485-700">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="14485-701">Oto przykład dzienników konsoli wyprodukowanych przez uruchomienie przykładowej `Debug` aplikacji skonfigurowanej do pokazywalek dzienników:</span><span class="sxs-lookup"><span data-stu-id="14485-701">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="14485-702">Identyfikator zdarzenia dziennika</span><span class="sxs-lookup"><span data-stu-id="14485-702">Log event ID</span></span>

<span data-ttu-id="14485-703">Każdy dziennik może określić *identyfikator zdarzenia*.</span><span class="sxs-lookup"><span data-stu-id="14485-703">Each log can specify an *event ID*.</span></span> <span data-ttu-id="14485-704">Przykładowa aplikacja robi to przy `LoggingEvents` użyciu klasy zdefiniowanej lokalnie:</span><span class="sxs-lookup"><span data-stu-id="14485-704">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="14485-705">Identyfikator zdarzenia kojarzy zestaw zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="14485-705">An event ID associates a set of events.</span></span> <span data-ttu-id="14485-706">Na przykład wszystkie dzienniki związane z wyświetlaniem listy elementów na stronie może być 1001.</span><span class="sxs-lookup"><span data-stu-id="14485-706">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="14485-707">Dostawca rejestrowania może przechowywać identyfikator zdarzenia w polu identyfikator, w komunikacie rejestrowania lub w ogóle.</span><span class="sxs-lookup"><span data-stu-id="14485-707">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="14485-708">Dostawca debugowania nie pokazuje identyfikatorów zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="14485-708">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="14485-709">Dostawca konsoli wyświetla identyfikatory zdarzeń w nawiasach po kategorii:</span><span class="sxs-lookup"><span data-stu-id="14485-709">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="14485-710">Szablon komunikatu dziennika</span><span class="sxs-lookup"><span data-stu-id="14485-710">Log message template</span></span>

<span data-ttu-id="14485-711">Każdy dziennik określa szablon wiadomości.</span><span class="sxs-lookup"><span data-stu-id="14485-711">Each log specifies a message template.</span></span> <span data-ttu-id="14485-712">Szablon wiadomości może zawierać symbole zastępcze, dla których są dostarczane argumenty.</span><span class="sxs-lookup"><span data-stu-id="14485-712">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="14485-713">Użyj nazw symboli zastępczych, a nie liczb.</span><span class="sxs-lookup"><span data-stu-id="14485-713">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="14485-714">Kolejność symboli zastępczych, a nie ich nazw, określa, które parametry są używane do dostarczania ich wartości.</span><span class="sxs-lookup"><span data-stu-id="14485-714">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="14485-715">W poniższym kodzie należy zauważyć, że nazwy parametrów są niesekwencjonowane w szablonie wiadomości:</span><span class="sxs-lookup"><span data-stu-id="14485-715">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="14485-716">Ten kod tworzy komunikat dziennika z wartościami parametrów w sekwencji:</span><span class="sxs-lookup"><span data-stu-id="14485-716">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="14485-717">Struktura rejestrowania działa w ten sposób, dzięki czemu dostawcy rejestrowania mogą zaimplementować [rejestrowanie semantyczne, znane również jako rejestrowanie strukturalne.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)</span><span class="sxs-lookup"><span data-stu-id="14485-717">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="14485-718">Same argumenty są przekazywane do systemu rejestrowania, a nie tylko do sformatowanego szablonu wiadomości.</span><span class="sxs-lookup"><span data-stu-id="14485-718">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="14485-719">Te informacje umożliwiają dostawcom rejestrowania przechowywanie wartości parametrów jako pól.</span><span class="sxs-lookup"><span data-stu-id="14485-719">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="14485-720">Załóżmy na przykład, że wywołania metody rejestratora wyglądają następująco:</span><span class="sxs-lookup"><span data-stu-id="14485-720">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="14485-721">Jeśli wysyłasz dzienniki do usługi Azure Table Storage, `ID` każda `RequestTime` jednostka tabeli platformy Azure może mieć i właściwości, co upraszcza zapytania dotyczące danych dziennika.</span><span class="sxs-lookup"><span data-stu-id="14485-721">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="14485-722">Kwerenda może znaleźć wszystkie dzienniki w określonym `RequestTime` zakresie bez analizowania czasu poza wiadomością tekstową.</span><span class="sxs-lookup"><span data-stu-id="14485-722">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="14485-723">Rejestrowanie wyjątków</span><span class="sxs-lookup"><span data-stu-id="14485-723">Logging exceptions</span></span>

<span data-ttu-id="14485-724">Metody rejestratora mają przeciążenia, które umożliwiają przekazywanie w wyjątku, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="14485-724">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="14485-725">Różni dostawcy obsługują informacje o wyjątkach na różne sposoby.</span><span class="sxs-lookup"><span data-stu-id="14485-725">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="14485-726">Oto przykład danych wyjściowych dostawcy debugowania z kodu pokazanego powyżej.</span><span class="sxs-lookup"><span data-stu-id="14485-726">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="14485-727">Filtrowanie dzienników</span><span class="sxs-lookup"><span data-stu-id="14485-727">Log filtering</span></span>

<span data-ttu-id="14485-728">Można określić minimalny poziom dziennika dla określonego dostawcy i kategorii lub dla wszystkich dostawców lub wszystkich kategorii.</span><span class="sxs-lookup"><span data-stu-id="14485-728">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="14485-729">Wszelkie dzienniki poniżej minimalnego poziomu nie są przekazywane do tego dostawcy, więc nie są wyświetlane ani przechowywane.</span><span class="sxs-lookup"><span data-stu-id="14485-729">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="14485-730">Aby pominąć wszystkie `LogLevel.None` dzienniki, należy określić jako minimalny poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="14485-730">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="14485-731">Wartość całkowita `LogLevel.None` wynosi 6, która jest `LogLevel.Critical` wyższa niż (5).</span><span class="sxs-lookup"><span data-stu-id="14485-731">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="14485-732">Tworzenie reguł filtrowania w konfiguracji</span><span class="sxs-lookup"><span data-stu-id="14485-732">Create filter rules in configuration</span></span>

<span data-ttu-id="14485-733">Kod szablonu `CreateDefaultBuilder` projektu wywołuje skonfigurowanie rejestrowania dla dostawców konsoli, debugowania i źródła zdarzeń (ASP.NET Core 2.2 lub nowszych).</span><span class="sxs-lookup"><span data-stu-id="14485-733">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="14485-734">Metoda `CreateDefaultBuilder` konfiguruje rejestrowanie w celu `Logging` wyszukywania konfiguracji w sekcji, jak wyjaśniono [wcześniej w tym artykule](#configuration).</span><span class="sxs-lookup"><span data-stu-id="14485-734">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="14485-735">Dane konfiguracyjne określają minimalne poziomy dziennika według dostawcy i kategorii, jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="14485-735">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="14485-736">Ten JSON tworzy sześć reguł filtrowania: jeden dla dostawcy debugowania, cztery dla dostawcy konsoli i jeden dla wszystkich dostawców.</span><span class="sxs-lookup"><span data-stu-id="14485-736">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="14485-737">Pojedyncza reguła jest wybierana `ILogger` dla każdego dostawcy podczas tworzenia obiektu.</span><span class="sxs-lookup"><span data-stu-id="14485-737">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="14485-738">Reguły filtrowania w kodzie</span><span class="sxs-lookup"><span data-stu-id="14485-738">Filter rules in code</span></span>

<span data-ttu-id="14485-739">W poniższym przykładzie pokazano, jak zarejestrować reguły filtrowania w kodzie:</span><span class="sxs-lookup"><span data-stu-id="14485-739">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="14485-740">Drugi `AddFilter` określa dostawcę debugowania przy użyciu jego nazwy typu.</span><span class="sxs-lookup"><span data-stu-id="14485-740">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="14485-741">Pierwszy `AddFilter` dotyczy wszystkich dostawców, ponieważ nie określa typu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="14485-741">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="14485-742">Jak stosowane są reguły filtrowania</span><span class="sxs-lookup"><span data-stu-id="14485-742">How filtering rules are applied</span></span>

<span data-ttu-id="14485-743">Dane konfiguracji i `AddFilter` kod pokazany w poprzednich przykładach tworzą reguły pokazane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="14485-743">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="14485-744">Pierwsze sześć pochodzą z przykładu konfiguracji i dwa ostatnie pochodzą z przykładu kodu.</span><span class="sxs-lookup"><span data-stu-id="14485-744">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="14485-745">Liczba</span><span class="sxs-lookup"><span data-stu-id="14485-745">Number</span></span> | <span data-ttu-id="14485-746">Dostawca</span><span class="sxs-lookup"><span data-stu-id="14485-746">Provider</span></span>      | <span data-ttu-id="14485-747">Kategorie, które zaczynają się od ...</span><span class="sxs-lookup"><span data-stu-id="14485-747">Categories that begin with ...</span></span>          | <span data-ttu-id="14485-748">Minimalny poziom dziennika</span><span class="sxs-lookup"><span data-stu-id="14485-748">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="14485-749">1</span><span class="sxs-lookup"><span data-stu-id="14485-749">1</span></span>      | <span data-ttu-id="14485-750">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="14485-750">Debug</span></span>         | <span data-ttu-id="14485-751">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="14485-751">All categories</span></span>                          | <span data-ttu-id="14485-752">Informacje</span><span class="sxs-lookup"><span data-stu-id="14485-752">Information</span></span>       |
| <span data-ttu-id="14485-753">2</span><span class="sxs-lookup"><span data-stu-id="14485-753">2</span></span>      | <span data-ttu-id="14485-754">Konsola</span><span class="sxs-lookup"><span data-stu-id="14485-754">Console</span></span>       | <span data-ttu-id="14485-755">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="14485-755">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="14485-756">Ostrzeżenie</span><span class="sxs-lookup"><span data-stu-id="14485-756">Warning</span></span>           |
| <span data-ttu-id="14485-757">3</span><span class="sxs-lookup"><span data-stu-id="14485-757">3</span></span>      | <span data-ttu-id="14485-758">Konsola</span><span class="sxs-lookup"><span data-stu-id="14485-758">Console</span></span>       | <span data-ttu-id="14485-759">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="14485-759">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="14485-760">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="14485-760">Debug</span></span>             |
| <span data-ttu-id="14485-761">4</span><span class="sxs-lookup"><span data-stu-id="14485-761">4</span></span>      | <span data-ttu-id="14485-762">Konsola</span><span class="sxs-lookup"><span data-stu-id="14485-762">Console</span></span>       | <span data-ttu-id="14485-763">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="14485-763">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="14485-764">Błąd</span><span class="sxs-lookup"><span data-stu-id="14485-764">Error</span></span>             |
| <span data-ttu-id="14485-765">5</span><span class="sxs-lookup"><span data-stu-id="14485-765">5</span></span>      | <span data-ttu-id="14485-766">Konsola</span><span class="sxs-lookup"><span data-stu-id="14485-766">Console</span></span>       | <span data-ttu-id="14485-767">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="14485-767">All categories</span></span>                          | <span data-ttu-id="14485-768">Informacje</span><span class="sxs-lookup"><span data-stu-id="14485-768">Information</span></span>       |
| <span data-ttu-id="14485-769">6</span><span class="sxs-lookup"><span data-stu-id="14485-769">6</span></span>      | <span data-ttu-id="14485-770">Wszyscy dostawcy</span><span class="sxs-lookup"><span data-stu-id="14485-770">All providers</span></span> | <span data-ttu-id="14485-771">Wszystkie kategorie</span><span class="sxs-lookup"><span data-stu-id="14485-771">All categories</span></span>                          | <span data-ttu-id="14485-772">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="14485-772">Debug</span></span>             |
| <span data-ttu-id="14485-773">7</span><span class="sxs-lookup"><span data-stu-id="14485-773">7</span></span>      | <span data-ttu-id="14485-774">Wszyscy dostawcy</span><span class="sxs-lookup"><span data-stu-id="14485-774">All providers</span></span> | <span data-ttu-id="14485-775">System</span><span class="sxs-lookup"><span data-stu-id="14485-775">System</span></span>                                  | <span data-ttu-id="14485-776">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="14485-776">Debug</span></span>             |
| <span data-ttu-id="14485-777">8</span><span class="sxs-lookup"><span data-stu-id="14485-777">8</span></span>      | <span data-ttu-id="14485-778">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="14485-778">Debug</span></span>         | <span data-ttu-id="14485-779">Microsoft</span><span class="sxs-lookup"><span data-stu-id="14485-779">Microsoft</span></span>                               | <span data-ttu-id="14485-780">Ślad</span><span class="sxs-lookup"><span data-stu-id="14485-780">Trace</span></span>             |

<span data-ttu-id="14485-781">Po `ILogger` utworzeniu `ILoggerFactory` obiektu obiekt wybiera jedną regułę na dostawcę, aby zastosować do tego rejestratora.</span><span class="sxs-lookup"><span data-stu-id="14485-781">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="14485-782">Wszystkie wiadomości napisane `ILogger` przez wystąpienie są filtrowane na podstawie wybranych reguł.</span><span class="sxs-lookup"><span data-stu-id="14485-782">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="14485-783">Najbardziej specyficzna reguła możliwa dla każdego dostawcy i pary kategorii jest wybierana z dostępnych reguł.</span><span class="sxs-lookup"><span data-stu-id="14485-783">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="14485-784">Następujący algorytm jest używany dla `ILogger` każdego dostawcy, gdy jest tworzony dla danej kategorii:</span><span class="sxs-lookup"><span data-stu-id="14485-784">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="14485-785">Wybierz wszystkie reguły, które pasują do dostawcy lub jego aliasu.</span><span class="sxs-lookup"><span data-stu-id="14485-785">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="14485-786">Jeśli nie zostanie znaleziony żaden mecz, wybierz wszystkie reguły z pustym dostawcą.</span><span class="sxs-lookup"><span data-stu-id="14485-786">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="14485-787">Z wyniku poprzedniego kroku wybierz reguły z najdłuższym pasującym prefiksem kategorii.</span><span class="sxs-lookup"><span data-stu-id="14485-787">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="14485-788">Jeśli nie zostanie znalezione żadne dopasowanie, wybierz wszystkie reguły, które nie określają kategorii.</span><span class="sxs-lookup"><span data-stu-id="14485-788">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="14485-789">Jeśli zaznaczono wiele reguł, weź **ostatni.**</span><span class="sxs-lookup"><span data-stu-id="14485-789">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="14485-790">Jeśli nie wybrano żadnych reguł, użyj . `MinimumLevel`</span><span class="sxs-lookup"><span data-stu-id="14485-790">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="14485-791">Załóżmy, że na powyższej `ILogger` liście reguł zostanie utworzony obiekt dla kategorii "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="14485-791">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="14485-792">W przypadku dostawcy debugowania obowiązują reguły 1, 6 i 8.</span><span class="sxs-lookup"><span data-stu-id="14485-792">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="14485-793">Reguła 8 jest najbardziej szczegółowa, więc to jest ta wybrana.</span><span class="sxs-lookup"><span data-stu-id="14485-793">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="14485-794">W przypadku dostawcy konsoli obowiązują zasady 3, 4, 5 i 6.</span><span class="sxs-lookup"><span data-stu-id="14485-794">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="14485-795">Zasada 3 jest najbardziej szczegółowa.</span><span class="sxs-lookup"><span data-stu-id="14485-795">Rule 3 is most specific.</span></span>

<span data-ttu-id="14485-796">Wynikowe `ILogger` wystąpienie wysyła `Trace` dzienniki poziomu i powyżej do dostawcy debugowania.</span><span class="sxs-lookup"><span data-stu-id="14485-796">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="14485-797">Dzienniki `Debug` poziomu i powyżej są wysyłane do dostawcy konsoli.</span><span class="sxs-lookup"><span data-stu-id="14485-797">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="14485-798">Aliasy dostawców</span><span class="sxs-lookup"><span data-stu-id="14485-798">Provider aliases</span></span>

<span data-ttu-id="14485-799">Każdy dostawca definiuje *alias,* który może być używany w konfiguracji zamiast w pełni kwalifikowanej nazwy typu.</span><span class="sxs-lookup"><span data-stu-id="14485-799">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="14485-800">W przypadku wbudowanych dostawców użyj następujących aliasów:</span><span class="sxs-lookup"><span data-stu-id="14485-800">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="14485-801">Konsola</span><span class="sxs-lookup"><span data-stu-id="14485-801">Console</span></span>
* <span data-ttu-id="14485-802">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="14485-802">Debug</span></span>
* <span data-ttu-id="14485-803">EventSource</span><span class="sxs-lookup"><span data-stu-id="14485-803">EventSource</span></span>
* <span data-ttu-id="14485-804">Eventlog</span><span class="sxs-lookup"><span data-stu-id="14485-804">EventLog</span></span>
* <span data-ttu-id="14485-805">TraceSource</span><span class="sxs-lookup"><span data-stu-id="14485-805">TraceSource</span></span>
* <span data-ttu-id="14485-806">Plik usługi AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="14485-806">AzureAppServicesFile</span></span>
* <span data-ttu-id="14485-807">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="14485-807">AzureAppServicesBlob</span></span>
* <span data-ttu-id="14485-808">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="14485-808">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="14485-809">Domyślny poziom minimalny</span><span class="sxs-lookup"><span data-stu-id="14485-809">Default minimum level</span></span>

<span data-ttu-id="14485-810">Istnieje minimalne ustawienie poziomu, które staje się skuteczne tylko wtedy, gdy żadne reguły z konfiguracji lub kodu nie mają zastosowania do danego dostawcy i kategorii.</span><span class="sxs-lookup"><span data-stu-id="14485-810">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="14485-811">W poniższym przykładzie pokazano, jak ustawić minimalny poziom:</span><span class="sxs-lookup"><span data-stu-id="14485-811">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="14485-812">Jeśli nie ustawisz jawnie minimalnego poziomu, `Information`wartością `Trace` domyślną jest , co oznacza, że i `Debug` dzienniki są ignorowane.</span><span class="sxs-lookup"><span data-stu-id="14485-812">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="14485-813">Funkcje filtrowania</span><span class="sxs-lookup"><span data-stu-id="14485-813">Filter functions</span></span>

<span data-ttu-id="14485-814">Funkcja filtrowania jest wywoływana dla wszystkich dostawców i kategorii, które nie mają reguł przypisanych do nich przez konfigurację lub kod.</span><span class="sxs-lookup"><span data-stu-id="14485-814">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="14485-815">Kod w funkcji ma dostęp do typu dostawcy, kategorii i poziomu dziennika.</span><span class="sxs-lookup"><span data-stu-id="14485-815">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="14485-816">Przykład:</span><span class="sxs-lookup"><span data-stu-id="14485-816">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="14485-817">Kategorie i poziomy systemu</span><span class="sxs-lookup"><span data-stu-id="14485-817">System categories and levels</span></span>

<span data-ttu-id="14485-818">Oto kilka kategorii używanych przez ASP.NET Core i Entity Framework Core, z uwagami na temat tego, jakich dzienników można się po nich spodziewać:</span><span class="sxs-lookup"><span data-stu-id="14485-818">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="14485-819">Kategoria</span><span class="sxs-lookup"><span data-stu-id="14485-819">Category</span></span>                            | <span data-ttu-id="14485-820">Uwagi</span><span class="sxs-lookup"><span data-stu-id="14485-820">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="14485-821">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="14485-821">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="14485-822">Diagnostyka ASP.NET podstawowa.</span><span class="sxs-lookup"><span data-stu-id="14485-822">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="14485-823">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="14485-823">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="14485-824">Które klucze zostały uznane, znalezione i używane.</span><span class="sxs-lookup"><span data-stu-id="14485-824">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="14485-825">Filtrowanie pliku Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="14485-825">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="14485-826">Hosty dozwolone.</span><span class="sxs-lookup"><span data-stu-id="14485-826">Hosts allowed.</span></span> |
| <span data-ttu-id="14485-827">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="14485-827">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="14485-828">Jak długo trwa wypełnianie żądań HTTP i o której godzinie rozpoczęto.</span><span class="sxs-lookup"><span data-stu-id="14485-828">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="14485-829">Które zestawy uruchamiania hostingu zostały załadowane.</span><span class="sxs-lookup"><span data-stu-id="14485-829">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="14485-830">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="14485-830">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="14485-831">Diagnostyka MVC i Razor.</span><span class="sxs-lookup"><span data-stu-id="14485-831">MVC and Razor diagnostics.</span></span> <span data-ttu-id="14485-832">Powiązanie modelu, wykonanie filtru, kompilacja widoku, wybór akcji.</span><span class="sxs-lookup"><span data-stu-id="14485-832">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="14485-833">Marszruta Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="14485-833">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="14485-834">Informacje o dopasowaniu trasy.</span><span class="sxs-lookup"><span data-stu-id="14485-834">Route matching information.</span></span> |
| <span data-ttu-id="14485-835">Serwer Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="14485-835">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="14485-836">Połączenie rozpoczyna, zatrzymuje i utrzymuje żywe odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="14485-836">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="14485-837">Informacje o certyfikacie HTTPS.</span><span class="sxs-lookup"><span data-stu-id="14485-837">HTTPS certificate information.</span></span> |
| <span data-ttu-id="14485-838">Plikami Microsoft.AspNetCore.Static</span><span class="sxs-lookup"><span data-stu-id="14485-838">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="14485-839">Pliki obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="14485-839">Files served.</span></span> |
| <span data-ttu-id="14485-840">Microsoft.EntityFrameCore</span><span class="sxs-lookup"><span data-stu-id="14485-840">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="14485-841">Diagnostyka rdzenia podstawowej struktury jednostki ogólne.</span><span class="sxs-lookup"><span data-stu-id="14485-841">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="14485-842">Aktywność i konfiguracja bazy danych, wykrywanie zmian, migracje.</span><span class="sxs-lookup"><span data-stu-id="14485-842">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="14485-843">Zakresy dzienników</span><span class="sxs-lookup"><span data-stu-id="14485-843">Log scopes</span></span>

 <span data-ttu-id="14485-844">*Zakres* można grupować zestaw operacji logicznych.</span><span class="sxs-lookup"><span data-stu-id="14485-844">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="14485-845">To grupowanie może służyć do dołączania tych samych danych do każdego dziennika, który jest tworzony jako część zestawu.</span><span class="sxs-lookup"><span data-stu-id="14485-845">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="14485-846">Na przykład każdy dziennik utworzony w ramach przetwarzania transakcji może zawierać identyfikator transakcji.</span><span class="sxs-lookup"><span data-stu-id="14485-846">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="14485-847">Zakres jest `IDisposable` typem, który jest <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> zwracany przez metodę i trwa do momentu jego usunięcia.</span><span class="sxs-lookup"><span data-stu-id="14485-847">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="14485-848">Użyj zakresu przez zawijania wywołań rejestratora `using` w bloku:</span><span class="sxs-lookup"><span data-stu-id="14485-848">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="14485-849">Poniższy kod umożliwia zakresy dla dostawcy konsoli:</span><span class="sxs-lookup"><span data-stu-id="14485-849">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="14485-850">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="14485-850">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="14485-851">Konfigurowanie `IncludeScopes` opcji rejestratora konsoli jest wymagane, aby włączyć rejestrowanie oparte na zakresie.</span><span class="sxs-lookup"><span data-stu-id="14485-851">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="14485-852">Aby uzyskać informacje na temat konfiguracji, zobacz [sekcję Konfiguracja.](#configuration)</span><span class="sxs-lookup"><span data-stu-id="14485-852">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="14485-853">Każdy komunikat dziennika zawiera informacje o zakresie:</span><span class="sxs-lookup"><span data-stu-id="14485-853">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="14485-854">Wbudowane dostawcy rejestrowania</span><span class="sxs-lookup"><span data-stu-id="14485-854">Built-in logging providers</span></span>

<span data-ttu-id="14485-855">ASP.NET Core wysyła następujących dostawców:</span><span class="sxs-lookup"><span data-stu-id="14485-855">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="14485-856">Konsola</span><span class="sxs-lookup"><span data-stu-id="14485-856">Console</span></span>](#console-provider)
* [<span data-ttu-id="14485-857">Debugowania</span><span class="sxs-lookup"><span data-stu-id="14485-857">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="14485-858">Eventsource</span><span class="sxs-lookup"><span data-stu-id="14485-858">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="14485-859">Eventlog</span><span class="sxs-lookup"><span data-stu-id="14485-859">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="14485-860">TraceSource</span><span class="sxs-lookup"><span data-stu-id="14485-860">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="14485-861">Plik usługi AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="14485-861">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="14485-862">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="14485-862">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="14485-863">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="14485-863">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="14485-864">Aby uzyskać informacje na temat rejestrowania stdout i debugowania za pomocą ASP.NET Core Module, zobacz <xref:test/troubleshoot-azure-iis> i <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="14485-864">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="14485-865">Dostawca konsoli</span><span class="sxs-lookup"><span data-stu-id="14485-865">Console provider</span></span>

<span data-ttu-id="14485-866">Pakiet dostawcy [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) wysyła dane wyjściowe dziennika do konsoli.</span><span class="sxs-lookup"><span data-stu-id="14485-866">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="14485-867">Aby wyświetlić dane wyjściowe rejestrowania konsoli, otwórz wiersz polecenia w folderze projektu i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="14485-867">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="14485-868">Dostawca debugowania</span><span class="sxs-lookup"><span data-stu-id="14485-868">Debug provider</span></span>

<span data-ttu-id="14485-869">Pakiet dostawcy [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje dane wyjściowe dziennika przy użyciu`Debug.WriteLine` klasy [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) ( wywołania metody).</span><span class="sxs-lookup"><span data-stu-id="14485-869">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="14485-870">W systemie Linux ten dostawca zapisuje dzienniki do */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="14485-870">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="14485-871">Dostawca źródła zdarzeń</span><span class="sxs-lookup"><span data-stu-id="14485-871">Event Source provider</span></span>

<span data-ttu-id="14485-872">Pakiet dostawcy [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na międzyplatformie `Microsoft-Extensions-Logging`źródła zdarzeń o nazwie .</span><span class="sxs-lookup"><span data-stu-id="14485-872">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="14485-873">W systemie Windows dostawca używa [etw](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="14485-873">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="14485-874">Dostawca źródła zdarzeń jest dodawany automatycznie po `CreateDefaultBuilder` wywołaniu do utworzenia hosta.</span><span class="sxs-lookup"><span data-stu-id="14485-874">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="14485-875">Narzędzie [PerfView](https://github.com/Microsoft/perfview) służy do zbierania i wyświetlania dzienników.</span><span class="sxs-lookup"><span data-stu-id="14485-875">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="14485-876">Istnieją inne narzędzia do przeglądania dzienników ETW, ale PerfView zapewnia najlepsze środowisko do pracy ze zdarzeniami ETW emitowanymi przez ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="14485-876">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="14485-877">Aby skonfigurować Program PerfView do zbierania zdarzeń zarejestrowanych przez tego dostawcę, dodaj ten ciąg `*Microsoft-Extensions-Logging` do listy Dostawcy **dodatkowi.**</span><span class="sxs-lookup"><span data-stu-id="14485-877">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="14485-878">(Nie przegap gwiazdki na początku ciągu.)</span><span class="sxs-lookup"><span data-stu-id="14485-878">(Don't miss the asterisk at the start of the string.)</span></span>

![Dodatkowi dostawcy Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="14485-880">Dostawca usługi Windows EventLog</span><span class="sxs-lookup"><span data-stu-id="14485-880">Windows EventLog provider</span></span>

<span data-ttu-id="14485-881">Pakiet dostawcy [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) wysyła dane wyjściowe dziennika do dziennika zdarzeń systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="14485-881">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="14485-882">[Przeciążenia AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) pozwalają <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>przejść w .</span><span class="sxs-lookup"><span data-stu-id="14485-882">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="14485-883">Jeśli `null` zostanie określony, używane są następujące ustawienia domyślne:</span><span class="sxs-lookup"><span data-stu-id="14485-883">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="14485-884">`LogName`&ndash; "Aplikacja"</span><span class="sxs-lookup"><span data-stu-id="14485-884">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="14485-885">`SourceName`&ndash; ".NET Środowisko uruchomieniowe"</span><span class="sxs-lookup"><span data-stu-id="14485-885">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="14485-886">`MachineName`&ndash; maszyna lokalna</span><span class="sxs-lookup"><span data-stu-id="14485-886">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="14485-887">Zdarzenia są rejestrowane dla [poziomu ostrzeżenia i wyższej](#log-level).</span><span class="sxs-lookup"><span data-stu-id="14485-887">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="14485-888">Aby rejestrować `Warning`zdarzenia niższe niż , jawnie ustawić poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="14485-888">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="14485-889">Na przykład dodaj następujące elementy do pliku *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="14485-889">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="14485-890">Dostawca tracesource</span><span class="sxs-lookup"><span data-stu-id="14485-890">TraceSource provider</span></span>

<span data-ttu-id="14485-891">Pakiet dostawcy [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) używa <xref:System.Diagnostics.TraceSource> bibliotek i dostawców.</span><span class="sxs-lookup"><span data-stu-id="14485-891">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="14485-892">[AddTraceSource przeciążenia](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umożliwiają przekazywanie w przełączniku źródłowym i odbiornik śledzenia.</span><span class="sxs-lookup"><span data-stu-id="14485-892">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="14485-893">Aby korzystać z tego dostawcy, aplikacja musi działać w programie .NET Framework (a nie .NET Core).</span><span class="sxs-lookup"><span data-stu-id="14485-893">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="14485-894">Dostawca może kierować wiadomości do różnych [odbiorników,](/dotnet/framework/debug-trace-profile/trace-listeners)takich jak <xref:System.Diagnostics.TextWriterTraceListener> używane w przykładowej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-894">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="14485-895">Dostawca usługi aplikacji platformy Azure</span><span class="sxs-lookup"><span data-stu-id="14485-895">Azure App Service provider</span></span>

<span data-ttu-id="14485-896">Pakiet dostawcy [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje dzienniki do plików tekstowych w systemie plików aplikacji usługi Azure App Service i magazynu [obiektów blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) na koncie usługi Azure Storage.</span><span class="sxs-lookup"><span data-stu-id="14485-896">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="14485-897">Pakiet dostawcy nie jest uwzględniony w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="14485-897">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="14485-898">Podczas kierowania .NET Framework lub `Microsoft.AspNetCore.App` odwoływania się do metapakiet, dodaj pakiet dostawcy do projektu.</span><span class="sxs-lookup"><span data-stu-id="14485-898">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="14485-899">Przeciążenie <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> pozwala przejść <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>w .</span><span class="sxs-lookup"><span data-stu-id="14485-899">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="14485-900">Obiekt ustawień może zastąpić ustawienia domyślne, takie jak szablon danych wyjściowych rejestrowania, nazwa obiektu blob i limit rozmiaru pliku.</span><span class="sxs-lookup"><span data-stu-id="14485-900">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="14485-901">(Szablon*wyjściowy* to szablon wiadomości, który jest stosowany do wszystkich dzienników `ILogger` oprócz tego, co jest dostarczane z wywołaniem metody.)</span><span class="sxs-lookup"><span data-stu-id="14485-901">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="14485-902">Podczas wdrażania w aplikacji usługi App Service aplikacja honoruje ustawienia w sekcji [Dzienniki usługi aplikacji](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stronie **Usługi aplikacji** w witrynie Azure portal.</span><span class="sxs-lookup"><span data-stu-id="14485-902">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="14485-903">Po zaktualizowaniu następujących ustawień zmiany zaczynają obowiązywać natychmiast bez konieczności ponownego uruchamiania lub ponownego wprowadzania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-903">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="14485-904">**Rejestrowanie aplikacji (system plików)**</span><span class="sxs-lookup"><span data-stu-id="14485-904">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="14485-905">**Rejestrowanie aplikacji (obiekt blob)**</span><span class="sxs-lookup"><span data-stu-id="14485-905">**Application Logging (Blob)**</span></span>

<span data-ttu-id="14485-906">Domyślna lokalizacja plików dziennika znajduje się w folderze *D:\\\\home LogFiles\\Application,* a domyślną nazwą pliku jest *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="14485-906">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="14485-907">Domyślny limit rozmiaru pliku wynosi 10 MB, a domyślna maksymalna liczba zachowanych plików to 2.</span><span class="sxs-lookup"><span data-stu-id="14485-907">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="14485-908">Domyślna nazwa obiektu blob to *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="14485-908">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="14485-909">Dostawca działa tylko wtedy, gdy projekt jest uruchamiany w środowisku platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="14485-909">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="14485-910">Nie ma wpływu, gdy projekt&mdash;jest uruchamiany lokalnie nie zapisuje do plików lokalnych lub magazynu lokalnego rozwoju dla obiektów blob.</span><span class="sxs-lookup"><span data-stu-id="14485-910">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="14485-911">Przesyłanie strumieniowe dziennika platformy Azure</span><span class="sxs-lookup"><span data-stu-id="14485-911">Azure log streaming</span></span>

<span data-ttu-id="14485-912">Przesyłanie strumieniowe dziennika platformy Azure umożliwia wyświetlanie aktywności dziennika w czasie rzeczywistym z:</span><span class="sxs-lookup"><span data-stu-id="14485-912">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="14485-913">Serwer aplikacji</span><span class="sxs-lookup"><span data-stu-id="14485-913">The app server</span></span>
* <span data-ttu-id="14485-914">Serwer www</span><span class="sxs-lookup"><span data-stu-id="14485-914">The web server</span></span>
* <span data-ttu-id="14485-915">Śledzenie żądań nie powiodło się</span><span class="sxs-lookup"><span data-stu-id="14485-915">Failed request tracing</span></span>

<span data-ttu-id="14485-916">Aby skonfigurować przesyłanie strumieniowe dziennika platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="14485-916">To configure Azure log streaming:</span></span>

* <span data-ttu-id="14485-917">Przejdź do strony **Dzienniki usługi app service** ze strony portalu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-917">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="14485-918">Ustaw **rejestrowanie aplikacji (system plików)** **na Włączone**.</span><span class="sxs-lookup"><span data-stu-id="14485-918">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="14485-919">Wybierz **poziom**dziennika .</span><span class="sxs-lookup"><span data-stu-id="14485-919">Choose the log **Level**.</span></span> <span data-ttu-id="14485-920">To ustawienie dotyczy tylko przesyłania strumieniowego dziennika platformy Azure, a nie innych dostawców rejestrowania w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-920">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="14485-921">Przejdź do strony **Strumień dziennika,** aby wyświetlić komunikaty aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14485-921">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="14485-922">Są one rejestrowane przez aplikację `ILogger` za pośrednictwem interfejsu.</span><span class="sxs-lookup"><span data-stu-id="14485-922">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="14485-923">Rejestrowanie śledzenia usługi Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="14485-923">Azure Application Insights trace logging</span></span>

<span data-ttu-id="14485-924">Pakiet dostawcy [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje dzienniki w usłudze Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="14485-924">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="14485-925">Usługa Application Insights to usługa, która monitoruje aplikację sieci web i udostępnia narzędzia do wykonywania zapytań i analizowania danych telemetrycznych.</span><span class="sxs-lookup"><span data-stu-id="14485-925">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="14485-926">Jeśli używasz tego dostawcy, można kwerendy i analizować dzienniki za pomocą narzędzi usługi Application Insights.</span><span class="sxs-lookup"><span data-stu-id="14485-926">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="14485-927">Dostawca rejestrowania jest uwzględniony jako zależność [microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), który jest pakietem, który udostępnia wszystkie dostępne dane telemetryczne dla ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="14485-927">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="14485-928">Jeśli używasz tego pakietu, nie trzeba instalować pakietu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="14485-928">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="14485-929">Nie należy używać pakietu&mdash; [Microsoft.ApplicationInsights.Web,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) który jest przeznaczony dla ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="14485-929">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="14485-930">Więcej informacji zawierają następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="14485-930">For more information, see the following resources:</span></span>

* [<span data-ttu-id="14485-931">Omówienie usługi Application Insights</span><span class="sxs-lookup"><span data-stu-id="14485-931">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="14485-932">[Usługa Application Insights dla aplikacji ASP.NET Core](/azure/azure-monitor/app/asp-net-core) — uruchom tutaj, jeśli chcesz zaimplementować pełny zakres danych telemetrycznych usługi Application Insights wraz z rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="14485-932">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="14485-933">[ApplicationInsightsLoggerProvider dla dzienników .NET Core ILogger](/azure/azure-monitor/app/ilogger) — uruchom tutaj, jeśli chcesz zaimplementować dostawcę rejestrowania bez pozostałej części telemetrii usługi Application Insights.</span><span class="sxs-lookup"><span data-stu-id="14485-933">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="14485-934">[Karty rejestrowania usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="14485-934">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="14485-935">[Instalowanie, konfigurowanie i inicjowanie zestawu SDK usługi Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) — interaktywny samouczek w witrynie Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="14485-935">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="14485-936">Zewnętrzni dostawcy usług rejestrowania</span><span class="sxs-lookup"><span data-stu-id="14485-936">Third-party logging providers</span></span>

<span data-ttu-id="14485-937">Struktury rejestrowania innych firm, które współpracują z ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="14485-937">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="14485-938">[elmah.io](https://elmah.io/) ([Repozytorium GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="14485-938">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="14485-939">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([Repozytorium GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="14485-939">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="14485-940">[JSNLog](https://jsnlog.com/) ([Repozytorium GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="14485-940">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="14485-941">[KissLog.net](https://kisslog.net/) ([Repozytorium GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="14485-941">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="14485-942">[Log4Net](https://logging.apache.org/log4net/) ([Repozytorium GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="14485-942">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="14485-943">[Loggr](https://loggr.net/) ([Repozytorium GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="14485-943">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="14485-944">[NLog](https://nlog-project.org/) ([Repozytorium GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="14485-944">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="14485-945">[Działko](https://sentry.io/welcome/) [(Repozytorium GitHub)](https://github.com/getsentry/sentry-dotnet)</span><span class="sxs-lookup"><span data-stu-id="14485-945">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="14485-946">[Serilog](https://serilog.net/) ([Repozytorium GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="14485-946">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="14485-947">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="14485-947">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="14485-948">Niektóre struktury innych firm mogą wykonywać [rejestrowanie semantyczne, znane również jako rejestrowanie strukturalne.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)</span><span class="sxs-lookup"><span data-stu-id="14485-948">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="14485-949">Korzystanie z struktury innej firmy jest podobne do korzystania z jednego z wbudowanych dostawców:</span><span class="sxs-lookup"><span data-stu-id="14485-949">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="14485-950">Dodaj pakiet NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="14485-950">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="14485-951">Wywołanie `ILoggerFactory` metody rozszerzenia dostarczone przez strukturę rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14485-951">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="14485-952">Aby uzyskać więcej informacji, zobacz dokumentację każdego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="14485-952">For more information, see each provider's documentation.</span></span> <span data-ttu-id="14485-953">Dostawcy rejestrowania innych firm nie są obsługiwani przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="14485-953">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14485-954">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="14485-954">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
